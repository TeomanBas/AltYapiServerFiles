FreeBSD 13.1'de ftpd sunucusunda root kullanıcısının giriş yapması engellenmişse, bunu değiştirmek için aşağıdaki adımları izleyebilirsiniz:

- root kullanıcısına geçin. `su -`
- ftpd ayar dosyasını düzenleyin. Bu dosya, /etc/ftpd.conf'dur. Bu dosyayı bir metin editörü ile açabilirsiniz. Örneğin, nano komutunu kullanarak şu şekilde açabilirsiniz: `nano /etc/ftpd.conf`
- Ayar dosyasında, root kullanıcısının giriş yapmasına izin veren seçeneği bulun. Bu seçenek, root olarak ayarlanmıştır. Örneğin, şu satırı bulabilirsiniz: `root`
- Bu seçeneği, yorum satırı haline getirin. Bunu yapmak için, satırın başına # işareti koyun. Örneğin, şu şekilde değiştirin: `#root`
- Ayar dosyasını kaydedip çıkın. Nano kullanıyorsanız, Ctrl+O tuşlarına basarak kaydedin, sonra Ctrl+X tuşlarına basarak çıkın.
- ftpd servisini yeniden başlatın. `service ftpd restart`

Bu adımları takip ederek, FreeBSD 13.1'de ftpd sunucusunda root kullanıcısının giriş yapmasını sağlayabilirsiniz. Daha fazla bilgi için, ftpd'nin man sayfasını¹ inceleyebilirsiniz.

Umarım bu cevap size yardımcı olmuştur. Başka bir sorunuz varsa, lütfen bana bildirin. 😊

¹: [ftpd - FreeBSD](^1^)

Kaynak: Bing ile konuşma, 26.11.2023
(1) ftpd - FreeBSD. https://man.freebsd.org/cgi/man.cgi?ftpd.
(2) ftpd - FreeBSD. https://man.freebsd.org/cgi/man.cgi?ftpd.
(3) Enable root Login On FreeBSD 10 | Unixmen. https://www.unixmen.com/enable-root-login-ssh-freebsd-10/.
(4) How To Enable SSH On FreeBSD - OSTechNix. https://ostechnix.com/how-to-enable-ssh-on-freebsd/.
(5) How to Allow root account to connect via SSH in FreeBSD. https://www.dark-hamster.com/operating-system/how-to-allow-root-account-to-connect-via-ssh-in-freebsd/.


---

Merhaba, Copilot burada. Size yardımcı olmaya çalışacağım. 😊

FreeBSD 13.1 versiyonunda dışarıdan root kullanıcısına ftp erişimini açmak için şu adımları izleyebilirsiniz:

- Öncelikle, ftp sunucusu olarak **vsftpd** veya **pure-ftpd** gibi bir program kurmanız gerekiyor. Bu programları **pkg** veya **ports** aracılığıyla yükleyebilirsiniz¹².
- Daha sonra, ftp sunucusu programının yapılandırma dosyasını (**/usr/local/etc/vsftpd.conf** veya **/usr/local/etc/pure-ftpd.conf**) açın ve aşağıdaki satırı ekleyin veya düzenleyin:

`userlist_enable=YES`

- Bu satır, ftp sunucusunun **/etc/ftpusers** ve **/etc/vsftpd.user_list** veya **/etc/pureftpd.passwd** gibi kullanıcı listelerini kontrol etmesini sağlar³⁴.
- Bu listelerden, root kullanıcısının önündeki yorum işaretini (#) kaldırın veya root kullanıcısını silin. Böylece, root kullanıcısının ftp erişimine izin verilir³⁴.
- Son olarak, ftp sunucusu programını yeniden başlatın veya sistemi yeniden başlatın. Bunu yapmak için şu komutu kullanabilirsiniz:

`service vsftpd restart` veya `service pure-ftpd restart`

- Artık root kullanıcısıyla ftp bağlantısı kurabilirsiniz. Ancak, bunu yapmanın çok güvenli olmadığını ve root şifrenizin ağ üzerinden şifresiz olarak gönderilebileceğini unutmayın⁵. Bu yüzden, mümkünse **ssh** veya **sftp** gibi daha güvenli protokolleri kullanmanızı tavsiye ederim.

Umarım bu cevap yardımcı olmuştur. Başka bir sorunuz varsa, lütfen bana bildirin. 😊

Kaynak: Bing ile konuşma, 22.02.2024
(1) How to enable root access for FTP (pure-fptd) - The FreeBSD Forums. https://forums.freebsd.org/threads/how-to-enable-root-access-for-ftp-pure-fptd.44259/.
(2) How to configure vsftpd to allow root logins? - Server Fault. https://serverfault.com/questions/34678/how-to-configure-vsftpd-to-allow-root-logins.
(3) How do I set up an FTP user with access to web root using vsftpd?. https://askubuntu.com/questions/1121317/how-do-i-set-up-an-ftp-user-with-access-to-web-root-using-vsftpd.
(4) centos - Allow root user to login on ftp - Stack Overflow. https://stackoverflow.com/questions/31896779/allow-root-user-to-login-on-ftp.
(5) Setting Up and Securing SSH on FreeBSD 13: A ... - ServerAstra. https://serverastra.com/docs/Tutorials/Setting-Up-and-Securing-SSH-on-FreeBSD-13%3A-A-Comprehensive-Guide.