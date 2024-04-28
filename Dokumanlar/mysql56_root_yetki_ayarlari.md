
FreeBSD 13.1'de MySQL 5.6'yı dışarıdan gelen bağlantılara ve dışarıdan root erişimine açmak için, aşağıdaki adımları izleyebilirsiniz:

- MySQL sunucusuna yerel olarak bağlanın. Bu işlem için, mysql komutunu kullanabilirsiniz. Örneğin, şu komutu çalıştırabilirsiniz: `mysql -u root -p`
- Root kullanıcısına dışarıdan erişim izni verin. Bu işlem için, GRANT komutunu kullanabilirsiniz. Örneğin, şu komutu çalıştırabilirsiniz: `GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root-password' WITH GRANT OPTION;` Bu komut, root kullanıcısının herhangi bir IP adresinden MySQL sunucusuna bağlanmasına, tüm veritabanlarına ve tablolara erişmesine, verileri değiştirmesine ve diğer kullanıcılara yetki vermesine izin verir. Burada, root-password yerine istediğiniz bir şifre belirleyebilirsiniz¹.
- Değişiklikleri etkinleştirmek için, FLUSH komutunu kullanabilirsiniz. Örneğin, şu komutu çalıştırabilirsiniz: `FLUSH PRIVILEGES;`
- MySQL sunucusunun ayar dosyasını düzenleyin. Bu dosya, /etc/mysql/mysql.conf.d/mysqld.cnf'dir. Bu dosyayı bir metin editörü ile açabilirsiniz. Örneğin, nano komutunu kullanarak şu şekilde açabilirsiniz: `sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf`
- Ayar dosyasında, bind-address satırını bulun. Bu satır, MySQL sunucusunun hangi IP adreslerine bağlantı kabul edeceğini belirler. Bu satır, varsayılan olarak şöyle görünür: `bind-address = 127.0.0.1` Bu satır, MySQL sunucusunun sadece yerel bağlantılara izin verdiği anlamına gelir. Bu satırı, yorum satırı haline getirin. Bunu yapmak için, satırın başına # işareti koyun. Örneğin, şu şekilde değiştirin: `#bind-address = 127.0.0.1` Bu satırı yorum satırı haline getirmek, MySQL sunucusunun tüm IP adreslerine bağlantı kabul etmesini sağlar².
- Ayar dosyasını kaydedip çıkın. Nano kullanıyorsanız, Ctrl+O tuşlarına basarak kaydedin, sonra Ctrl+X tuşlarına basarak çıkın.
- MySQL sunucusunu yeniden başlatın. Bu işlem için, systemctl komutunu kullanabilirsiniz. Örneğin, şu komutu çalıştırabilirsiniz: `sudo systemctl restart mysql`

Bu adımları takip ederek, MySQL 5.6'yı dışarıdan gelen bağlantılara ve dışarıdan root erişimine açabilirsiniz. Daha fazla bilgi için, [bu sayfayı](^1^) veya [bu sayfayı](^2^) inceleyebilirsiniz.

Umarım bu cevap size yardımcı olmuştur. Başka bir sorunuz varsa, lütfen bana bildirin. 😊

¹: [MySQL: How to allow remote connection to mysql - Stack Overflow](^1^)
²: [How To Allow Remote Access to MySQL | DigitalOcean](^2^)

