# Alman Trafik İşaretleri Tanıma Sistemi (GTSRB)

Bu proje, **TensorFlow** ve **Keras** kütüphaneleri kullanılarak geliştirilmiş, Alman Trafik İşareti Tanıma Benchmark (GTSRB) veri setindeki trafik işaretlerini yüksek doğrulukla sınıflandırabilen bir Derin Öğrenme (Deep Learning) projesidir.

Proje bünyesinde Evrişimli Sinir Ağları (CNN) mimarisi kurulmuş, veri setindeki dengesizlikleri ve aşırı öğrenmeyi (overfitting) önlemek amacıyla Veri Artırımı (Data Augmentation) ve Erken Durdurma (Early Stopping) gibi teknikler entegre edilmiştir.

## Veri Seti Hakkında

Projede kullanılan **German Traffic Sign Recognition Benchmark (GTSRB)** veri seti, Kaggle API aracılığıyla otomatik olarak indirilmektedir.

* **Toplam Sınıf Sayısı:** 43 farklı trafik işareti
* **Eğitim Görseli Sayısı:** 39,209
* **Test Görseli Sayısı:** 12,630
* **Görsel Boyutları:** Minimum 25x25 ile Maksimum 243x225 piksel arasında değişen, ortalama 50x50 piksel boyutlarında renkli görüntüler.

## Kullanılan Teknolojiler ve Kütüphaneler

* **Dil:** Python
* **Derin Öğrenme:** TensorFlow / Keras
* **Veri Analizi & Manipülasyon:** Pandas, NumPy
* **Veri Görselleştirme:** Matplotlib, Seaborn
* **Görsel İşleme:** PIL (Python Imaging Library)
* **Model Değerlendirme:** Scikit-learn (Confusion Matrix, Classification Report)

## Model Mimarisi

Model, görüntü özelliklerini yakalamak için ardışık olarak dizilmiş Evrişim (Convolutional) ve Ortaklama (MaxPooling) katmanlarından oluşur:

1.  **Conv2D Katmanları:** Görsellerdeki kenar, köşe ve şekil gibi öznitelikleri yakalar.
2.  **BatchNormalization:** Eğitimi hızlandırır ve kararlı hale getirir.
3.  **MaxPooling2D:** Boyutsal azaltma yaparak hesaplama yükünü düşürür.
4.  **Dropout:** Belirli oranda nöronu rastgele kapatarak aşırı öğrenmeyi (overfitting) engeller.
5.  **Dense (Tam Bağlantılı) Katmanlar:** Sınıflandırma kararını verir (Son katmanda 43 sınıf için `softmax` aktivasyonu kullanılmıştır).

### Gelişmiş Eğitim Yöntemleri
* **ImageDataGenerator:** Eğitim sırasında görselleri rastgele döndürerek, kaydırarak ve yakınlaştırarak veri çeşitliliği artırılmıştır.
* **EarlyStopping:** Doğrulama kaybı (val_loss) iyileşmeyi bıraktığında eğitimi otomatik olarak sonlandırır.
* **ReduceLROnPlateau:** Eğitim tıkandığında öğrenme oranını (learning rate) otomatik olarak düşürür.
* **ModelCheckpoint:** En iyi ağırlıklara sahip modeli `.h5` formatında diske kaydeder.

## Sonuçlar ve Model Performansı

### Veri Seti Dağılım Analizi
Veri seti üzerinde yapılan ilk analizlerde 43 sınıf arasında ciddi bir dengesizlik (Imbalance) olduğu tespit edilmiştir:

* **En çok örnek içeren sınıf:** Sınıf 2 (Hız Sınırı 50km/s) -> 2250 görüntü.
* **En az örnek içeren sınıf:** Sınıf 0 (Hız Sınırı 20km/s) -> 210 görüntü.
* **Dengesizlik Oranı:** En çok ve en az örnek barındıran sınıflar arasında 10.7 kat fark bulunmaktadır.

Bu dengesizliğin modelin az bulunan sınıfları öğrenmesini zorlaştırmaması adına, eğitim sürecinde ImageDataGenerator ile veri artırımı (Data Augmentation) teknikleri uygulanmıştır.

### Eğitim ve Test Başarısı
Model 43 sınıfın tamamında kararlı bir öğrenme grafiği çizmiştir. EarlyStopping ve ReduceLROnPlateau algoritmaları sayesinde aşırı öğrenmeye (overfitting) düşmeden eğitim en optimize noktada tamamlanmıştır.

* **Eğitim Doğruluğu (Training Accuracy):** %99.84
* **Doğrulama Doğruluğu (Validation Accuracy):** %99.97
* **Test Doğruluğu (Test Accuracy):** %99.26
* **Test Kaybı (Test Loss):** %02.37

### Detaylı Değerlendirme (Evaluation)
Modelin başarısını sadece genel doğruluk (accuracy) üzerinden değil, sınıflar bazında da ölçmek için scikit-learn kütüphanesinden yararlanılmıştır:

**Sınıflandırma Raporu (Classification Report):** Her bir trafik işareti için Precision (Kesinlik), Recall (Duyarlılık) ve F1-Score değerleri hesaplanmıştır. Veri setindeki 10.7 katlık dengesizliğe rağmen, az örnekli sınıflarda (Örn: Sınıf 0) bile yüksek F1-Skorları elde edilmiştir.

**Karışıklık Matrisi (Confusion Matrix):** Test setindeki 12,630 görsel üzerinde yapılan tahminlerin hata dağılımları incelenmiştir. Birbirine çok benzeyen hız sınırı levhalarının (Örn: 30km/s ve 50km/s) ufak oranlarda birbirleriyle karıştığı, ancak genel olarak ayırt etme yeteneğinin mükemmele yakın olduğu görülmüştür.
