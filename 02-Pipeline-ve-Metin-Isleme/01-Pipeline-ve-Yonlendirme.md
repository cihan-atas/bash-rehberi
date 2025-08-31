# 2.1. Pipeline ve Yönlendirme

[↩️ Geri: İçindekiler](../README.md)

Bash'in gücü, tekil komutların yeteneklerinden çok, bu komutları birbirine bağlayarak ve veri akışlarını kontrol ederek karmaşık görevleri basit adımlarla çözme felsefesinden gelir. Bu felsefenin temelinde üç standart akış (standard streams), pipeline (`|`) ve yönlendirme (`>`, `<`, `>>`) operatörleri yatar.

## Üç Standart Akış (Standard Streams)

Bir terminalde bir komut çalıştırdığınızda, arka planda üç temel veri kanalı otomatik olarak açılır:

1.  **Standard Input (stdin - 0):** Komutun veri aldığı varsayılan kanaldır. Genellikle klavyenizdir.
2.  **Standard Output (stdout - 1):** Komutun başarılı çıktılarının gönderildiği varsayılan kanaldır. Genellikle terminal ekranınızdır.
3.  **Standard Error (stderr - 2):** Komutun hata mesajlarının gönderildiği varsayılan kanaldır. Bu da genellikle terminal ekranınızdır.

Bu kanalları yönetebilmek, Bash'te ustalaşmanın anahtarıdır.

## Pipeline (`|`) - Komutları Zincirleme

Pipeline (`|`) operatörü, bir komutun **stdout**'unu (başarılı çıktısını) bir sonraki komutun **stdin**'ine (girdisine) bağlar. Bu, komutları bir zincir gibi birbirine ekleyerek çıktıları adım adım işlemenizi sağlar.

**Örnek:** `/etc` dizinindeki tüm dosyaları listeleyelim ve bu listede sadece "conf" kelimesi geçen satırları gösterelim.
```bash
ls -l /etc | grep conf
```
**Ne Oldu?**
1.  `ls -l /etc` komutu çalıştı ve uzun bir dosya listesini **stdout**'una, yani normalde ekrana yazdıracaktı.
2.  Ancak `|` operatörü bu çıktıyı yakaladı.
3.  Yakalanan bu metin akışı, `grep conf` komutunun **stdin**'ine (girdisine) yönlendirildi.
4.  `grep` komutu, kendisine gelen bu metin içinde "conf" kelimesini aradı ve sadece eşleşen satırları kendi **stdout**'una, yani ekrana yazdırdı.

## Yönlendirme (Redirection) - Akışları Yönlendirme

Yönlendirme operatörleri, standart akışları terminal ekranı veya klavye yerine dosyalara yönlendirmenizi sağlar.

### Çıktı Yönlendirme: `>` ve `>>`

-   **`>` (Overwrite - Üzerine Yaz):** Bir komutun **stdout**'unu bir dosyaya yönlendirir. Eğer dosya yoksa oluşturur. Eğer dosya **varsa, içeriğini tamamen siler ve üzerine yazar.**
    > ⚠️ **DİKKAT:** Bu operatör veri kaybına neden olabilir. Kullanırken dikkatli olun.
    ```bash
    # Ev dizininin içeriğini dosya_listesi.txt dosyasına yaz
    ls -l ~ > dosya_listesi.txt
    ```

-   **`>>` (Append - Sona Ekle):** Bir komutun **stdout**'unu bir dosyanın **sonuna ekler**. Eğer dosya yoksa oluşturur. Var olan veriyi silmez.
    ```bash
    # Mevcut tarihi ve saati dosya_listesi.txt dosyasının sonuna ekle
    date >> dosya_listesi.txt
    ```

### Hata Yönlendirme: `2>`

Bir komutun sadece **stderr**'ini (hata mesajlarını) bir dosyaya yönlendirir.
```bash
# Bu komut "Permission denied" gibi hatalar üretecektir.
# Bu hataları ekranda göstermek yerine hatalar.log dosyasına yazalım.
find /etc -name "shadow" 2> hatalar.log
```
Buradaki `2`, stderr'in dosya tanıtıcı (file descriptor) numarasıdır.

**Yaygın Bir Kullanım: Hataları Görmezden Gelme**
`/dev/null`, Linux'ta "kara delik" olarak bilinen özel bir dosyadır. Oraya gönderilen her şey yok olur. Bir komutun hata mesajlarını görmek istemiyorsanız, onları `/dev/null`'a yönlendirebilirsiniz.
```bash
find /etc -name "shadow" 2> /dev/null
```

### Çıktı ve Hatayı Birleştirme: `&>` ve `2>&1`

Bazen bir komutun hem normal çıktısını hem de hata mesajlarını aynı dosyaya kaydetmek istersiniz.
-   **Modern Yöntem: `&>`**
    ```bash
    # Hem stdout hem de stderr'i ayni_dosya.log'a yaz
    find /etc &> ayni_dosya.log
    ```
-   **Klasik Yöntem: `2>&1`**
    Bu, "stderr'i (2), stdout'un (1) gittiği yere yönlendir" anlamına gelir.
    ```bash
    find /etc > ayni_dosya.log 2>&1
    ```

### Girdi Yönlendirme: `<`

Bir komutun **stdin**'ini klavye yerine bir dosyadan almasını sağlar.
```bash
# isimler.txt dosyasının içeriğini sort komutuna girdi olarak ver
# sort < isimler.txt komutu, cat isimler.txt | sort ile aynı işi yapar.
sort < isimler.txt
```

---
[➡️ Sonraki Konu: Metin Arama: `grep`](./02-Metin-Arama-grep.md)
