# 3.2. Değişkenler ve Parametreler

[↩️ Geri: İçindekiler](../README.md)

Bir script'i güçlü kılan şey, sabit komutları çalıştırması değil, farklı durumlara ve girdilere göre dinamik olarak davranabilmesidir. Bu dinamizmi **değişkenler** ve **parametreler** aracılığıyla sağlarız.

## Değişkenler

Değişkenler, bir değeri (metin, sayı vb.) saklamak için kullanılan isimlendirilmiş alanlardır.

### Değişken Tanımlama ve Değer Atama
Bash'te bir değişken tanımlamak için, değişken adını yazar ve `=` operatörü ile değerini atarsınız.
> **⚠️ ÖNEMLİ:** Değişken adı, `=` işareti ve değer arasında **kesinlikle boşluk olmamalıdır**.

```bash
# Doğru kullanım
kullanici="zeus"
port=22

# Yanlış kullanım (Hata verir!)
# kullanici = "zeus"
```

### Değişken Değerine Erişme
Bir değişkenin değerini kullanmak için, adının başına dolar işareti (`$`) koyarsınız.
```bash
#!/bin/bash

isim="Cihan"
echo "Merhaba, $isim"

# Değişkenleri süslü parantez {} içinde kullanmak,
# diğer metinlerle karışmasını önlemek için iyi bir pratiktir.
echo "Kullanıcının adı ${isim}dır."
```

### Komut Çıktısını Değişkene Atama
Bir komutun çıktısını bir değişkene atamak için komutu `$(...)` içine alırsınız. Buna "Command Substitution" denir.
```bash
#!/bin/bash

mevcut_dizin=$(pwd)
echo "Şu anki dizin: $mevcut_dizin"

aktif_kullanicilar=$(who | wc -l)
echo "Sistemde $aktif_kullanicilar aktif kullanıcı var."
```

## Parametreler (Pozisyonel Parametreler)

Parametreler, bir script'i çalıştırırken ona dışarıdan bilgi vermemizi sağlar. Script'e verilen argümanlar, özel değişkenlerde sırasıyla saklanır.

-   **`$0`**: Script'in kendi adı.
-   **`$1`**: Birinci parametre.
-   **`$2`**: İkinci parametre.
-   ...
-   **`$9`**: Dokuzuncu parametre.
-   **`${10}`**: Onuncu ve sonrası parametreler için süslü parantez kullanılmalıdır.

### Özel Parametre Değişkenleri
| Değişken | Anlamı |
| :--- | :--- |
| **`$#`** | Script'e verilen toplam parametre sayısı. |
| **`$@`** | Tüm parametreleri ayrı ayrı, tırnak işaretlerini koruyarak bir liste olarak temsil eder. **Genellikle en güvenli ve en çok tercih edilen yöntemdir.** |
| **`$*`** | Tüm parametreleri tek bir metin dizesi olarak birleştirir. |
| **`$?`** | En son çalışan komutun **çıkış kodunu (exit code)** tutar. `0` genellikle başarı, `0`'dan farklı bir değer ise hata anlamına gelir. |

### Parametreleri Kullanan Bir Script Örneği

Kullanıcıdan bir isim ve bir şehir alan ve buna göre bir selamlama mesajı yazan `selamla.sh` adında bir script oluşturalım.

**`selamla.sh` içeriği:**
```bash
#!/bin/bash

# Parametre sayısını kontrol edelim. 2'den az ise, hata ver ve çık.
if [ $# -lt 2 ]; then
    echo "Kullanım: $0 <isim> <şehir>"
    exit 1 # Hata kodu ile çık
fi

isim=$1
sehir=$2

echo "Merhaba $isim!"
echo "$sehir şehrini ziyaret etmeyi çok isterim."

echo "Bu script'e toplam $# parametre verildi."
```

**Script'i Çalıştırma:**
```bash
chmod +x selamla.sh

# Doğru kullanım
./selamla.sh Cihan İstanbul

# Eksik parametre ile kullanım
./selamla.sh Cihan
```

## Kullanıcıdan İnteraktif Girdi Alma: `read`

Bazen script çalışırken kullanıcıdan bilgi almak isteyebilirsiniz. `read` komutu bu işi yapar.

**Örnek:**
```bash
#!/bin/bash

echo "Lütfen adınızı girin:"
read kullanici_adi

echo "Merhaba, $kullanici_adi! Yaşınız kaç?"
read -p "Yaşınız: " yas # -p parametresi ile istemi aynı satırda gösterir

echo "$yas, başlamak için harika bir yaş!"
```

---
[➡️ Sonraki Konu: Akış Kontrolü: `if`, `for`, `while`](./03-Akis-Kontrolu-If-For-While.md)