Kaynak: Bing ile konuşma, 26.11.2023
(1) How to Install Apache, MySQL, PHP and PHPMyAdmin on FreeBSD?. https://www.zenarmor.com/docs/freebsd-tutorials/how-to-install-apache-mysql-php-and-phpmyadmin-on-freebsd.
(2) Install and configure MySQL on FreeBSD · GitHub. https://gist.github.com/WillSquire/2d9cfc9eb97c3dd40f375dbf5e90f75b.
(3) How to Install Apache, MySQL, PHP and PHPMyAdmin on FreeBSD?. https://www.zenarmor.com/docs/freebsd-tutorials/how-to-install-apache-mysql-php-and-phpmyadmin-on-freebsd.
(4) Install and configure MySQL on FreeBSD · GitHub. https://gist.github.com/WillSquire/2d9cfc9eb97c3dd40f375dbf5e90f75b.
(5) How to Install Apache, MySQL, PHP and PHPMyAdmin on FreeBSD?. https://www.zenarmor.com/docs/freebsd-tutorials/how-to-install-apache-mysql-php-and-phpmyadmin-on-freebsd.
(6) Install and configure MySQL on FreeBSD · GitHub. https://gist.github.com/WillSquire/2d9cfc9eb97c3dd40f375dbf5e90f75b.
(7) FreeBSD 13.0 Release - Source Build Etmelik - MMO Tutkunları. https://www.mmotutkunlari.com/konu/freebsd-13-0-release-source-build-etmelik-oyun-calistirmalik-gcc8-gcc9-gcc10-mysql5-6-mariadb10-3-python2-7.11359/.

--

MariaDB 10 versiyonunda dışarıdan root erişimi sağlamak için aşağıdaki adımları izleyebilirsiniz:

1. **MariaDB Yapılandırma Dosyasını Bulma ve Düzenleme:**
   - MariaDB'nin `/etc/my.cnf` veya `/etc/mysql/my.cnf` gibi varsayılan yapılandırma dosyasını bulun¹.
   - `bind-address` yönergesini bulun ve değerini `0.0.0.0` olarak değiştirin ya da tamamen kaldırın. Bu, MariaDB sunucunuzun tüm ağ arayüzlerinden bağlantı kabul etmesini sağlar¹.

2. **MariaDB Kullanıcı İzinlerini Ayarlama:**
   - MariaDB komut satırı istemcisini kullanarak, root kullanıcısına uzaktan erişim izni verin. Aşağıdaki komutu kullanabilirsiniz, ancak `PASSWORD` yerine gerçek MariaDB root şifrenizi yazmayı unutmayın:
     ```
     GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'PASSWORD' WITH GRANT OPTION;
     ```
     Bu komut, root kullanıcısına herhangi bir hosttan (`%` sembolü ile gösterilir) bağlanma izni verir³.

3. **MariaDB Sunucusunu Yeniden Başlatma:**
   - Yapılandırma değişikliklerinin etkili olması için MariaDB sunucusunu yeniden başlatın.

4. **Güvenlik Duvarı Ayarlarını Kontrol Etme:**
   - MariaDB'nin uzaktan bağlantıları kabul etmesi için, 3306 numaralı portun güvenlik duvarınızda açık olduğundan emin olun¹.

Bu adımları takip ederek, MariaDB 10 versiyonunda dışarıdan root erişimi ile bağlantıya izin verebilirsiniz. Ancak, bu işlemleri yaparken güvenlik konusunda dikkatli olmanız önemlidir. Uzaktan erişim, potansiyel güvenlik riskleri taşıyabilir, bu nedenle güvenlik duvarı kurallarınızı ve kullanıcı izinlerinizi dikkatlice yönettiğinizden emin olun..

Kaynak: Bing ile konuşma, 13.04.2024
(1) Configuring MariaDB for Remote Client Access. https://mariadb.com/kb/en/configuring-mariadb-for-remote-client-access/.
(2) Connect to MariaDB from a different machine - Bitnami. https://docs.bitnami.com/virtual-machine/infrastructure/lamp/administration/connect-remotely-mariadb/.
(3) How to enable Remote access to your MariaDB/MySQL database on ... - Webdock. https://webdock.io/en/docs/how-guides/database-guides/how-enable-remote-access-your-mariadbmysql-database.
(4) Grant root account remote access to MariaDB - Community. https://community.flexera.com/t5/Software-Vulnerability-Manager/Grant-root-account-remote-access-to-MariaDB/ta-p/4967.
(5) MariaDB not allowing remote connections - Stack Overflow. https://stackoverflow.com/questions/21664091/mariadb-not-allowing-remote-connections.
(6) tr.wikipedia.org. https://tr.wikipedia.org/wiki/MariaDB.