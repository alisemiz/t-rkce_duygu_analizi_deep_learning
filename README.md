Hepsiburada Ürün Yorumları: Türkçe Duygu Analizi 🇹🇷
Bu proje, e-ticaret platformu Hepsiburada'daki müşteri yorumlarından yola çıkarak Türkçe metinler üzerinde Duygu Analizi (Sentiment Analysis) yapmayı amaçlamaktadır. Proje kapsamında, doğal dil işleme (NLP) teknikleri ve önceden eğitilmiş BERTurk dil modeli kullanılarak yorumlar Olumsuz, Nötr veya Olumlu olmak üzere 3 farklı sınıfa ayrılmaktadır.
Not: Projenin detaylı metodolojisi, veri analiz grafikleri ve derinlemesine hata analizi için depoda yer alan `Duygu_Analizi_Proje_Raporu.docx` dosyasına başvurabilirsiniz.
📌 Problem Tanımı
Yüz binlerce müşteri yorumunun manuel olarak incelenmesi pratik değildir. Bu model, serbest formatta yazılmış Türkçe ürün yorumlarını analiz ederek ürün/hizmet memnuniyetinin otomatik, hızlı ve ölçeklenebilir bir şekilde izlenmesine olanak tanır. Etiketler, kullanıcıların verdiği yıldız puanlarından (1-5) türetilmiştir.
📊 Veri Seti
Kullanılan veri seti Hepsiburada ürün yorumlarını içermektedir (`hb.csv`).
Boyut: Tekilleştirme öncesi 272.216, tekilleştirme sonrası 235.179 yorum.
Sınıflandırma Mantığı:
1 ve 2 Yıldız ➡️ Olumsuz (Sınıf 0)
3 Yıldız ➡️ Nötr (Sınıf 1)
4 ve 5 Yıldız ➡️ Olumlu (Sınıf 2)
Sınıf Dağılımı: Ciddi bir dengesizlik mevcuttur. Verinin %84.1'i Olumlu, %10.8'i Nötr ve %5.1'i Olumsuz yorumlardan oluşmaktadır.
⚙️ Metodoloji ve Ön İşleme
Veri Temizliği: URL'ler silindi, tekrarlayan harfler normalize edildi ("çoooook" -> "çook"), boşluklar düzenlendi. BERT'in kendi tokenizer'ı kullanıldığı için büyük/küçük harf ve noktalama işaretleri korundu.
Dengesizlik Yönetimi: Eğitim seti için en büyük sınıf, en küçük sınıfın maksimum 5 katı olacak şekilde alt-örnekleme (undersampling) uygulandı ve kayıp (loss) fonksiyonunda sınıf ağırlıkları kullanıldı.
Gürültü Temizliği (Label Noise): Yıldız puanı ile metin içeriğinin uyuşmadığı şüpheli veriler (örn: "çok iyi ürün" yazıp 1 yıldız verenler) eğitim setinden çıkarıldı.
🧠 Modeller
Projede iki farklı model kıyaslanmıştır:
Temel Model (Baseline): TF-IDF (kelime ve karakter n-gram) özellikleri çıkarılarak SGDClassifier ile eğitilmiştir.
Derin Öğrenme Modeli: Önceden eğitilmiş Türkçe BERT modeli (`dbmdz/bert-base-turkish-128k-cased`) üzerine sınıflandırma katmanı eklenerek fine-tuning işlemi yapılmıştır.
Nihai Karar Mekanizması: En yüksek başarıyı elde etmek için BERT ve TF-IDF olasılıkları harmanlanmış ve sınıf dengesizliğine karşı eşik kaydırması (threshold bias) uygulanmıştır.
📈 Sonuçlar ve Performans
Sınıf dengesizliği nedeniyle temel metrik olarak Macro-F1 skoru baz alınmıştır. Aşağıdaki sonuçlar, eğitimde hiç görülmemiş doğal dağılımlı test setinde (23.518 yorum) elde edilmiştir:
Model	Accuracy	Macro Precision	Macro Recall	Macro F1
TF-IDF + SGD (Baseline)	0.7848	0.5469	0.6251	0.5755
BERTurk (Argmax)	0.8218	0.6076	0.6670	0.6321
BERTurk + TF-IDF Harmanı (Nihai)	0.8507	0.6521	0.6337	0.6422
Nihai Model Sınıf Bazlı F1 Skorları: Olumsuz: 0.63, Nötr: 0.36, Olumlu: 0.92
🔍 Hata Analizi Özeti
Model en çok Nötr (3 yıldız) sınıfını tahmin etmekte zorlanmaktadır. Analizler, 3 yıldız verilen yorumların metinlerinin aslında belirgin şekilde olumlu veya olumsuz kelimeler içerdiğini göstermektedir (Etiket gürültüsü).
"Ürün gayet iyi ama kargo çok geç geldi" gibi karışık duygulu (mixed sentiment) yorumlarda model haklı olarak nötr ve olumlu sınıflar arasında kalabilmektedir.
🚀 Kurulum ve Kullanım
(Bu bölümü kendi GitHub/Colab yapınıza göre düzenleyiniz)
Repoyu klonlayın:
```bash
   git clone https://github.com/kullaniciadi/hepsiburada-duygu-analizi.git
   ```
Gerekli kütüphaneleri yükleyin:
```bash
   pip install -r requirements.txt
   ```
Modeli çalıştırmak için `main.ipynb` veya `train.py` dosyasını kullanabilirsiniz.
💡 Gelecek Geliştirmeler
Nötr sınıfı (3 yıldız) için etiket kalitesinin manuel veya kurallı yollarla artırılması.
Yıldız puanını sıralı (ordinal) bir hedef olarak ele alan regresyon tabanlı yaklaşımların denenmesi.
Embedding + LSTM mimarisi ile BERT modelinin performans kıyaslamasının yapılması.
---
Hazırlayan: Ali Semiz
