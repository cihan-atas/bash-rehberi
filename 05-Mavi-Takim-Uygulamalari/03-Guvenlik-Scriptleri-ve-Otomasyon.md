# 5.3. Güvenlik Script'leri ve Otomasyon

[↩️ Geri: İçindekiler](../README.md)

Şimdiye kadar, bir Linux sistemini izlemek ve anormallikleri tespit etmek için birçok güçlü komut öğrendik. Ancak bu komutları her gün manuel olarak çalıştırmak hem zaman alıcı hem de hataya açıktır. Mavi Takım'ın gücü, bu tekrarlanan güvenlik kontrollerini **otomatikleştiren** script'ler yazmaktan gelir.

Bu bölümde, öğrendiklerimizi birleştirerek, sistemde temel güvenlik kontrolleri yapan basit ama etkili Bash script'leri oluşturacağız. Bu script'ler, `cron` ile zamanlanarak düzenli olarak çalıştırılabilir ve sonuçları bir log dosyasına veya e-posta ile sistem yöneticisine gönderilebilir.

## Örnek Script 1: Temel Sistem Sağlamlık Kontrolü

Bu script, bir sunucuda hızlı bir "sağlamlık kontrolü" (sanity check) yaparak, en yaygın şüpheli göstergeleri arar.

**`health_check.sh`:**
```bash
#!/bin/bash
set -euo pipefail

# Rapor dosyasının adını tarihle birlikte oluştur
RAPOR_DOSYASI="/var/log/security_health_check_$(date +%Y-%m-%d).log"

# Rapor fonksiyonu
logla() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] - $1" >> "$RAPOR_DOSYASI"
}

# --- KONTROLLER BAŞLIYOR ---
echo "" > "$RAPOR_DOSYASI" # Her çalıştığında rapor dosyasını temizle
logla "Sistem Sağlamlık Kontrolü Başlatıldı."

# 1. Beklenmedik dinleme portları var mı?
logla "--- Dinlemedeki TCP Portları ---"
ss -tlnp >> "$RAPOR_DOSYASI"

# 2. /tmp dizininde çalıştırılabilir dosyalar var mı? (Genellikle şüphelidir)
logla "--- /tmp Dizinindeki Çalıştırılabilir Dosyalar ---"
find /tmp -type f -perm /a=x >> "$RAPOR_DOSYASI" 2>/dev/null

# 3. Son 24 saatte auth.log dosyasındaki başarısız oturum denemeleri
logla "--- Son 24 Saatteki Başarısız Oturum Denemeleri ---"
grep "Failed password" /var/log/auth.log | \
  grep "$(date --date='1 day ago' +'%b %e')" >> "$RAPOR_DOSYASI" || \
  logla "Başarısız oturum denemesi bulunamadı."

# 4. Kök (root) kullanıcısına ait cron görevleri
logla "--- Root Kullanıcısının Cron Görevleri ---"
crontab -l -u root >> "$RAPOR_DOSYASI" 2>/dev/null || \
  logla "Root kullanıcısı için cron görevi bulunamadı."

# 5. SUID biti ayarlı dosyalar (Yetki yükseltme için kullanılabilir)
logla "--- SUID Biti Ayarlı Dosyalar ---"
find / -type f -perm -4000 >> "$RAPOR_DOSYASI" 2>/dev/null

logla "Sistem Sağlamlık Kontrolü Tamamlandı."

# İsteğe bağlı: Raporu e-posta ile gönder
# mail -s "Güvenlik Raporu: $(hostname)" admin@example.com < "$RAPOR_DOSYASI"
```

**Bu Script Nasıl Çalışır?**
1.  Her çalıştığında, o günün tarihini taşıyan bir rapor dosyası oluşturur.
2.  `logla` adında bir fonksiyon, tüm çıktıları standart bir formatta bu dosyaya yazar.
3.  Sistemde dinlemede olan portları listeler.
4.  Saldırganların sıkça kullandığı `/tmp` dizininde çalıştırılabilir dosya olup olmadığını kontrol eder.
5.  Kimlik doğrulama loglarında son 24 saatteki başarısız denemeleri arar. `||` operatörü, `grep` komutu hiçbir şey bulamazsa (hata kodu verirse) "bulunamadı" mesajı yazmasını sağlar.
6.  Root kullanıcısının zamanlanmış görevlerini listeler.
7.  Yetki yükseltme saldırılarında kullanılabilecek SUID bitine sahip dosyaları bulur.

**Nasıl Otomatize Edilir?**
Bu script'i `chmod +x health_check.sh` ile çalıştırılabilir yaptıktan sonra, `root` kullanıcısının `crontab`'ına ekleyerek her gece çalışmasını sağlayabilirsiniz:
```crontab
# Her gece saat 03:00'te sistem sağlık kontrolünü çalıştır
0 3 * * * /usr/local/sbin/health_check.sh
```

## Örnek Script 2: Dosya Bütünlüğü İzleme

Bu script, önemli sistem dosyalarının (örn: `/etc/passwd`, `/etc/hosts`) değiştirilip değiştirilmediğini kontrol eden basit bir "dosya bütünlüğü monitörü" (File Integrity Monitor - FIM) görevi görür.

**`integrity_check.sh`:**
```bash
#!/bin/bash
set -euo pipefail

# Kontrol edilecek dosyaların listesi
IZLENECEK_DOSYALAR=("/etc/passwd" "/etc/hosts" "/etc/ssh/sshd_config")

# Hash'lerin saklanacağı veritabanı dosyası
HASH_VERITABANI="/var/lib/integrity_db.txt"

# Eğer veritabanı dosyası yoksa, ilk referans (baseline) oluşturulur
if [[ ! -f "$HASH_VERITABANI" ]]; then
    echo "İlk referans veritabanı oluşturuluyor..."
    for dosya in "${IZLENECEK_DOSYALAR[@]}"; do
        sha256sum "$dosya" >> "$HASH_VERITABANI"
    done
    echo "Veritabanı oluşturuldu. Script bir sonraki çalışmada kontrol edecektir."
    exit 0
fi

# Kontrol döngüsü
echo "Dosya bütünlüğü kontrol ediliyor..."
degisiklik_var=0
while read -r satir; do
    # Veritabanı satırını hash ve dosya yoluna ayır
    kayitli_hash=$(echo "$satir" | awk '{print $1}')
    dosya_yolu=$(echo "$satir" | awk '{print $2}')

    # Mevcut dosyanın hash'ini hesapla
    mevcut_hash=$(sha256sum "$dosya_yolu" | awk '{print $1}')

    if [[ "$kayitli_hash" != "$mevcut_hash" ]]; then
        echo "UYARI: '$dosya_yolu' dosyasında değişiklik tespit edildi!"
        degisiklik_var=1
    fi
done < "$HASH_VERITABANI"

if [[ $degisiklik_var -eq 0 ]]; then
    echo "Tüm dosyalar bütünlüğünü koruyor."
fi
```
Bu script, izlenen dosyaların `sha256` hash'lerini bir dosyada saklar ve her çalıştığında mevcut hash'lerle karşılaştırır. Bir değişiklik olduğunda uyarı verir.

---
[➡️ Sonraki Bölüm: Kırmızı Takım Uygulamaları](../06-Kirmizi-Takim-Uygulamalari/01-Kesif-ve-Bilgi-Toplama.md)
