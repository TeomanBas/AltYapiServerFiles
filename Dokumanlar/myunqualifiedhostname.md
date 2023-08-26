FreeBSD'de hostname değiştirmek için /etc/rc.conf dosyasında hostname="yeni_hostname" şeklinde bir satır eklemeniz veya var olan satırı değiştirmeniz gerekiyor. Bu dosyayı düzenlemek için nano, vi veya başka bir metin editörü kullanabilirsiniz. Örneğin, nano kullanmak isterseniz, şu komutu vermeniz yeterli:

sudo nano /etc/rc.conf

Dosyayı kaydedip çıktıktan sonra, hostname komutu ile yeni hostname'inizi görebilirsiniz. Eğer sistemi yeniden başlatmak istemiyorsanız, ayrıca şu komutu da vermeniz gerekir:

sudo service hostname restart

Bu komut, hostname servisini yeniden başlatarak yeni hostname'inizi etkinleştirir.

Bu konuda daha fazla bilgi için, FreeBSD belgelerine bakabilirsiniz¹³.

Kaynak: Bing ile konuşma, 25.08.2023
(1) Chapter 3. FreeBSD Basics | FreeBSD Documentation Portal. https://docs.freebsd.org/en/books/handbook/basics/.
(2) Chapter 14. Configuration, Services, Logging and Power Management. https://docs.freebsd.org/en/books/handbook/config/.
(3) FreeBSD Nasıl Kurulur? - Technopat. https://www.technopat.net/2022/06/28/freebsd-kurma-rehberi/.

---

Hostname standardı, bir bilgisayarın ağ üzerindeki adını belirleyen bir kurallar bütünüdür. Bir hostname, genellikle bir nokta ile ayrılan iki veya daha fazla bölümden oluşur. Bu bölümlerden en sağdaki, bilgisayarın bağlı olduğu üst seviye alan adını (top-level domain) gösterir. Örneğin, foo.bar.com.tr bir hostname'dir ve com.tr üst seviye alan adıdır. Hostname'lerin uzunluğu 255 karakteri geçemez ve sadece harf, rakam ve tire içerebilir¹.

My unqualified host name hatası, sendmail gibi bir e-posta sunucusu çalıştıran bir Linux sisteminde hostname'in tam olarak tanımlanmamış olmasından kaynaklanır. Bu hata, sendmail'in hostname'i tam olarak çözemediğini ve e-posta göndermek için uygun olmadığını belirtir². Bu hatayı çözmek için, /etc/hosts dosyasında hostname'in tam olarak yazılması gerekir. Örneğin, 127.0.0.1 localhost localhost.localdomain 127.0.0.1 foo.bar şeklindeki bir dosya hatalıdır, çünkü foo.bar tam bir alan adı değildir. Bu dosyayı 127.0.0.1 localhost localhost.localdomain foo.bar.com.tr 127.0.0.1 foo.bar.com.tr şeklinde değiştirmek gerekir². Ayrıca, hostname komutu ile sistemin hostname'ini de tam olarak ayarlamak gerekir³. Bu işlemlerden sonra, sendmail servisini yeniden başlatarak hatanın giderilip giderilmediğini kontrol etmek gerekir².

Bu konuda daha fazla bilgi için, web arama sonuçlarına bakabilirsiniz.

Kaynak: Bing ile konuşma, 25.08.2023
(1) Sendmail: "unqualified hostname unknown; sleeping for retry unqualified .... https://linuxconfig.org/sendmail-unqualified-hostname-unknown-sleeping-for-retry-unqualified-hostname.
(2) "My unqualified host name (foo.bar) unknown" Problem. https://serverfault.com/questions/58363/my-unqualified-host-name-foo-bar-unknown-problem.
(3) Sendmail SMTP "My unqualified hostname" and "My unqualified domain name". https://unix.stackexchange.com/questions/347392/sendmail-smtp-my-unqualified-hostname-and-my-unqualified-domain-name.

---

Hosts dosyasını düzenlemek için, öncelikle dosyayı yönetici olarak çalıştıran bir metin editörü ile açmanız gerekir. Bu işlem için Not Defteri'ni kullanabilirsiniz. Aşağıdaki adımları izleyebilirsiniz¹²⁶:

