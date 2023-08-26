# Metin 2 Rehberi
Burada Metin 2 için 2014 yılında sızdırılan kaynak kodlarından temiz bir server client alt yapısı oluşturulmaya çalışılacak.Bununla beraber Metin2 kaynak koları hakkında bilgiler, derleme aşaması,server configurasyonu,oyunu çalıştırmak,Oyun içi ve oyun dışındaki server veya client tarafındanki hata çözümleri yer alacak.Burasının tüm Metin2 yapısı için kapsamlı notların ve bilgilerin yer aldığı bir sayfa olması hedefleniyor.

*Tarih : 2023.08.25*

## 1. FreeBsd 9.3 Server Kurulumu
Kaynak kodlar sızdırıldığında FreeBsd 9.3 bir işletim sistemi üzerinde çalıştığı için bu işletim sisteminin kurulumu ile başlayacağız.
Burada FreeBsd 9.3 kurulumunun anlatımı değilde Freebsd kurarken bazı ayrıntılara değinilecek.

İlk olarak makine oluşturuluyor makine Qemu/Kvm teknolojisi ile Virt Manager kullanılarak oluşturuldu.Ancak Oracle ın Virtual Box veya VMware de kullanılabilirdi.

**Makine Diskinin oluşturulması**
```bash
qemu-img create -f qcow2 freebsd_9-3_i386_4cpu-4096mem-60G-hdd.qcow2 60G  
```

**Sanal Makine** : 
Sanal makine qemu/kvm alt yapısında çalışıyor.Eğer aynı ayarlarda Bir sanal makine oluşturulmak istenirse [xml dosyası](./SanalMakine/Config/freebsd_9-3_i386_4cpu-4096mem-60G-hdd-conf.xmls) ile aynı makine oluşturulabilir.Oluşturulan makine içerisinde şifreler de *readme* dosyası içerisindedir.Şifreler ,xml içerisindeki disk ve iso dizini değiştirilebilir hepsi farklılık gösteren şeylerdir.Sanal makinanın configurasyonunun nasıl yedeklendiği ve nasıl oluşturulduğu ile ilgili bir [döküman](./Dokumanlar/QemuLibvirt-diskimage-vmconf.md)


**Makine kurulumu ve sonra yapılan configurasyonlar**
Makine kurulurken ssh ve dumpdev servislerinin aktif edilmesi gerekli eğer aktif edilmesse daha sonradanda aktif edilebilir.

Kurulum tamamlandıktan sonra `/etc/ssh/sshd.config` dosyasını `ee` editörü ile açıp düzenlemeler yapmammız lazım.
`ee /etc/ssh/sshd.config` şeklinde bir komut ile açabiliriz.
Dosyayı açtıktan sonra bu dosya içerisinde `#PasswordAuthentication no` yazan satırı bulup `PasswordAuthentication yes` olarak değiştiriyoruz. `#` işaretleri yorum satırı olduğunu gösterir ve o satırdakiler dikkate alınmaz.Aynı şekilde `#PermitRootLogin no` yazan satırı bulup `yes` olarak değiştiriyoruz.Bu şekilde dışarıdan root olarak ssh bağlantısı yapabileceğiz.

Bu ayarlamaları yaptıktan sonra `reboot` komutu ile makine yi yeniden başlatıyoruz bundan sonrada ssh ve ftp ile bağlanabiliriz ftp varsayılan olarak açık oluyor.

## 2. Server Paketlerinin kurulumu
FreeBsd 9.3 sürümünde `pkg` paket yöneticisinin desteği kalktığı için bu paket yöneticisini kendimiz kurmamız gerekli.Aşağıdaki komut portslar içerisinden `pkg` yi kurabiliriz.Bu komut `pkg` yi kaynak kodlarından derler ve `pkg` kurmamızın sebebi hazır derlenmiş olan paketleri kurabilmek.

```bash
cd /usr/ports/ports-mgmt/pkg/ && make -DBATCH install clean
```
kurulması gereken paketlerin  bulunduğu zip dosyası `./Paketler/Freebsd-9.3-temel-paketleri.zip`
Bu dosyanın içindekileri server içerisinde `/var/cache/pkg/` dizinine atıyoruz.Kurulumları buradan yapacağız.

## 3. Paketlerin Kurulumu
`pkg add <paket_adı>` komutuyla paketlerin kurulumunu yapıyoruz ancak paketlerin bağımlılıklarından dolayı aşağıdaki sırayla kurmak gerekli.

