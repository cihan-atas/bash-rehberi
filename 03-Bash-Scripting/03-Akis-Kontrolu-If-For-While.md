# 3.3. Akış Kontrolü: `if`, `for`, `while`

[↩️ Geri: İçindekiler](../README.md)

Script'lerimize "beyin" ekleyerek karar vermelerini ve "kas" ekleyerek işlemleri tekrarlamalarını sağlayan akış kontrol yapıları, otomasyonun temelini oluşturur. Bu bölümde Bash'teki en temel kontrol yapıları olan `if`, `for` ve `while`'ı öğreneceğiz.

## Koşullu İfadeler: `if` / `elif` / `else`

`if` ifadesi, bir koşulun doğru olup olmadığını test eder ve sonuca göre belirli bir kod bloğunu çalıştırır.

**Sözdizimi:**
```bash
if [[ koşul ]]; then
    # Koşul doğruysa bu blok çalışır
elif [[ başka_bir_koşul ]]; then
    # Önceki koşul yanlış ama bu doğruysa bu blok çalışır
else
    # Hiçbir koşul doğru değilse bu blok çalışır
fi
```
-   **`if`** bloğu ile başlar, **`fi`** ("if" kelimesinin tersi) ile biter.
-   **`[[ ... ]]`**: Koşulları test etmek için kullanılan yapıdır. Tek köşeli parantez `[ ... ]` de kullanılabilir, ancak çift köşeli parantez `[[ ... ]]` daha modern, daha güvenli ve daha fazla özellik sunduğu için genellikle tercih edilir.
-   **`; then`**: Koşuldan sonra `then` anahtar kelimesi aynı satırda kullanılacaksa araya noktalı virgül konur. Ayrı satırlarda da yazılabilir.

### Temel Test Operatörleri
`[[ ... ]]` içinde en sık kullanılan test operatörleri şunlardır:

**Dosya Testleri:**
| Operatör | Anlamı |
| :--- | :--- |
| `-e dosya` | Dosya var mı? |
| `-f dosya` | Dosya normal bir dosya mı? |
| `-d dizin` | Dosya bir dizin mi? |
| `-r dosya` | Dosya okunabilir mi? |
| `-w dosya` | Dosya yazılabilir mi? |
| `-x dosya` | Dosya çalıştırılabilir mi? |

**Metin (String) Karşılaştırmaları:**
| Operatör | Anlamı |
| :--- | :--- |
| `str1 == str2` | Metinler eşit mi? |
| `str1 != str2` | Metinler eşit değil mi? |
| `-z str` | Metin boş mu? (Zero length) |
| `-n str` | Metin boş değil mi? (Non-zero length) |

**Sayısal Karşılaştırmalar:**
| Operatör | Anlamı |
| :--- | :--- |
| `num1 -eq num2`| Eşittir (Equals) |
| `num1 -ne num2`| Eşit değildir (Not Equals) |
| `num1 -gt num2`| Büyüktür (Greater Than) |
| `num1 -ge num2`| Büyük veya eşittir (Greater or Equal) |
| `num1 -lt num2`| Küçüktür (Less Than) |
| `num1 -le num2`| Küçük veya eşittir (Less or Equal) |

**Örnek:** Bir dosyanın varlığını kontrol eden bir script.
```bash
#!/bin/bash

dosya_yolu="/etc/passwd"

if [[ -e $dosya_yolu ]]; then
    echo "$dosya_yolu dosyası bulundu."
    if [[ -r $dosya_yolu ]]; then
        echo "Ve dosya okunabilir."
    else
        echo "Ama dosya okunabilir değil."
    fi
else
    echo "$dosya_yolu dosyası bulunamadı."
fi
```

## Döngüler: `for` ve `while`

### `for` Döngüsü
Bir listedeki (kelimeler, dosyalar, sayılar vb.) her bir öğe için bir kod bloğunu tekrarlamak amacıyla kullanılır.

**Sözdizimi:**
```bash
for degisken in liste; do
    # Her bir öğe için çalışacak kodlar
    # $degisken ile o anki öğeye erişilir
done
```

**Örnek:** Sunucu listesindeki her sunucuya bağlanmayı deneyelim.
```bash
#!/bin/bash

sunucular="web01 db01 app01"

for sunucu in $sunucular; do
    echo "--------------------------"
    echo "$sunucu sunucusuna ping atılıyor..."
    ping -c 1 $sunucu
done

echo "Tüm sunucular kontrol edildi."
```

### `while` Döngüsü
Belirli bir koşul doğru (`true`) olduğu sürece bir kod bloğunu tekrarlamak için kullanılır. Koşul her döngünün başında kontrol edilir.

**Sözdizimi:**
```bash
while [[ koşul ]]; do
    # Koşul doğru olduğu sürece çalışacak kodlar
done
```

**Örnek:** 1'den 5'e kadar sayan bir sayaç.
```bash
#!/bin/bash

sayac=1
while [[ $sayac -le 5 ]]; do
    echo "Sayı: $sayac"
    # Sayacı bir artırmazsak sonsuz döngüye girer!
    ((sayac++))
done
```

### Pratik Bir Örnek: Sunucu Durum Kontrol Script'i
Bu script, bir listedeki sunucuların çevrimiçi olup olmadığını kontrol eder ve sonucu raporlar.
```bash
#!/bin/bash

sunucular=("8.8.8.8" "1.1.1.1" "192.168.1.254") # Ulaşılamayan bir IP de ekledik

echo "Sunucu durum kontrolü başlıyor..."

for sunucu in "${sunucular[@]}"; do
    # -c 1: Sadece bir paket gönder
    # -W 1: Cevap için 1 saniye bekle
    # > /dev/null: Ping komutunun normal çıktısını gizle
    ping -c 1 -W 1 "$sunucu" > /dev/null 2>&1

    # $? değişkeni en son komutun çıkış kodunu tutar.
    # 0 başarı, diğerleri hata demektir.
    if [[ $? -eq 0 ]]; then
        echo "✅ $sunucu -> ÇEVRİMİÇİ"
    else
        echo "❌ $sunucu -> ÇEVRİMDIŞI"
    fi
done

echo "Kontrol tamamlandı."
```

---
[➡️ Sonraki Konu: Fonksiyonlar ve Hata Yönetimi](./04-Fonksiyonlar-ve-Hata-Yonetimi.md)
