# Balık Türü Sınıflandırma Projesi

Bu yazı, balık türlerini sınıflandırmak için kullanılan bir derin öğrenme projesinin adım adım açıklamasını içermektedir. Bu proje, görüntü işleme, veri artırma (data augmentation) ve sinir ağları kullanarak balık türlerinin doğru bir şekilde sınıflandırılmasını amaçlamaktadır.

## Projenin Genel Amacı

Bu proje, çeşitli balık türlerine ait görüntüleri kullanarak, bir yapay sinir ağı modeli (Artificial Neural Network - ANN) ile bu balıkların türlerinin sınıflandırılmasını amaçlamaktadır. Veri setinde farklı balık türlerine ait resimler bulunmakta ve bu resimler üzerinde çeşitli veri işleme, model eğitim teknikleri uygulanarak bir sınıflandırma modeli geliştirilmiştir.

### 1. Gerekli Kütüphanelerin Yüklenmesi

Projenin ilk adımında, Python'da kullanılan çeşitli kütüphaneler projeye dahil edilmiştir. Bu kütüphaneler, görüntü işleme ve model eğitimi için gerekli olan fonksiyonları sağlar. Yüklenen kütüphaneler şunlardır:
- `os` ve `pathlib`: Dosya ve dizin yapılarının yönetimi için kullanılır.
- `glob`: Dosya sistemindeki belirli desenlere sahip dosyaları bulmak için kullanılır.
- `pandas` ve `numpy`: Veri manipülasyonu ve sayısal işlemler için kullanılır. Pandas, özellikle veri çerçeveleri (DataFrame) ile çalışmak için uygundur.
- `cv2 (OpenCV)`: Görüntü işleme ve analiz için kullanılır. Bu kütüphane, projedeki resim dosyalarının okunması, dönüştürülmesi ve işlenmesi için kullanılır.
- `seaborn` ve `matplotlib`: Veri görselleştirme için kullanılır.
- `tensorflow` ve `keras`: Derin öğrenme modelleri oluşturmak ve eğitmek için kullanılır.
- `scikit-learn`: Makine öğrenmesi algoritmaları ve veri işleme fonksiyonları sağlar.

Ayrıca, projedeki uyarı mesajlarının çıkmasını engellemek için `warnings.filterwarnings('ignore')` ifadesi ile uyarılar kapatılmıştır.


### 2. Veri Hazırlığı

Bu adımda, proje için kullanılan balık görüntüleri yüklenmiş ve işlenmeye hazır hale getirilmiştir.
- **Balık Resimlerinin Yüklenmesi**: Görüntü dosyaları, belirlenen ana dizindeki klasörlerden toplanmıştır. `glob` ve `pathlib` kullanılarak, tüm `.png` uzantılı görüntü dosyalarının yolu alınmıştır.
- **Etiketleme**: Görüntülerin etiketleri, dosya yolunun bir üst klasörü olarak belirlenmiştir. Bu, her bir balık görüntüsünün hangi türe ait olduğunu belirlememizi sağlar. `os.path.split()` fonksiyonu ile her görüntünün dosya yolundan balık türü etiketleri çıkarılmıştır.
- **DataFrame Oluşturma**: Dosya yolları ve etiketler, “pandas” kullanılarak bir DataFrame'e dönüştürülmüştür. Bu DataFrame, veri setinin temelini oluşturur ve her bir görüntüyle ilgili hem dosya yolunu hem de etiket bilgisini barındırır.


### 3. Veri Temizleme ve Karıştırma

Veri setinde bazı görüntülerin etiketleri "GT" ile bitmektedir. Bu görüntüler sınıflandırma esnasında problem yaşanmaması için çıkarılmıştır. Ayrıca, veri setinin rastgele karıştırılması sağlanmıştır. Bu, eğitim ve test aşamalarında veri setindeki sıralamadan kaynaklanabilecek hataları önlemeye yardımcı olunmasını sağlamaktadır.


### 4. Verilerin Dağılımı ve Görselleştirme

Veri setindeki balık türlerinin dağılımı analiz edilmiştir. Her bir balık türüne ait kaç görüntü bulunduğu sayıldı ve bu sınıfsal dağılım görselleştirildi. Bu adımların amacı, veri setindeki dengesizlikleri belirlemek ve gerektiğinde veri artırma (data augmentation) yöntemlerini uygulamaktır.
- **Balık türlerine göre görüntü sayıları**: Her balık türünün veri setinde ne kadar temsil edildiği belirlendi.
- **Yüzdesel dağılım(Frequency)**: Her türün veri setindeki toplam görüntü sayısına oranı hesaplandı ve yüzdelik dilimler ile sunuldu.