1. xproto-7.0.27-8f0c903ddd.txz
2. indexinfo-0.2.3-8e42ef8da1.txz
3. libedit-3.1.20150325_2,1-80bff3b261.txz
4. openssl-1.0.2_3-05319ee068.txz 
5. libffi-3.2.1-150efa3e3e.txz 
6. python27-2.7.9_1-8e3d7936d0.txz
7. python2-2_3-560d6468f5.txz
8. python-2.7_2,2-c3fe5f14d5.txz
9. gmake-4.1_2-fdd1e6002a.txz 
10. makedepend-1.0.5,1-4d00b3bcf3.txz 
11. perl5-5.24.1.r4_1-1ea84e082b.txz
12. mysql56-client-5.6.34-5f5369ef5b.txz
13. mysql56-server-5.6.34-8cc3d17934.txz

Temel Paketler hakkında kısa [açıklama](./Dokumanlar/TemelPaketler.md)

```shell
root@metin2server:/var/cache/pkg # pkg info
gmake-4.1_2                    GNU version of 'make' utility
indexinfo-0.2.3                Utility to regenerate the GNU info page index
libedit-3.1.20150325_2,1       Command line editor library
libffi-3.2.1                   Foreign Function Interface
makedepend-1.0.5,1             Dependency generator for makefiles
mysql56-client-5.6.34          Multithreaded SQL database (client)
mysql56-server-5.6.34          Multithreaded SQL database (server)
openssl-1.0.2_3                SSL and crypto library
perl5-5.24.1.r4_1              Practical Extraction and Report Language
python-2.7_2,2                 The "meta-port" for the default version of Python interpreter
python2-2_3                    The "meta-port" for version 2 of the Python interpreter
python27-2.7.9_1               Interpreted object-oriented programming language
xproto-7.0.27                  X11 protocol headers
```
sadece googletest paketini kurmadık bunu ilerleyen kısımlarda kuracağız zatem şu anda lzma bağımlılığı var hemen kuramayız.

mysql dizini için dosya yekilerini ayarlayıyoruz.
```shell
root@metin2server:~ # chown -R mysql:mysql /var/db/mysql
```
sunucu açıldığında otomatik olarak mysql servisinin çalışması için rc.conf dosyasına mysql servisini ekliyoruz.
```shell
root@metin2server:~ # chown -R mysql:mysql /var/db/mysql
```
mysql servisini başlatıyoruz
```shell
root@metin2server:~ # service mysql-server start
Starting mysql.
```
mysql için root şifresi belirliyoruz ilk kez mysql kurulmuşsa şifre boştur
```shell
root@metin2server:~ # mysqladmin -u root -p password
Enter password: 
New password: 
Confirm new password: 
```
`mysql -u root -p` komutuyla mysql e bağlanıyoruz ve aşağıdaki yetkilendirmeleri yapıyoruz her ihtimale karşın CREATE USER denemesi yaptık ama zaten root kullanıcının var olduğunu biliyorduk.
```shell
mysql> CREATE USER 'root'@'%' IDENTIFIED BY 'root';
ERROR 1396 (HY000): Operation CREATE USER failed for 'root'@'%'

mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql> quit
Bye

```

buradan sonra workbench yada navicat gibi uygulamalar ile mysql bağlantısı gerçekleştirilebilir.

## 4. Kaynak Kodlar
Kaynak kodlar 2014 yılına ait [ham kaynak kodlardır](./Paketler/kaynakkodlar/kraizy.zip)
Buradaki kodlar içerisinden şimdilik novaline ı üzerinde çalışacağız novaline da bazı bugların fixlendiğini ve bazı hataların zaten giderilmiş olduğunu biliyoruz.öncelikle çalışma yapabilmek için temizlik yapmamız gerekli bunun bu zip dosyasını ayıkladıktan sonra içerisinde sadece novaline dosyaları kalacak ve novaline içerisinde bulunan debug, release, obj gibi derleme sırasında ortaya çıkan dosyaları hem yer kaplamaması hemde kafa karaşıklığına neden olmaması için siliyoruz. 

kraizy.zip arşivini ayıkladıktan içerisindekilerden silininen dizinler
```shell
/dev
/dev_wolf_branch
/mainline_cython
/mainline_released
/mainline_w2.0
/mainline_w2.1
/mainline

# tüm kalsörlerin içindeki Debug klasörleri silinecek. ve _UpgradeReport_Files ,ipch adlı klasörler silinecek 

/novaline/Srcs/Client/obj
/novaline/Srcs/Client/ipch
/novaline/Srcs/Client/Metin2Client_VC90.sdf

/novaline/Srcs/Server/db/Depend
/novaline/Srcs/Server/db/Depend.bak
/novaline/Srcs/Server/db/DB_BUILD_LOG.txt
/novaline/Srcs/Server/db/ERROR_LOG.txt

/novaline/Srcs/Server/game/locale
/novaline/Srcs/Server/game/data
/novaline/Srcs/Server/game/asdf
/novaline/Srcs/Server/game/CONFIG
/novaline/Srcs/Server/game/game.idb
/novaline/Srcs/Server/game/game.pdb
/novaline/Srcs/Server/game/game_r_32
/novaline/Srcs/Server/game/metin2@10.1.89.23
/novaline/Srcs/Server/game/sdf
/novaline/Srcs/Server/game/test
/novaline/Srcs/Server/game/VERSION.txt
/novaline/Srcs/Server/game/src/_UpgradeReport_Files
/novaline/Srcs/Server/game/src/OBJDIR

/novaline/Srcs/Tools/WorldEditor/WorldEditor_VC90.sdf
/novaline/Srcs/Tools/obj
/novaline/Srcs/Tools/WorldEditor/Debug
/novaline/Srcs/Tools/WorldEditor/ipch
/novaline/Srcs/Tools/_UpgradeReport_Files
```

