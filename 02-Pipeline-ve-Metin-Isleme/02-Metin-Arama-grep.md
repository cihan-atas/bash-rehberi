# 2.2. Metin Arama: `grep`

[↩️ Geri: İçindekiler](../README.md)

`grep` (Global Regular Expression Print), komut satırının en güçlü ve en temel araçlarından biridir. Temel görevi, bir metin akışı (bir dosya veya başka bir komutun çıktısı) içinde belirli bir deseni (pattern) aramak ve eşleşen satırları yazdırmaktır. Log dosyalarını analiz etmekten, konfigürasyon dosyalarında belirli bir ayarı bulmaya kadar sayısız kullanım alanı vardır.

## `grep`'in Temel Kullanımı

`grep`'i iki ana şekilde kullanabilirsiniz:

1.  **Bir Dosya İçinde Arama:**
    ```bash
    grep "aranacak_desen" dosya_adı
    ```

2.  **Pipeline ile Başka Bir Komutun Çıktısında Arama:**
    ```bash
    komut | grep "aranacak_desen"
    ```

**Örnekler:**

-   `/etc/passwd` dosyasında `zeus` kullanıcısını içeren satırı bulalım.
    ```bash
    grep "zeus" /etc/passwd
    ```

-   Çalışan işlemler listesinde "ssh" ile ilgili olanları bulalım.
    ```bash
    ps aux | grep "ssh"
    ```
    > **💡 İpucu:** `ps aux | grep "ssh"` komutunun çıktısında `grep "ssh"` satırını da görebilirsiniz. Bunu engellemek için yaygın bir hile `grep "[s]sh"` şeklinde yazmaktır. Bu, `grep` işleminin kendisinin eşleşmesini engeller.

## En Sık Kullanılan `grep` Parametreleri

`grep`'in asıl gücü, aramanızı özelleştirmenizi sağlayan parametrelerinden gelir.

| Parametre | Açıklama | Örnek |
| :--- | :--- | :--- |
| **`-i`** | **I**gnore case. Büyük/küçük harf duyarsız arama yapar. | `grep -i "error" log.txt` |
| **`-v`** | In**v**ert match. Desenle **eşleşmeyen** tüm satırları gösterir. | `ps aux \| grep -v "root"` |
| **`-r`** | **R**ecursive. Bir dizin ve tüm alt dizinleri içinde arama yapar. | `grep -r "API_KEY" /etc/` |
| **`-n`** | Line **n**umber. Eşleşen satırların dosyadaki satır numarasını gösterir. | `grep -n "root" /etc/passwd` |
| **`-c`** | **C**ount. Eşleşen desen yerine, eşleşen satırların **sayısını** gösterir. | `grep -c "Failed" auth.log` |
| **`-w`** | **W**ord. Sadece tam kelime eşleşmelerini arar. "ther" kelimesi "other" içinde bulunmaz. | `grep -w "root" /etc/passwd` |
| **`-l`** | Files with matches. Eşleşen satırları değil, sadece desenin bulunduğu **dosya adlarını** listeler. | `grep -rl "192.168.1.1" /var/log/` |
| **`-A`, `-B`, `-C`**| **A**fter, **B**efore, **C**ontext. Eşleşen satırla birlikte, o satırdan **sonraki**, **önceki** veya **hem önceki hem sonraki** belirtilen sayıda satırı gösterir. Log analizi için çok kullanışlıdır. | `grep -C 2 "ERROR" app.log` |

### Parametreleri Birleştirme Örneği

**Senaryo:** `/var/log` dizini ve alt dizinlerindeki tüm `.log` uzantılı dosyalarda, büyük/küçük harf duyarsız bir şekilde "authentication failure" ifadesini arayalım. Eşleşme bulunan dosyaların adını ve eşleşen satırın numarasını istiyoruz.

```bash
# --include ile sadece .log uzantılı dosyalarda arama yapıyoruz
grep -rin --include="*.log" "authentication failure" /var/log/
```
-   **`r`**: Alt dizinlere in.
-   **`i`**: Büyük/küçük harf duyarsız ol.
-   **`n`**: Satır numarasını göster.

## Regular Expressions (Regex) ile `grep`

`grep`'in adındaki "Regular Expression" kısmı, onun en gelişmiş yeteneğidir. Regex, metin içinde karmaşık desenleri tanımlamanızı sağlayan özel bir sözdizimidir.

-   **`^`**: Satırın başlangıcını ifade eder.
    ```bash
    # Sadece 'root' ile başlayan satırları bul
    grep "^root" /etc/passwd
    ```

-   **`$`**: Satırın sonunu ifade eder.
    ```bash
    # Sadece '/bin/bash' ile biten satırları bul (sistemde oturum açabilen kullanıcılar)
    grep "/bin/bash$" /etc/passwd
    ```

-   **`.`**: Herhangi bir tek karakterle eşleşir.

-   **`*`**: Kendinden önceki karakterin sıfır veya daha fazla tekrarı ile eşleşir.

Regex karmaşık bir konudur ancak temellerini bilmek bile `grep`'in gücünü katlar. Daha karmaşık regex desenleri için `-E` (Extended Regular Expression) parametresi kullanılır.

**Örnek:** Bir log dosyasında hem "error" hem de "warning" kelimelerini aynı anda arayalım.
```bash
grep -E "error|warning" application.log
```
Buradaki `|` "veya" anlamına gelir.

---
[➡️ Sonraki Konu: Metin Manipülasyonu: `sed`, `awk`, `cut`](./03-Metin-Manipulasyonu-sed-awk-cut.md)