### 5. Görüntülerin Piksel Dağılımı

Veri setindeki görüntüler üzerinde yapılan incelemeler sonucunda, görüntülerin piksel değerlerinin dağılımı da analiz edildi. İlk 10 görüntünün piksel yoğunluğu incelendi ve bu piksel değerlerinin dağılımı bir histogram ile görselleştirildi. Bu adım, görüntülerin ne kadar iyi normalize edildiğini ve belirli bir örüntü olup olmadığını anlamamıza yardımcı olmaktadır.


### 6. Veri Setinin Bölünmesi

Veri seti, modelin eğitim ve test süreçlerinde kullanmak üzere üç ana bölüme ayrıldı:
- **Eğitim seti**: Modelin öğrenme işlemi bu veri ile gerçekleştirilir. Bu veri seti, toplam verinin %75'ini oluşturur.
- **Doğrulama seti**: Modelin eğitim sırasında performansını kontrol etmek için kullanılır. Bu veri seti, %10'luk bir kısmı oluşturur.
- **Test seti**: Modelin eğitimi tamamlandıktan sonra, gerçek performansını ölçmek için kullanılır. Bu set, %15'lik bir kısmı kapsar.

Verilerin bu şekilde bölünmesi, modelin genelleme kabiliyetini artırmayı ve aşırı öğrenmeyi önlemeyi amaçlamaktadır.


### 7. Veri Artırma (Data Augmentation)

Eğitim setindeki görüntüler üzerinde çeşitli veri artırma işlemleri uygulanmıştır. Veri artırma teknikleri, sınıflar arası dengesizlikleri azaltmak ve modelin farklı senaryolarla başa çıkabilme kabiliyetini artırmak amacıyla kullanılmıştır. Kullanılan artırma yöntemleri şunlardır:
- **Dönme (rotation)**: Görüntüler rastgele 30 dereceye kadar döndürülmüştür.
- **Zoom**: Görüntüler rastgele %20 oranında büyütülmüş veya küçültülmüştür.
- **Yatay çevirme**: Görüntüler yatay olarak çevrilmiştir.
- **Genişlik ve yükseklik kaydırma**: Görüntüler rastgele olarak yatay ve dikey eksende kaydırılmıştır.


### 8. One-Hot Encoded Etiketlerinin Görselleştirilmesi

Bu bölümde, görüntü veri setindeki etiketlerin one-hot encoding yöntemiyle dönüştürülmesi ve görselleştirilmesi anlatılmaktadır. `LabelBinarizer` kullanılarak etiketler ikili formata çevrilmiştir.

Eğitim setinden rastgele 10 görüntü seçilmiş ve her birinin karşılık gelen one-hot encoded etiketi ile birlikte gösterilmiştir. Görseller, başlıklarında hangi balık türüne ait oldukları ve one-hot encoded değerleri ile sunulmuştur.

Ayrıca, ilk 10 örneğe ait one-hot encoded etiketler bir ısı haritası (heatmap) ile görselleştirilmiştir. Bu harita, her balık türünün temsilini ve etiketlerin dağılımını net bir şekilde göstermektedir.


### 9. Yapay Sinir Ağı (ANN) Modeli

Bu projede kullanılan sinir ağı modeli, tam bağlı katmanlardan oluşan bir Artificial Neural Network (ANN) modelidir. Modelin yapısı aşağıdaki gibidir:
- **Giriş katmanı**: İlk olarak, görüntülerin piksel değerleri 0 ile 1 arasında ölçeklenmiştir.
- **Gizli katmanlar**: Modelde iki adet tam bağlı gizli katman bulunmaktadır. Bu katmanlar 512 nöron içermekte ve her biri ReLU aktivasyon fonksiyonunu kullanmaktadır. Ayrıca, aşırı öğrenmeyi (overfitting) önlemek için Dropout uygulanmıştır.
- **Çıkış katmanı**: Balık türlerinin sayısına (9 sınıf) karşılık gelen softmax aktivasyonlu bir çıkış katmanı kullanılmıştır.

Model, kategorik çapraz entropi kaybı (categorical cross-entropy loss) fonksiyonu ve Adam (Adaptive Moment Estimation (Uyarlanabilir Anlık Tahmin)) optimizasyon algoritması kullanılarak derlenmiştir.


### 10. Model Eğitimi

