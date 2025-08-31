# 2.4. Komutları Birleştirme: `xargs` ve `find`

[↩️ Geri: İçindekiler](../README.md)

Şimdiye kadar pipeline (`|`) kullanarak bir komutun çıktısını diğerine yönlendirmeyi öğrendik. Ancak bazı komutlar, girdiyi doğrudan pipeline'dan (stdin) okuyacak şekilde tasarlanmamıştır; bunun yerine argüman olarak almayı beklerler (örn: `rm dosya1 dosya2`). İşte bu noktada `xargs` devreye girer. `xargs`, stdin'den gelen metni okur ve onu bir sonraki komuta argüman olarak geçirir.

Bu bölümde, dosya aramanın en güçlü aracı olan `find` ile `xargs`'ın nasıl bir araya gelerek güçlü otomasyonlar oluşturduğunu göreceğiz.

## `find` - Dosya ve Dizin Arama

`find` komutu, bir dosya sisteminde belirli kriterlere (isim, boyut, tarih, izinler vb.) uyan dosyaları veya dizinleri bulmak için kullanılır. `ls` veya `grep`'ten çok daha esnek ve güçlüdür.

**Temel Sözdizimi:**
```bash
find <nerede_aranacak> <kriterler> <eylem>
```

**Yaygın Kriterler (`-` ile başlayanlar):**
-   **`-name "desen"`:** Ada göre arama yapar. Joker karakterler (`*`, `?`) kullanılabilir.
-   **`-type [f|d|l]`:** Türe göre arama yapar (`f`: dosya, `d`: dizin, `l`: link).
-   **`-size [+|-]boyut`:** Boyuta göre arama yapar (`+`: daha büyük, `-`: daha küçük). Boyut `c`(byte), `k`(KB), `M`(MB), `G`(GB) ile belirtilir.
-   **`-mtime [+|-]gun`:** Son değiştirilme zamanına göre arama yapar (`+`: daha eski, `-`: daha yeni).
-   **`-user kullanici_adi`:** Belirli bir kullanıcıya ait dosyaları arar.
-   **`-perm mod`:** İzinlere göre arama yapar.

**Örnekler:**

1.  **Mevcut dizinde ve alt dizinlerde tüm `.log` uzantılı dosyaları bul:**
    ```bash
    find . -name "*.log"
    ```
    Buradaki `.` mevcut dizini ifade eder.

2.  **/home dizininde, boyutu 100MB'dan büyük olan dosyaları bul:**
    ```bash
    find /home -type f -size +100M
    ```

3.  **/tmp dizininde, son 7 günden daha eski olan dosyaları bul:**
    ```bash
    find /tmp -type f -mtime +7
    ```

## `xargs` - Argümanları Oluştur ve Çalıştır

`xargs`, standart girdiden (stdin) gelen veriyi okur, onu parçalara ayırır ve başka bir komuta komut satırı argümanı olarak gönderir.

**Neden `xargs`'a İhtiyacımız Var?**
Şöyle bir komut yazmayı deneyelim:
`ls *.log | rm`
Bu komut **çalışmaz**, çünkü `rm` komutu silinecek dosya adlarını pipeline'dan okumaz, argüman olarak bekler.

**Doğru Yöntem:**
```bash
ls *.log | xargs rm
```
**Ne Oldu?**
1.  `ls *.log` komutu, `.log` ile biten dosyaların listesini (örn: `a.log b.log c.log`) stdout'a yazdı.
2.  `xargs` bu listeyi stdin'den okudu.
3.  `xargs`, okuduğu bu listeyi `rm` komutunun sonuna argüman olarak ekleyerek şu komutu oluşturdu ve çalıştırdı: `rm a.log b.log c.log`.

## `find` ve `xargs`: Mükemmel İkili

`find` komutunun bulduğu dosyalar üzerinde toplu işlemler yapmak, bu ikilinin en yaygın kullanım alanıdır.

**Senaryo 1:** /tmp dizinindeki 7 günden eski tüm dosyaları silelim.
```bash
find /tmp -type f -mtime +7 | xargs rm
```

**Senaryo 2:** Proje dizinimizdeki tüm `.html` dosyalarının izinlerini `644` olarak ayarlayalım.
```bash
find . -name "*.html" | xargs chmod 644
```

### Daha Güvenli Bir Yöntem: `-print0` ve `-0`

Dosya adlarında boşluk veya özel karakterler (`!`, `&` vb.) olduğunda, yukarıdaki `find | xargs` yapısı sorun çıkarabilir. Çünkü `xargs` varsayılan olarak boşluklara göre ayırma yapar.

Bu sorunu çözmek için daha güvenli bir yöntem vardır:
-   `find` komutuna `-print0` parametresi eklenir. Bu, dosya yollarını boşluk yerine NULL karakteriyle ayırarak listeler.
-   `xargs` komutuna `-0` parametresi eklenir. Bu, `xargs`'ın girdiyi NULL karakterine göre ayırmasını söyler.

**Örnek:** Adında boşluk olan dosyaları bile güvenle silmek.
```bash
find /path/to/files -type f -name "*.tmp" -print0 | xargs -0 rm
```
> **🚀 En İyi Pratik:** Dosya yolları ile çalışırken `find` ve `xargs`'ı her zaman `-print0` ve `-0` parametreleriyle birlikte kullanmak, beklenmedik hataları önlemek için en güvenli yoldur.

### `find`'ın Kendi `-exec` Eylemi

`find` komutunun, bulduğu her dosya için bir komut çalıştırmasını sağlayan `-exec` adında yerleşik bir eylemi de vardır.
```bash
find . -name "*.tmp" -exec rm {} \;
```
-   `{}`: `find`'ın bulduğu mevcut dosyayı temsil eder.
-   `\;`: Komutun sonunu belirtir.

`find -exec` her bir dosya için ayrı bir komut çalıştırırken, `find | xargs` mümkün olduğunca çok dosyayı tek bir komutta birleştirir. Bu nedenle `xargs` genellikle daha hızlı ve verimlidir.

---
[➡️ Sonraki Bölüm: Bash Scripting](../03-Bash-Scripting/01-Ilk-Script-ve-Shebang.md)
