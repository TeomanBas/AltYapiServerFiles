Qemu libvirt disk imajlarını nereye oluşturduğunu merak ediyorsunuz. Bu sorunun cevabı, disk imajlarının hangi türde olduğuna bağlıdır. Disk imajları aşağıdaki türlerden birine sahip olabilir¹:

- Tam tahsisli ham dosyalar
- Seyrek tahsisli ham dosyalar
- Qcow2, vmdk gibi özel disk formatları

Disk imajlarının nereye oluşturulduğunu öğrenmek için, libvirt tarafından yönetilen depolama havuzlarını ve hacimlerini incelemeniz gerekebilir. Bir depolama havuzu, sanal makineler tarafından kullanılmak üzere yönetici tarafından ayrılan bir depolama miktarıdır. Depolama havuzları, depolama yöneticisi veya sistem yöneticisi tarafından depolama hacimlerine bölünür ve hacimler sanal makinelerin blok aygıtları olarak atanır¹.

Örneğin, bir NFS paylaşımı kullanarak disk imajlarını saklamak istiyorsanız, libvirt ile bir NFS havuzu tanımlayabilirsiniz. Bu havuzun yolu, disk imajlarının oluşturulacağı dizini belirtir¹. Bu dizin havuz tanımlandığında mevcut değilse, build işlemi ile oluşturulabilir².

Başka bir örnek olarak, bir iSCSI havuzu kullanabilirsiniz. Bu durumda, iSCSI sunucusu tarafından tanımlanan LUN'lar depolama hacimleri olarak raporlanır ve disk imajları bu LUN'ların üzerinde oluşturulur¹.

Disk imajlarının nerede oluşturulduğunu görmek için, virsh komutunu kullanabilirsiniz. Örneğin, aşağıdaki komut tüm depolama havuzlarını ve hacimlerini listeler:

`virsh pool-list --all`

`virsh vol-list --pool <pool-name>`

Umarım bu bilgiler size yardımcı olmuştur. Başka bir sorunuz varsa, lütfen bana sorun. 😊

Kaynak: Bing ile konuşma, 25.08.2023
(1) libvirt: Storage Management. https://libvirt.org/storage.html.
(2) Windows 10 in KVM: change boot disk to Virtio - Super User. https://superuser.com/questions/1057959/windows-10-in-kvm-change-boot-disk-to-virtio.
(3) KVM: creating and reverting libvirt external snapshots. https://fabianlee.org/2021/01/10/kvm-creating-and-reverting-libvirt-external-snapshots/.
(4) undefined. https://me.m01.eu/blog/2015/03/windows-10-kvm-and-iscsi/.

---

Oluşturulan HDD'lerin Linux'ta hangi dizinde bulunduğunu soruyorsunuz. Bu sorunun cevabı, HDD'lerin hangi depolama havuzuna ait olduğuna bağlıdır. Eğer HDD'ler varsayılan depolama havuzuna aitse, yani `/var/lib/libvirt/images` dizinini kullanan bir dosya havuzuysa, bu dizinde bulunurlar. Eğer HDD'ler farklı bir depolama havuzuna aitse, o havuzun tanım dosyasını inceleyerek dizini öğrenebilirsiniz.

Örneğin, eğer HDD'ler `my-nfs-pool` adlı bir NFS havuzuna aitse, aşağıdaki komutu kullanarak havuzun tanım dosyasını görebilirsiniz:

`virsh pool-dumpxml my-nfs-pool`

Bu komutun çıktısında, `<target>` etiketi altında `<path>` etiketi bulabilirsiniz. Bu etiket, NFS paylaşımının Linux'ta hangi dizine bağlandığını gösterir. Bu dizin, HDD'lerin bulunduğu dizindir.

Umarım bu bilgiler size yardımcı olmuştur. Başka bir sorunuz varsa, lütfen bana sorun. 😊

---
Qemu ile bir sanal makine oluşturduğunuzu ve bu sanal makineyi başka zaman tekrar aynı ayarlarla ve konfigürasyonlarla oluşturabilmek istediğinizi söylüyorsunuz. Bu işlemi yapmanın birkaç yolu vardır:

- Sanal makinenin XML tanım dosyasını kaydedebilir ve daha sonra yeniden kullanabilirsiniz. XML dosyası, sanal makinenin tüm ayarlarını ve konfigürasyonlarını içerir. XML dosyasını kaydetmek için, aşağıdaki komutu kullanabilirsiniz:

`virsh dumpxml <vm-name> > <xml-file>`

Bu komut, `<vm-name>` adlı sanal makinenin XML tanımını `<xml-file>` adlı bir dosyaya yazar. Bu dosyayı başka bir sistemde kullanmak isterseniz, aşağıdaki komutu kullanabilirsiniz:

`virsh define <xml-file>`

Bu komut, `<xml-file>` adlı dosyadaki XML tanımına göre bir sanal makine oluşturur.

- Sanal makinenin disk imajını kaydedebilir ve daha sonra yeniden kullanabilirsiniz. Disk imajı, sanal makinenin tüm verilerini içerir. Disk imajını kaydetmek için, aşağıdaki komutu kullanabilirsiniz:

`qemu-img convert -O <format> <source-image> <target-image>`

Bu komut, `<source-image>` adlı disk imajını `<format>` adlı bir formata dönüştürerek `<target-image>` adlı bir dosyaya yazar. `<format>` olarak `qcow2`, `vmdk`, `vdi` veya `raw` gibi farklı seçenekler kullanabilirsiniz. Bu dosyayı başka bir sistemde kullanmak isterseniz, aşağıdaki komutu kullanabilirsiniz:

`qemu-system-x86_64 -hda <target-image>`

Bu komut, `<target-image>` adlı disk imajını kullanarak bir sanal makine başlatır.

Umarım bu bilgiler size yardımcı olmuştur. Başka bir sorunuz varsa, lütfen bana sorun. 😊

Kaynak: Bing ile konuşma, 25.08.2023
(1) Sanal Makine Görüntü Formatları Nasıl Dönüştürülür - Kerim Koç. https://kockerim.com/sanal-makine-goruntu-formatlari-nasil-donusturulur/.
(2) Çözüldü GPU Passthrough Yapılan Sanal Makine Açılmıyor. https://www.technopat.net/sosyal/konu/gpu-passthrough-yapilan-sanal-makine-acilmiyor.2351473/.
(3) Mobil Cihazlara Sanal Makine Kurulumu | TurkHackTeam. https://www.turkhackteam.org/konular/mobil-cihazlara-sanal-makine-kurulumu.2041019/.

---

