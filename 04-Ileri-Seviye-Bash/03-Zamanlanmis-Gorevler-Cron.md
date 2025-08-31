# 4.3. Zamanlanmış Görevler: `cron`

[↩️ Geri: İçindekiler](../README.md)

Otomasyonun en önemli parçalarından biri, görevleri manuel olarak başlatmak yerine, belirli bir zamanda veya düzenli aralıklarla (örneğin, her gece, her saat başı) otomatik olarak çalışacak şekilde planlamaktır. Linux sistemlerinde bu görevi yerine getiren emektar ve son derece güvenilir araca **cron** denir.

`cron`, arka planda sürekli çalışan bir "daemon" (servis) işlemidir ve `crontab` adı verilen özel bir dosyayı sürekli kontrol ederek, zamanı gelen komutları çalıştırır.

## `crontab` Dosyasını Yönetme

Her kullanıcının (root dahil) kendi `crontab` dosyası olabilir. Bu dosyayı yönetmek için `crontab` komutu kullanılır.

-   **`crontab -e`**: Mevcut kullanıcının `crontab` dosyasını düzenlemek için açar. İlk kez çalıştırdığınızda, `nano` gibi bir metin editörü seçmenizi isteyebilir.
-   **`crontab -l`**: Mevcut kullanıcının `crontab` dosyasının içeriğini listeler.
-   **`crontab -r`**: Mevcut kullanıcının `crontab` dosyasını **komple siler**. (DİKKATLİ KULLANIN!)

## `crontab` Sözdizimini (Syntax) Anlama

Bir `crontab` dosyasına eklenen her satır, bir zamanlanmış görevi temsil eder ve iki ana bölümden oluşur: **zamanlama ifadesi** ve **çalıştırılacak komut**.

```
# ┌───────────── dakika (0 - 59)
# │ ┌───────────── saat (0 - 23)
# │ │ ┌───────────── ayın günü (1 - 31)
# │ │ │ ┌───────────── ay (1 - 12)
# │ │ │ │ ┌───────────── haftanın günü (0 - 6) (Pazar=0 veya 7)
# │ │ │ │ │
# * * * * *  /çalıştırılacak/komutun/tam/yolu
```

### Zamanlama İfadesindeki Özel Karakterler

-   **`*` (Yıldız):** "Her" anlamına gelir. Dakika sütunundaki `*`, "her dakika" demektir.
-   **`,` (Virgül):** Değer listesi belirtir. `1,15,30` dakika sütununda, "1., 15. ve 30. dakikalarda" demektir.
-   **`-` (Tire):** Değer aralığı belirtir. `9-17` saat sütununda, "sabah 9 ile akşam 5 (dahil) arasında her saat" demektir.
-   **`/` (Slash):** Adım değeri belirtir. `*/15` dakika sütununda, "her 15 dakikada bir" (0, 15, 30, 45) demektir.

### Pratik `crontab` Örnekleri

-   **Her gece saat 02:30'da bir yedekleme script'i çalıştır:**
    ```crontab
    30 2 * * * /home/zeus/scripts/yedekle.sh
    ```

-   **Her saat başı bir sistem sağlık kontrolü script'i çalıştır:**
    ```crontab
    0 * * * * /home/zeus/scripts/saglik_kontrol.sh
    ```

-   **Her 5 dakikada bir bir komut çalıştır:**
    ```crontab
    */5 * * * * /usr/bin/php /var/www/html/cron.php
    ```

-   **Her Pazartesi sabahı saat 9:00'da bir rapor oluştur:**
    ```crontab
    0 9 * * 1 /home/zeus/scripts/haftalik_rapor.sh
    ```

## `cron` ile İlgili Önemli İpuçları ve En İyi Pratikler

1.  **Her Zaman Tam Yolları (Absolute Paths) Kullanın:** `cron`'un kendi çalışma ortamı (environment) çok kısıtlıdır ve sizin normalde terminalde eriştiğiniz `PATH` değişkenine sahip olmayabilir. Bu nedenle, hem çalıştırılacak script'in hem de script içindeki komutların tam yolunu belirtmek en güvenli yoldur.
    - **Kötü:** `yedekle.sh`
    - **İyi:** `/home/zeus/scripts/yedekle.sh`

2.  **Çıktıyı Yönlendirin:** `cron` tarafından çalıştırılan bir komutun ürettiği çıktılar (stdout ve stderr), varsayılan olarak sistemin mail servisine gönderilir. Bu istenmeyen bir durum olabilir. Çıktıyı bir log dosyasına yönlendirmek veya `/dev/null`'a göndererek yok saymak iyi bir pratiktir.
    ```crontab
    # Hem normal çıktıyı hem de hataları bir log dosyasına ekle
    * * * * * /path/to/script.sh >> /var/log/my_script.log 2>&1

    # Tüm çıktıları yok say
    * * * * * /path/to/script.sh > /dev/null 2>&1
    ```

3.  **Dosya İzinlerini Kontrol Edin:** `cron`'a eklediğiniz script'in **çalıştırılabilir (`+x`)** izne sahip olduğundan emin olun.
    ```bash
    chmod +x /home/zeus/scripts/yedekle.sh
    ```

4.  **Sistem `crontab`'ı:** Sistem genelindeki görevler (örneğin, log rotasyonu) genellikle `/etc/crontab` dosyasında veya `/etc/cron.d/`, `/etc/cron.hourly/`, `/etc/cron.daily/` gibi dizinlerde yönetilir. Bu dosyaları düzenlemek için root yetkisi gerekir.

---
[➡️ Sonraki Konu: Ağ Araçları](./04-Ag-Araclari.md)
