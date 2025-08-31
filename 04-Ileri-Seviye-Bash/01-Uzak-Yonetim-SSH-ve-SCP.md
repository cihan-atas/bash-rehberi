# 4.1. Uzak Yönetim: `ssh` ve `scp`

[↩️ Geri: İçindekiler](../README.md)

Modern IT altyapıları, genellikle birden çok sunucudan oluşur. Bu sunucuları yönetmek için her birinin başına fiziksel olarak gitmek imkansızdır. **SSH (Secure Shell)**, ağ üzerindeki başka bir bilgisayara güvenli bir şekilde bağlanıp, o bilgisayarın komut satırını (shell) sanki kendi bilgisayarınızdaymış gibi kullanmanızı sağlayan standart protokoldür.

## `ssh` - Güvenli Uzak Oturum

`ssh` komutu, uzak bir sunucuya şifrelenmiş bir bağlantı kurar. Bu, gönderdiğiniz komutların ve aldığınız çıktıların ağ üzerinde dinlenemeyeceği anlamına gelir.

**Temel Sözdizimi:**
```bash
ssh kullanici@sunucu_adresi
```
-   **`kullanici`**: Uzak sunucuda oturum açmak istediğiniz kullanıcı adı.
-   **`sunucu_adresi`**: Sunucunun IP adresi (örn: `192.168.1.100`) veya alan adı (örn: `webserver.example.com`).

**Örnek:**
```bash
ssh zeus@192.168.1.50
```
Bu komutu ilk kez çalıştırdığınızda, sunucunun kimliğini (`fingerprint`) doğrulamanızı isteyen bir soruyla karşılaşırsınız. `yes` yazıp devam ettikten sonra, `zeus` kullanıcısının parolasını girmeniz istenir. Başarılı bir şekilde giriş yaptığınızda, komut isteminiz uzak sunucunun istemiyle değişir. Artık yazdığınız her komut o sunucuda çalışacaktır. Oturumu kapatmak için `exit` yazmanız yeterlidir.

### Parolasız Oturum için SSH Anahtarları (En İyi Pratik)

Her seferinde parola girmek hem yorucu hem de otomasyon script'leri için imkansızdır. Daha güvenli ve pratik olan yöntem, **SSH anahtar çifti** kullanmaktır.

1.  **Anahtar Çifti Oluşturma (Sadece bir kez yapılır):**
    Kendi yerel makinenizde `ssh-keygen` komutunu çalıştırın. Genellikle varsayılan seçeneklere `Enter` basarak devam edebilirsiniz.
    ```bash
    ssh-keygen -t ed25519
    ```
    Bu komut, ev dizininizdeki `~/.ssh/` klasöründe iki dosya oluşturur:
    -   `id_ed25519` (veya `id_rsa`): **Özel anahtarınız (Private Key).** Bu dosyayı **ASLA** kimseyle paylaşmayın. Bu sizin kimliğinizdir.
    -   `id_ed25519.pub`: **Açık anahtarınız (Public Key).** Bu dosyayı, parolasız bağlanmak istediğiniz tüm sunuculara kopyalayacaksınız.

2.  **Açık Anahtarı Sunucuya Kopyalama:**
    `ssh-copy-id` komutu, bu işlemi sizin için otomatik ve güvenli bir şekilde yapar.
    ```bash
    ssh-copy-id zeus@192.168.1.50
    ```
    Bu komut, son bir kez sizden parola isteyecek ve ardından açık anahtarınızı uzak sunucudaki `~/.ssh/authorized_keys` dosyasına ekleyecektir.

Bu adımdan sonra, `ssh zeus@192.168.1.50` komutunu çalıştırdığınızda artık size parola sorulmayacaktır.

### Tek Komut Çalıştırma

`ssh` ile tam bir oturum açmak yerine, sadece tek bir komutu uzakta çalıştırıp çıktısını yerel terminalinizde görebilirsiniz.
```bash
# Uzak sunucudaki disk kullanımını kontrol et
ssh zeus@192.168.1.50 "df -h"

# Uzak sunucuyu yeniden başlat (sudo gerektirir ve sudoers ayarı ister)
# ssh zeus@192.168.1.50 "sudo reboot"
```
Bu özellik, otomasyon script'lerinde sunucu grupları üzerinde işlem yapmak için yaygın olarak kullanılır.

## `scp` - Güvenli Dosya Kopyalama

`scp` (secure copy), `ssh` protokolü üzerinden iki bilgisayar arasında dosya veya dizin kopyalamanızı sağlar. `cp` komutuna çok benzer, ancak kaynak veya hedeflerden biri `kullanici@sunucu:dosya_yolu` formatında belirtilir.

**Sözdizimi:**
-   Yerelden uzağa: `scp <yerel_dosya> kullanici@sunucu:<uzak_yol>`
-   Uzaktan yerele: `scp kullanici@sunucu:<uzak_dosya> <yerel_yol>`

**Örnekler:**

1.  **Yerel makinedeki `yedek.tar.gz` dosyasını uzak sunucunun ev dizinine kopyalama:**
    ```bash
    scp yedek.tar.gz zeus@192.168.1.50:~/
    ```
    (`:` sonrası boş bırakılırsa veya `~` kullanılırsa ev dizinine kopyalar).

2.  **Uzak sunucudaki `/var/log/nginx/access.log` dosyasını yerel makinedeki mevcut dizine kopyalama:**
    ```bash
    scp zeus@192.168.1.50:/var/log/nginx/access.log .
    ```
    (Sondaki `.` mevcut dizini ifade eder).

3.  **Bir dizini ve içeriğini kopyalamak için `-r` (recursive) parametresi kullanılır:**
    ```bash
    scp -r proje/ zeus@192.168.1.50:~/projeler/
    ```

> **💡 İpucu:** `rsync` komutu, `scp`'ye göre daha gelişmiş bir alternatiftir. Sadece değişen dosyaları veya dosyaların değişen kısımlarını göndererek daha hızlı transfer sağlar ve `-a` (archive) gibi kullanışlı parametreleri vardır. Genellikle büyük veya sık senkronize edilen dosyalar için `rsync` tercih edilir.

---
[➡️ Sonraki Konu: İşlem Yönetimi ve Arka Plan Görevleri](./02-Islem-Yonetimi-ve-Arka-Plan-Gorevleri.md)
