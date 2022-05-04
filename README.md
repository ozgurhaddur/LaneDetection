# LANE DETECTION

## Amaç
Çeşitli görüntü işleme yöntemleri kullanılarak araç içerisinde bulunan kamera aracılığıyla elde edilen görüntüleri değerlendirerek gerçek zamanlı olarak aracın sağ ve solundaki trafik şeritlerini bulmaya yönelik şerit bölgesi belirleme, şerit eşleştirme ve şerit takibi olmak üzere şerit algılama işlemlerini gerçekleştiren bir sistem tasarlanmış ve gerçeklenmiştir. Bu tür sistemlerde amaç, insan kontrolüne gerek kalmaksızın güvenli sürüşü sağlamak ve insan hatalarından kaynaklanan kazaları engellemeye yardımcı olmaktır.

## Yöntem
Uygulamayı çalıştırmak için kullanılacak IDE içinde import edilmesi gereken kütüphaneler yer almaktadır. 
Bu kütüphanelerin import edilmeden önce eğer daha önce görüntü işleme üzerine proje yapılmamışsa ya da kütüphaneler ekli değilse command prompter (cmd) aracılığıyla piplenmesi gerekmektedir. Aksi takdirde import edilen kütüphaneler için ModuleNotFoundError: No module named ‘library_name’ şeklinde hata ile karşılaşılmaktadır.

### A.Görüntü İşleme
Öncelikle gerekli kütüphaneler import edilir.

![image](https://user-images.githubusercontent.com/73740709/166804915-2c9184a8-f09e-4109-8930-d89a24d0864c.png)

Kütüphaneler import edildikten sonra görüntünün işlenebilmesi okuma işleminin gerçekleşmesi gerekmektedir.

![image](https://user-images.githubusercontent.com/73740709/166805020-f0ab5bc5-76c2-44ee-a4e1-8697b4147a7e.png)

Görüntünün okuma işlemi gerçekleştikten sonra kenarları ayırmamıza yarayacak şekilde, görüntünün siyah-beyaz olarak elde edilmesi gerekmektedir. 
Görüntüyü grayscale elde etmek için  cv2.COLOR_RGB2GRAY flagi kullanılmaktadır.

![image](https://user-images.githubusercontent.com/73740709/166805088-17ac69f7-6738-4ac5-825a-dc8d2ae6f485.png)

Grayscale görüntü elde edildikten sonra görüntünün gürültüsünü azaltmak gerekmektedir. Gürültü azaltma işlemi bir nevi blurlama işlemidir. Bu işlemin gerçekleşmesi için cv2.GaussianBlur fonksiyonu kullanılmaktadır. Gaussian Blurlama, görüntüleri 'bulanıklaştırmak', ayrıntı ve gürültüyü ortadan kaldırmak için kullanılan 2 boyutlu konvolüsyon (çekirdek matris ile resim üzerindeki piksellerin çarpımı işlemi) operatörüdür.

![image](https://user-images.githubusercontent.com/73740709/166805160-52bd4f6d-fdf7-4c5a-827c-f0dd8c31a107.png)

### B.Canny Algoritması
Görüntü blurlama işlemi gerçekleştikten sonra çalışma şerit algılama mantığı üzerine kurulduğundan kenar algılama işleminin gerçekleşmesi gerekmektedir. 
Birçok kenar algılama algoritması bulunmaktadır, bunlardan Canny algoritması kullanılmıştır. 
Canny kenar belirleme algoritması; görüntüde keskin olarak belirlenmiş kenarları bulmak için geliştirilmiş ve aşamaları olan bir algoritmadır. 
Görüntünün gürültülerini azaltmak amacıyla Gaussian çekirdekle konvolüsyon alınarak azaltılır. 
Çalışmada da önceki aşamada Gaussian blurlama işlemi gerçekleşmişti. 
Canny fonksiyonu cv2.Canny(goruntu, düşük_threshold, yüksek_threshold)  değerleri ile tanımlanır. 
OpenCV içerisindeki threshold fonksiyonunun istediği ilk parametre işlem yapılacak resmin kendisidir. 
Sonrasındaki parametre ise verilen eşik değerinden daha düşük olan değerlerin kenar olarak alınmamasını sağlamaktadır. 
Üçüncü parametre ise bu eşik değeri geçen matris elemanlarının atanmasını istediğimiz değerdir.

![image](https://user-images.githubusercontent.com/73740709/166805477-3533896c-d1ab-4373-a670-4a4e485b6766.png)

#### Canny Fonksiyonu

![image](https://user-images.githubusercontent.com/73740709/166805606-1ab2a2a5-dda4-4ccf-b61d-823be154d9c8.png)

Canny algoritması ile birlikte aşağıda verilen ilk görüntü kenarların threshold değerlerine göre algılandığı ikinci şekle bürünmüştür.

![image](https://user-images.githubusercontent.com/73740709/166805679-8728ac3c-41f8-4e8f-ac5d-715dbd88ee0b.png)

![image](https://user-images.githubusercontent.com/73740709/166805728-cfbf6102-49c1-40d8-b0ec-a4f26a5c65b8.png)

### C.İlgilenilecek Bölgenin Tespiti
Görüntünün kenar algılamalarının tespiti gerçekleştikten sonra ilgilenilen bölgenin tespiti için matplotlib kütüphanesi kullanılmıştır.
İlgilenilen bölgenin tespiti için x ve y değerlerinin izdüşümleri gözlenmiş olup [200,1100] değerleri belirlenmiştir. 
Tepe noktası olarak da [550,250] değeri gelmektedir.

![image](https://user-images.githubusercontent.com/73740709/166805850-a9eb4fba-a38f-4138-b06f-c42f9ac08601.png)

![image](https://user-images.githubusercontent.com/73740709/166805899-ecce27ae-9b84-47f9-b1d4-5e9fcb7d26e0.png)

### D.Hough Transform
İlgilenilen bölgenin tespitinden sonra görüntü kırpılarak sonuçların oluşacağı hough transform için görüntü hazırlanır. 
Hough dönüşümü temelde kenarların olası geometrik şekilleri oylaması mantığı ile çalışmaktadır. 
Hough transform ile birlikte görüntünün kenarlıkları ortaya çıkmıştır. 
Fakat her kenara göre bir kenar çizgisi ortaya çıktığı için görüntü bu şekilde ortaya çıkmaktadır.

![image](https://user-images.githubusercontent.com/73740709/166806266-26d6caaf-89ee-4e6e-abbb-411328aaa7f0.png)

Bunu engellemek için koordinatlara göre ortalama bir kenar çizgisi elde edilmek istenmiştir.  
Her kenar çizgisi eğimlerine ve olduğu tarafa (sağ veya sol) göre sınıflandırılıp ortalama bir kenar çizgisi ortaya çıkarılmıştır.

## Sonuç

![image](https://user-images.githubusercontent.com/73740709/166806443-7662628f-d8a2-4d22-a540-5fb415324276.png)

Çeşitli görüntü işleme yöntemleri kullanılarak araç içerisinde bulunan kamera aracılığıyla elde edilen görüntüler üzerinde şerit algılama tespiti gerçekleştirilmiştir.