- Başlat menüsünü açın ve Not Defteri yazın.
- Çıkan sonucu sağ tıklayın ve Yönetici olarak çalıştır seçeneğini seçin.
- Dosya menüsünden Aç seçeneğini seçin.
- Aşağıdaki yola gidin: C:\\Windows\\System32\\Drivers\\etc\\
- Dosya adı yanındaki açılır menüden Tüm Dosyalar seçeneğini seçin.
- Hosts dosyasını çift tıklayın.
- Dosyada istediğiniz değişiklikleri yapın. Örneğin, bir IP adresi ile bir alan adı arasında eşleme yapmak için, IP adresini yazın, bir boşluk bırakın ve ardından alan adını yazın. Her satırda bir eşleme yapabilirsiniz. Eğer bir siteye erişimi engellemek istiyorsanız, 127.0.0.1 IP adresini ve ardından site adını yazabilirsiniz.
- Dosyayı kaydedin ve kapatın.

Bu işlemlerden sonra, hosts dosyasındaki değişikliklerin etkili olması için bilgisayarınızı yeniden başlatmanız gerekebilir³. Değişiklikleri test etmek için, Komut İstemi'ni açarak ping komutunu kullanabilirsiniz¹. Örneğin, ping google.com yazarak Google'a erişiminizi kontrol edebilirsiniz.

Bu konuda daha fazla bilgi için, web arama sonuçlarına bakabilirsiniz.

Kaynak: Bing ile konuşma, 25.08.2023
(1) Windows 10’da hosts dosyası nerede ve nasıl düzenlenir?. https://www.technicablog.net/windows-tutorials/windows-10-hosts-dosyasi-nerede-ve-nasil-duzenlenir.html.
(2) Hosts Dosyası Nerede Bulunur? Nasıl Değiştirilir? | Natro. https://www.natro.com/blog/hosts-dosyasi-nerede-bulunur-nasil-degistirilir/.
(3) HOSTS dosyası nerede ve nasıl düzenlenir? Windows 11. https://www.technicablog.net/windows-tutorials/hosts-dosyasi-nerede-nasil-duzenlenir.html.
(4) Hosts Dosyası Nerede Bulunur? Nasıl Değiştirilir? | Natro. https://bing.com/search?q=hosts+dosyas%c4%b1n%c4%b1+nas%c4%b1l+d%c3%bczenlemeliyim.
(5) Windows 10'da Hosts Dosyası Nasıl Düzenlenir - digiist.com. https://tur.digiist.com/windows/how-edit-hosts-file-windows-10-8336540.html.
(6) Bilgisayardaki hosts dosyası nedir ve ne için | ITIGIC. https://itigic.com/tr/what-is-the-hosts-file-and-what-is-it-for/.

---

FreeBSD'de my unqualified host name hatasını çözmek için hosts dosyasını düzenlemek, hostname'in tam olarak tanımlanmış olmasını sağlamak gerekiyor. Bu işlem için aşağıdaki adımları takip edebilirsiniz:

- Hostname'inizi bir nokta ile ayrılan iki veya daha fazla bölümden oluşacak şekilde belirleyin. Örneğin, myhost.mydomain.home gibi. Bu bölümlerin gerçekten geçerli olması gerekmez, sadece sendmail'in bir alan adına dayanması gerekir¹.
- Hostname'inizi /etc/rc.conf dosyasında hostname="yeni_hostname" şeklinde bir satır ekleyerek veya var olan satırı değiştirerek ayarlayın. Bu dosyayı düzenlemek için nano, vi veya başka bir metin editörü kullanabilirsiniz.
- Hostname'inizi /etc/hosts dosyasında da aynı şekilde yazın. Ayrıca, localhost ve 127.0.0.1 IP adresi ile de eşleştirin. Örneğin, şöyle bir dosya oluşturun:

::1 localhost
127.0.0.1 localhost
192.168.1.3 myhost.mydomain.home
192.168.1.3 myhost

- Sistemi yeniden başlatın veya hostname servisini yeniden başlatın:

sudo service hostname restart

- Sendmail servisini yeniden başlatın:

sudo service sendmail restart

Bu işlemlerden sonra, hatanın giderilip giderilmediğini kontrol edebilirsiniz.

Bu konuda daha fazla bilgi için, web arama sonuçlarına bakabilirsiniz²³.

Kaynak: Bing ile konuşma, 25.08.2023
(1) Freebsd my unqualified host name unknown sleeping for retry hatasının .... https://www.mmotutkunlari.com/konu/freebsd-my-unqualified-host-name-unknown-sleeping-for-retry-hatasinin-cozumu.7142/.
(2) sendmail and unqualified host name | The FreeBSD Forums. https://forums.freebsd.org/threads/sendmail-and-unqualified-host-name.323/.
(3) Erreurs "My unqualified hostname [...]" etc. · Wiki · sakura / FreeBSD .... https://gitlab.com/sakura-lain/freebsd-msi/-/wikis/Erreurs-%22My-unqualified-hostname-%5B...%5D%22-etc.?version_id=91dc2d0190fe6c7485e9cf8134f4c5ee8c240969.

---
