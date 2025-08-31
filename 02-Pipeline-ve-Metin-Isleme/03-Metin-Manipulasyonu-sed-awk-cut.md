# 2.3. Metin Manipülasyonu: `cut`, `sed` ve `awk`

[↩️ Geri: İçindekiler](../README.md)

Bash'te her şeyin bir metin akışı olduğunu söylemiştik. `grep` ile bu akıştan istediğimiz satırları filtreledik. Şimdi ise bu satırları alıp parçalara ayırma, belirli sütunları seçme ve içeriği anında değiştirme gibi daha gelişmiş metin manipülasyonu tekniklerini öğreneceğiz. Bu işler için `cut`, `sed` ve `awk` üçlüsü vazgeçilmezdir.

## `cut` - Metni Sütunlara Ayırma

`cut` komutu, adından da anlaşılacağı gibi, bir metnin her satırını belirli bir ayraç (delimiter) karakterine göre sütunlara ayırır ve istediğiniz sütunları "kesip" alır.

**En Sık Kullanılan Parametreler:**
-   **`-d` (delimiter):** Sütunları neye göre ayıracağını belirtir. Varsayılanı TAB karakteridir.
-   **`-f` (fields):** Hangi sütun(lar)ın seçileceğini belirtir.

**Örnek:** `/etc/passwd` dosyası `:` karakteriyle ayrılmış sütunlardan oluşur. Bu dosyadan sadece kullanıcı adlarını (1. sütun) ve ev dizinlerini (6. sütun) alalım.

```bash
cat /etc/passwd | cut -d':' -f1,6
```
**Ne Oldu?**
1.  `cat /etc/passwd` dosyanın içeriğini pipeline'a gönderdi.
2.  `cut` komutu bu içeriği aldı.
3.  `-d':'` parametresi, her satırı `:` karakterinden bölmesini söyledi.
4.  `-f1,6` parametresi, sadece 1. ve 6. sütunları seçmesini söyledi.

## `sed` - Stream Editor (Akış Editörü)

`sed`, bir metin akışı üzerinde "bul ve değiştir" (find and replace) işlemleri yapmak için kullanılan çok güçlü bir araçtır. Dosyaları doğrudan düzenlemez (bunu yapabilir ama varsayılan davranışı bu değildir), bunun yerine metni okur, değişiklikleri yapar ve sonucu `stdout`'a yazar.

**Temel Sözdizimi:**
```bash
sed 's/bulunacak_desen/yeni_değer/g'
```
-   **`s`**: Substitute (değiştir) komutu.
-   **`/`**: Desenleri ayıran karakter (başka bir karakter de olabilir: `sed 's#eski#yeni#g'`).
-   **`g`**: Global. Satır içinde sadece ilk eşleşmeyi değil, **tüm** eşleşmeleri değiştirir.

**Örnek:** Bir metin dosyasındaki tüm "error" kelimelerini "WARNING" olarak değiştirelim.
```bash
# Dosya üzerinde simülasyon yapalım
cat log.txt | sed 's/error/WARNING/g'

# Değişikliği doğrudan dosyaya kaydetmek için -i (in-place) parametresi kullanılır (DİKKATLİ OLUN!)
# sed -i 's/error/WARNING/g' log.txt
```

## `awk` - Bir Metin İşleme Programlama Dili

`awk`, basit bir komuttan çok, kendi içinde bir programlama dilidir. Metni satır satır ve sütun sütun işlemek için tasarlanmıştır. `cut`'ın yapabildiği her şeyi ve çok daha fazlasını yapabilir.

`awk`'da her satır otomatik olarak boşluk karakterlerine göre sütunlara ayrılır. Bu sütunlara `$1`, `$2`, `$3`, ... şeklinde erişilir. `$0` ise satırın tamamını temsil eder.

**Temel Sözdizimi:**
```bash
awk '{ print $1, $3 }'
```

**Örnek 1:** `ls -l` komutunun çıktısından sadece izinleri (1. sütun) ve dosya adını (9. sütun) alalım.
```bash
ls -l | awk '{ print $1, $9 }'
```

**Örnek 2: Koşullu İşlemler ve Hesaplama**
`awk`'ın asıl gücü, desen eşleştirme ve koşullu ifadelerle ortaya çıkar. Örneğin, disk kullanımını (`df -h`) listeleyip, kullanım oranı (5. sütun) %80'den fazla olanları yazdıralım.

```bash
# df -h çıktısının 5. sütunundaki '%' işaretini kaldırıp, sayıyı 80 ile karşılaştır
df -h | awk '$5+0 > 80 { print $0 }'
```
-   `$5+0 > 80`: `awk`'ın bir metin sütununu sayısal olarak karşılaştırmasını sağlamak için yaygın bir hiledir.
-   `{ print $0 }`: Eğer koşul doğruysa, satırın tamamını yazdır.

## `cut` mı, `awk` mı?

-   **`cut`:** Sadece belirli bir karaktere göre ayrılmış veriden basitçe sütun(lar) almanız gerektiğinde hızlı ve kolaydır.
-   **`awk`:** Sütunlar arasında boşluklar varsa, sütunlar üzerinde matematiksel veya mantıksal işlemler yapmanız gerekiyorsa veya çıktıyı daha karmaşık bir şekilde formatlamak istiyorsanız `awk` çok daha güçlü ve esnektir.

Bu üç araç, Bash'te metin işlemenin temelini oluşturur ve log analizi, rapor oluşturma gibi sayısız otomasyon görevinde birlikte kullanılırlar.

---
[➡️ Sonraki Konu: Komutları Birleştirme: `xargs` ve `find`](./04-Komutlari-Birlestirme-xargs-ve-find.md)