Model, yukarıda bahsedilen veri seti ve sınıf ağırlıkları dikkate alınarak eğitilmiştir. Eğitim sürecinde, erken durdurma (early stopping) yöntemi kullanılarak modelin fazla eğitilmesi (overfitting) engellenmiştir. Model 30 epoch boyunca eğitilmiş ve her epoch'ta eğitim ve doğrulama verisi üzerindeki performans takip edilmiştir.

Sonuçlar grafikler ile sunulmuştur:
- **Kayıp fonksiyonu**: Eğitim ve doğrulama kayıpları epoch bazında çizilmiştir.
- **Doğruluk grafiği**: Modelin eğitim ve doğrulama doğruluğu takip edilmiştir.

### 11. Modelin Değerlendirilmesi
#### Kayıp Fonksiyonu Görselleştirmesi
- Eğitim kaybı ve doğrulama kaybı, her bir epoch için çizilmiş ve kayıpların gelişimi izlenmiştir.
- Kayıp yüzdeleri, grafik üzerinde kayıp değerlerinin üzerinde belirtilmiştir; bu sayede her epoch için kayıp değeri daha net bir şekilde görülebilmektedir.

#### Doğruluk Görselleştirmesi
- Eğitim doğruluğu ve doğrulama doğruluğu da benzer şekilde grafikleştirilmiştir.
- Doğruluk yüzdeleri, grafik üzerinde doğruluk değerlerinin üzerinde gösterilerek her epoch için doğruluk oranları net bir şekilde sunulmuştur.

#### Sonuçların Yazdırılması
- Modelin son eğitim kaybı ve doğrulama kaybı ile son eğitim ve doğrulama doğruluk değerleri yazdırılmıştır. Bu bilgiler, modelin genel performansını özetlemekte ve değerlendirmekte yardımcı olmaktadır.

### 12. Hiperparametre Optimizasyonu

Hiperparametre optimizasyonu, modelin performansını artırmak için katman sayısı, nöron sayısı, öğrenme oranı ve dropout gibi parametrelerin ayarlanmasını içerir. Yapay sinir ağları (ANN) üzerinde çalışırken, bu parametrelerin doğru bir şekilde optimize edilmesi, modelin hem eğitim hem de doğrulama performansını doğrudan etkiler. Bu projede, başlangıçta kullanılan hiperparametrelerin değiştirilmesi ve bunun sonucunda elde edilen performans değerleri karşılaştırılmaktadır.

#### Başlangıç Parametreleri:
- **Yoğun Katman (Dense)**: 128 nöron
- **Dropout Oranı**: 0.2

Başlangıç hiperparametreleriyle modelin elde ettiği sonuçlar:
- **Son Eğitim Kaybı**: 1.042
- **Son Doğrulama Kaybı**: 1.020
- **Son Eğitim Doğruluğu**: 61.50%
- **Son Doğrulama Doğruluğu**: 58.20%

Performansı artırmak için iki önemli değişiklik yapıldı: Yoğun katmandaki nöron sayısı artırıldı ve dropout oranı korundu.

#### Yeni Parametreler:
- **Yoğun Katman (Dense)**: 512 nöron (iki adet)
- **Dropout Oranı**: 0.2

Yapılan bu hiperparametre değişiklikleri sonucunda modelin performansında iyileşme gözlendi. Yeni hiperparametreler ile modelin eğitim ve doğrulama sonuçları aşağıdaki gibidir:
- **Son Eğitim Kaybı**: 0.5871
- **Son Doğrulama Kaybı**: 0.4389
- **Son Eğitim Doğruluğu**: 78.16%
- **Son Doğrulama Doğruluğu**: 84.74%

Eğitim kaybındaki azalma ve doğrulama kaybındaki benzer orandaki iyileşme, modelin overfitting yapmadığını ve daha genel bir performans elde ettiğini işaret etmektedir.

### 13. Sonuç

Model, balık türlerinin sınıflandırılmasında başarılı sonuçlar vermiştir. Proje sonunda modelin doğrulama setindeki doğruluğu yüksek, veri kaybı ise düşük bir seviyede kalmış olarak sonuçlandırılmıştır.

### 14. Ek
#### Kaggle Erişim Linki
Kaggle'daki balık sınıflandırma datasetine ve bu veri setiyle ilgili yapılan çalışmaya erişmek için aşağıdaki bağlantıyı kullanabilirsiniz:
https://www.kaggle.com/code/fehmibahaaydn/fish-classdataset-ann