bu temizliğin ardından novaline dosyasını tar formatında arşivliyoruz([novaline](./Paketler/TemizKaynakKod-novaline/novaline.tar)) hem elimizin altında bir temiz kaynak bulunsun hemde bunu serverde `/home` diznine arşiv olarak atıp orada tekrar ayıklayacağız direkt dosyaları atarsak bazen aktarımda dosya isimlerinden dolayı hata verebiliyor.


Server tarafına attığımız ham dosyalar aynı zamanda bir git alt yapısında tutulacak ve dosyaların üzerinde yapılan değişiklikler git ile izlenmesi sağlanacak.Git izlemi **novaline** ın gereksiz yer kaplayan dosyalardan arındırıldıktan sonraki hali git e dahil edildi.Burada takip edilecek Dosya bu dizindeki `Server` dizininin altında yer alıyor yani Server dosyasını oyun kaynak kodlarını derlediğimiz sunucu olarak düşünebiliriz.Oyun Dosyaları dışında yapılan değişikler örnek olarak pkg kurulumları ve sistem içerisinde kullanlan bazı kısımlar zaten bu anlatımda yer alacak sadece server klasörünü oyun dosyalarının toplu bir izlemesi olarak düşünebiliriz.

`/home` dizini oluşturduktan sonra içerisine sadece arşivi atıyoruz ve ayıklıyoruz daha sonra `/home/novaline/Srcs` içerisine giriş  `Server` dosyasının haricindekileri siliyoruz. `Extern` nin hepsini atmayacağız çünkü içerisinde çalışmayan eklentiler var bunların bazılarını kendimiz oluşturacağız.

## 5. Server Extern Dosyalarını Oluşturmak
- `/home/novaline/Srcs/Extern` dizinini oluşturuyoruz ve içerisine arşivimizde bulunun `/novaline/Srcs/Extern/` dizininde bulunan `cryptopp` dosyasını atıyoruz.
- Daha sonra aynı dizine `/home/novaline/Srcs/Extern/include`,`/home/novaline/Srcs/Extern/lib` adında iki klasör oluşturuyoruz.Arşivde bulunan `Extern/include/` içindeki `cryptopp`, `gtest`, `il` dosyalarını serverdaki `/home/novaline/Extern/include/` dizinine atıyoruz.
- Daha sonra [Boost](./Paketler/ServerFilesPaketleri/boost_1_43_0.tar.gz) arşivini `/home/novaline/Extern/include/` dizinine atıyoruz ve aynı dizine ayıklıyoruz. Ayıklanan dosyanın içerisindeki `boost` klasörünü bir dizin geriye `/home/novaline/Extern/include/` dizinine alıyoruz.
- Dada sonra `cp -r /usr/local/include/mysql` klasörünü `/home/novaline/Srcs/Extern/include/` içerisine aynı isimde ve içerisinkeki dosyalarla kopyalıyoruz.
- Daha sonra `/home/novaline/Srcs/Server/libdevil/` içerisindeki `.a` uzantılı static kütüphaneleri `/home/novaline/Srcs/lib/` içerisine atıyoruz.
- Dahra sonra `googletest-1.7.0.txz` paketi kurulmamıştı bu paketi arşiv yönetici ile açıp içerisinden `/usr/local/lib/libgtest.a` dosyasını alıp `/home/novaline/Extern/lib/` içerisine atıyoruz.

## 6. Server Kodlarının Build Edilmesi
Öncelikle `/home/novaline/Srcs/Extern/cryptopp/` dizininin içerisine gelip `gmake libcryptopp.a -j10` komutuyla cryptopp un derlenmesini sağlıyoruz ve `/home/novaline/Srcs/Extern/lib/libcryptopp.a` dosyası çıkartıldı.

server üzerinden [svn-version](./Dokumanlar/svn-version.md) paketi kurulu olmadığı için server klasörü derlenirken svn hatası verecektir bunun makefile üzerinden bir düzenleme yapıyoruz.