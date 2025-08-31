# 1.2. Temel Komutlar ve Dosya Sistemi

[↩️ Geri: İçindekiler](../README.md)

Komut satırında çalışmak, bir dosya sistemi içinde gezinmek ve dosyaları yönetmek demektir. Bu bölümde, Linux'un hiyerarşik dosya sistemi yapısını anlayacak ve bu sistem içinde hareket etmenizi, dosyaları listelemenizi, oluşturmanızı, taşımanızı, kopyalamanızı ve silmenizi sağlayan temel komutları öğreneceksiniz.

## Linux Dosya Sistemi Yapısı

Windows'taki `C:\`, `D:\` gibi sürücü harflerinin aksine, Linux'ta her şey tek bir kök dizin olan **`/`** (slash) altında başlar. Tüm diskler, cihazlar ve klasörler bu ağaç yapısının bir dalı olarak görünür.

İşte bazı önemli dizinler ve anlamları:

| Dizin | Açıklama |
| :--- | :--- |
| **`/`** | **Kök Dizin:** Tüm dosya sisteminin başlangıç noktası. |
| **`/home`** | Kullanıcıların kişisel "ev" dizinlerinin bulunduğu yer (örn: `/home/zeus`). |
| **`/bin`** | Temel kullanıcı komut dosyalarının (binary) bulunduğu yer (`ls`, `cp` gibi). |
| **`/etc`** | Sistem genelindeki yapılandırma (konfigürasyon) dosyalarının bulunduğu yer. |
| **`/var`** | Değişken verilerin tutulduğu yer (loglar `/var/log` altında, web dosyaları `/var/www` altında olabilir). |
| **`/tmp`** | Geçici dosyaların tutulduğu ve sistem yeniden başladığında genellikle temizlenen dizin. |

## Dosya Yolları (Paths): Mutlak ve Göreceli

-   **Mutlak Yol (Absolute Path):** Her zaman kök dizin olan `/` ile başlar ve bir dosyanın veya dizinin tam konumunu belirtir. Nerede olursanız olun, mutlak yol sizi her zaman aynı yere götürür.
    - Örnek: `/home/zeus/Desktop`, `/etc/ssh/sshd_config`

-   **Göreceli Yol (Relative Path):** Bulunduğunuz mevcut dizine göre bir konumu belirtir. `/` ile başlamaz.
    - `.` (tek nokta): Mevcut dizini ifade eder.
    - `..` (iki nokta): Bir üst dizini ifade eder.
    - Örnek: Eğer `/home/zeus` dizinindeyseniz, `Desktop` yazmak sizi `/home/zeus/Desktop` dizinine götürür. `../..` yazmak sizi `/` kök dizinine götürür.

## Temel Gezinme ve Yönetim Komutları

### `pwd` - Print Working Directory
"Şu anda neredeyim?" sorusunun cevabıdır. Mevcut çalışma dizininizin tam (mutlak) yolunu ekrana basar.
```bash
pwd
```

### `ls` - List
Bir dizinin içeriğini (dosyalar ve alt dizinler) listeler.
```bash
# Mevcut dizinin içeriğini listele
ls

# Belirli bir dizinin içeriğini listele
ls /etc
```
**En Sık Kullanılan `ls` Parametreleri:**

| Parametre | Açıklama | Örnek |
| :--- | :--- | :--- |
| **`-l`** | **L**ong (Uzun) formatta listeler. İzinler, sahip, boyut, tarih gibi detayları gösterir. | `ls -l` |
| **`-a`** | **A**ll (Tümü). Gizli dosyalar (`.` ile başlayanlar) dahil her şeyi gösterir. | `ls -a` |
| **`-h`** | **H**uman (İnsan) okunabilir format. `-l` ile kullanıldığında dosya boyutlarını (örn: 4K, 12M, 2G) gösterir. | `ls -lh` |
| **`-t`** | **T**ime (Zaman). Dosyaları en son değiştirilme tarihine göre sıralar (en yeni en üstte). | `ls -lt` |

> **💡 İpucu:** Parametreler birleştirilebilir. Örneğin, `ls -lah` komutu tüm dosyaları, detaylı ve insan okunabilir formatta listeler.

### `cd` - Change Directory
Dizinler arasında geçiş yapmanızı sağlar.
```bash
# Mutlak yol ile /var/log dizinine git
cd /var/log

# Göreceli yol ile bir üst dizine çık
cd ..

# Hiçbir şey yazmadan sadece 'cd' komutunu kullanmak sizi ev dizininize (~/) götürür
cd
```

### `mkdir` - Make Directory
Yeni bir dizin (klasör) oluşturur.
```bash
mkdir yeni-projem
```

### `touch` - Create Empty File
Boş bir dosya oluşturur veya var olan bir dosyanın son değiştirilme tarihini günceller.
```bash
touch rapor.txt
```

### `cp` - Copy
Dosyaları veya dizinleri kopyalar.
```bash
# Bir dosyayı aynı dizinde farklı bir isimle kopyala
cp rapor.txt rapor_yedek.txt

# Bir dosyayı başka bir dizine kopyala
cp rapor.txt ~/Desktop/

# Bir dizini ve içindekileri kopyalamak için -r (recursive) parametresi kullanılır
cp -r yeni-projem/ yeni-projem_yedek/
```

### `mv` - Move
Dosyaları veya dizinleri taşır. **Aynı zamanda yeniden adlandırmak için de kullanılır.**
```bash
# Dosyayı yeniden adlandır
mv rapor.txt son_rapor.txt

# Dosyayı başka bir dizine taşı
mv son_rapor.txt ~/Documents/
```

### `rm` - Remove
Dosyaları siler.
```bash
rm son_rapor.txt
```
> **⚠️ DİKKAT:** `rm` komutu dosyaları **kalıcı olarak** siler. Geri Dönüşüm Kutusu yoktur!
>
> - `rm -r dizin_adi`: Bir dizini ve içindeki her şeyi siler.
> - `rm -rf dizin_adi`: **ÇOK TEHLİKELİDİR.** Sormadan, zorla bir dizini ve içindeki her şeyi siler. Bu komutu kullanırken iki kez, hatta üç kez düşünün.

---
[➡️ Sonraki Konu: Yardım Alma: `man` ve `apropos`](./03-Yardim-Alma-man-ve-apropos.md)
