# 5.1. Log Analizi (`/var/log`, `journalctl`)

[↩️ Geri: İçindekiler](../README.md)

Bir Linux sisteminde olan biten hemen her önemli olay (kullanıcı oturum açmaları, servis hataları, ağ bağlantı denemeleri, güvenlik olayları) log dosyalarına kaydedilir. Mavi Takım için bu loglar, bir anormalliği tespit etmek, bir saldırıyı analiz etmek veya bir sistem sorununu teşhis etmek için en değerli bilgi kaynağıdır. Bu bölümde, bu logları nerede bulacağımızı ve `grep`, `awk`, `sed` gibi araçlarla nasıl analiz edeceğimizi öğreneceğiz.

## Geleneksel Log Dosyaları: `/var/log`

Çoğu Linux dağıtımında, sistem ve uygulama logları varsayılan olarak `/var/log` dizini altında toplanır. İşte en sık karşılaşacağınız bazı önemli log dosyaları:

| Dosya | İçerik |
| :--- | :--- |
| **`/var/log/syslog`** veya **`/var/log/messages`** | Sistem genelindeki olayların ve mesajların çoğu burada toplanır. Genellikle ilk bakılacak yerdir. (Debian/Ubuntu'da `syslog`, RHEL/CentOS'ta `messages`) |
| **`/var/log/auth.log`** veya **`/var/log/secure`** | Kimlik doğrulama ile ilgili tüm olaylar. Başarılı/başarısız oturum açma denemeleri, `sudo` kullanımları vb. (Debian/Ubuntu'da `auth.log`, RHEL/CentOS'ta `secure`) **Güvenlik analizi için kritiktir.** |
| **`/var/log/dmesg`** | Çekirdek (kernel) halka arabelleğindeki mesajlar. Özellikle sistem açılışı sırasında donanımla ilgili sorunları teşhis etmek için kullanılır. |
| **`/var/log/boot.log`** | Sistem başlangıç (boot) sürecindeki servislerin başlatılmasıyla ilgili mesajlar. |
| **`/var/log/apache2/access.log`** | (Eğer yüklüyse) Apache web sunucusuna gelen tüm isteklerin kaydı. |
| **`/var/log/nginx/access.log`** | (Eğer yüklüyse) Nginx web sunucusuna gelen tüm isteklerin kaydı. |

### Log Dosyalarını Analiz Etme Teknikleri

Daha önce öğrendiğimiz metin işleme araçları, log analizi için mükemmeldir.

-   **`tail` - Dosyanın Sonunu İzleme:** `tail` komutu bir dosyanın son 10 satırını gösterir. `-f` (follow) parametresi ile kullanıldığında, dosyaya yeni satırlar eklendikçe bunları gerçek zamanlı olarak ekrana basar. Canlı log takibi için vazgeçilmezdir.
    ```bash
    tail -f /var/log/syslog
    ```

-   **`grep` ile Hata Ayıklama:** Belirli bir anahtar kelimeyi (örn: "error", "failed", "denied") arayarak sorunları hızlıca tespit edebilirsiniz.
    ```bash
    # Kimlik doğrulama loglarında tüm başarısız denemeleri bul
    grep -i "failed" /var/log/auth.log

    # Sistem loglarında tüm hata ve uyarı mesajlarını bul
    grep -E -i "error|warn|critical" /var/log/syslog
    ```

-   **`awk` ile Raporlama:** `awk` kullanarak log dosyalarından yapılandırılmış bilgi çıkarabiliriz.
    **Senaryo:** `auth.log` dosyasından, başarısız parola denemesi yapan IP adreslerini sayalım ve en çok deneyen ilk 10 tanesini sıralayalım.
    ```bash
    grep "Failed password" /var/log/auth.log | \
      awk '{print $11}' | \
      sort | \
      uniq -c | \
      sort -nr | \
      head -n 10
    ```
    Bu komut zinciri, on binlerce satırlık bir log dosyasını saniyeler içinde anlamlı bir "Top 10 Saldırgan IP" raporuna dönüştürür.

## Modern Log Yönetimi: `systemd` ve `journalctl`

Modern Linux dağıtımlarının çoğu, `systemd` adı verilen bir sistem ve servis yöneticisi kullanır. `systemd`, logları `/var/log` altındaki metin dosyaları yerine, yapılandırılmış ve sıkıştırılmış bir ikili (binary) formatta, **journal** adı verilen merkezi bir veritabanında tutar.

Bu journalları sorgulamak için `journalctl` komutu kullanılır.

### `journalctl` Kullanmanın Avantajları

-   **Yapılandırılmış Veri:** Loglar sadece metin değil, aynı zamanda servis adı, PID, kullanıcı ID'si gibi metadata içerir. Bu, çok daha hassas filtreleme yapmanızı sağlar.
-   **Hız:** İkili format ve indeksleme sayesinde sorgulama genellikle metin dosyalarını `grep`'lemekten daha hızlıdır.
-   **Merkezi Olması:** Çekirdek, servisler, uygulamalar dahil sistemdeki hemen her şeyin logu tek bir yerde toplanır.

### Temel `journalctl` Komutları

-   **Tüm logları görüntüleme (en yeniden en eskiye):**
    ```bash
    journalctl
    ```
-   **Gerçek zamanlı log takibi (`-f`):**
    ```bash
    journalctl -f
    ```
-   **Belirli bir servisin loglarını filtreleme (`-u`):**
    ```bash
    # Sadece ssh servisinin loglarını göster
    journalctl -u ssh.service
    ```
-   **Zamana göre filtreleme (`--since`, `--until`):**
    ```bash
    # Son 1 saatteki logları göster
    journalctl --since "1 hour ago"

    # Belirli bir tarihten itibaren olan logları göster
    journalctl --since "2024-10-26 10:00:00"
    ```
-   **Önceliğe (priority) göre filtreleme (`-p`):**
    Hata seviyesine göre logları filtreler (`err`, `warning`, `notice`, `info`, `debug`).
    ```bash
    # Sadece hata (error) seviyesindeki logları göster
    journalctl -p err
    ```
-   **Çıktı Formatını Değiştirme (`-o`):**
    Logları analiz için JSON formatında almak çok kullanışlıdır.
    ```bash
    journalctl -u nginx -o json-pretty
    ```

---
[➡️ Sonraki Konu: Sistem İzleme ve Auditing](./02-Sistem-Izleme-ve-Auditing.md)
