📡 nRF24L01 Haberleşme Protokolü ve Teknik Analiz Rehberi
Bu bölümde; projede kullanılan nRF24L01+ 2.4GHz RF kablosuz haberleşme modülünün çalışma mimarisi, endüstriyel standartlardaki yeri,
Bluetooth protokolü ile karşılaştırmalı analizi ve yüksek hızlı veri aktarımı için gereken kritik optimizasyonlar teknik detaylarıyla 
ele alınmıştır.

<img width="761" height="359" alt="image" src="https://github.com/user-attachments/assets/cfff4337-600a-4e21-be04-b770b9b78c74" />

1. nRF24L01 Nedir ve Öne Çıkan Özellikleri Nelerdir?
nRF24L01, Nordic Semiconductor firması tarafından geliştirilen, 2.4 GHz ISM (Industrial, Scientific, and Medical) frekans bandında
çalışan, ultra düşük güç tüketimine sahip tek çipli bir telsiz (transceiver) modülüdür.

⚙️ Öne Çıkan Teknik Özellikler:

Çalışma Frekansı: 2.4 GHz - 2.525 GHz aralığında 125 farklı frekans kanalı (Girişimi önlemek için kanal seçimi yapılabilir).

Donanımsal Paket Yönetimi (Enhanced ShockBurst™): Paketlerin otomatik olarak yapılandırılması, gönderilmesi, paket bütünlük kontrolü (CRC) ve onay 
(ACK - Acknowledgement) sinyallerinin donanımsal olarak yönetilmesi.

Otomatik Yeniden Gönderim (Auto-Retransmit): Paket alıcıya ulaşmadığında, işlemciyi yormadan donanımsal düzeyde paketi otomatik olarak yeniden gönderir.

Çoklu Alıcı Desteği (Multiceiver): Aynı anda 6 farklı verici borusundan (data pipe) gelen verileri tek bir alıcı modül ile dinleyebilme yeteneği.

Düşük Güç Tüketimi: Aktif iletim modunda dahi mikroamper ($\mu A$) seviyelerinde akım çekerek batarya dostu çözümler sunması.

2. "Doğrudan Bağlantı" (Handshake) Olmaması Ne Anlama Gelir?

nRF24L01 haberleşmesinde, Bluetooth veya Wi-Fi teknolojilerindeki gibi aktif ve sürekli bir "bağlantı kuruldu" (connected) durumu yoktur. * Çalışma Prensibi: Verici tarafı,
alıcının açık olup olmadığını veya kapsama alanında bulunup bulunmadığını önceden sorgulamaz. Veri paketi hazırlanır, havaya salınır (broadcast).
Eğer alıcı açık ve aynı frekans kanalını (channel) ve adresi (address) dinliyorsa paketi yakalar.

 Güvenlik / Kontrol Sorunu: Cihazların birbiriyle fiziksel olarak bağlı kalmaması, sinyalin kesildiğinin veya cihazın kapsama alanı dışına 
 çıktığının anlaşılmasını zorlaştırır.

Çözüm (RF Watchdog): Bu sorunu çözmek için alıcı koduna bir Watchdog  Zamanlayıcısı eklenmelidir. Alıcı, örneğin son 1
saniye içinde yeni bir paket almadıysa, bağlantının koptuğunu varsayarak motorları ve kritik sistemleri güvenlik amacıyla otomatik olarak durdurmalıdır.

3. Hız Sorunları ve RF24_2MBPS Yapılandırmasının Önemi
nRF24L01+ modülü varsayılan olarak genellikle RF24_1MBPS veya RF24_250KBPS hızlarında çalışmaya ayarlanmıştır. Ancak özellikle silah
taretleri, yüksek hızlı step motorlar veya ahtapot robot kolları gibi anlık tepki verilmesi gereken milisaniyelik sistemlerde bu hızlar yetersiz
kalmakta ve gözle görülür gecikmelere (lag) yol açmaktadır.

Neden Hız Sorunu Yaşanır?
Haberleşme hızı düştükçe, bir paketin havada kalma süresi (Airtime) uzar. Havada kalma süresinin uzaması, elektromanyetik gürültülerden etkilenme 
ihtimalini artırır ve saniyede gönderilebilecek maksimum paket sayısını sınırlar.

Çözüm: radio.setDataRate(RF24_2MBPS);
Yabancı kaynaklardaki akademik ve endüstriyel optimizasyon çalışmalarına göre; kablosuz iletim hattındaki tıkanmaları (bottleneck) önlemek ve gecikmeyi minimuma
indirmek için veri hızının 2 Mbps olarak ayarlanması kritik öneme sahiptir:

radio.setDataRate(RF24_2MBPS); // Veri iletim hızını maksimum sınıra (2 Mbps) çek
