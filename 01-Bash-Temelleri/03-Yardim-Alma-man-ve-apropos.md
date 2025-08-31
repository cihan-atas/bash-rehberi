# 1.3. Yardım Alma: `man`, `apropos` ve `--help`

[↩️ Geri: İçindekiler](../README.md)

Komut satırında yüzlerce komut ve her komutun onlarca parametresi vardır. Bunların hepsini ezberlemek imkansızdır. İyi bir komut satırı kullanıcısı her şeyi ezbere bilen değil, ihtiyaç duyduğu bilgiyi hızlıca bulabilen kişidir. Bu bölümde, Linux/macOS sistemlerdeki yerleşik yardım mekanizmalarını nasıl kullanacağınızı öğreneceksiniz.

## `man` - The Manual (Kılavuz)

`man` komutu, bir komutun "kullanım kılavuzu" sayfalarını (manual pages) açar. Aradığınız neredeyse her komutun bir `man` sayfası vardır ve bu sayfalar komut hakkında en detaylı ve en güvenilir bilgiyi sunar.

**Sözdizimi:**
```bash
man <komut_adı>
```

**Örnek:** `ls` komutunun kılavuz sayfasını açalım.
```bash
man ls
```
Bu komutu çalıştırdığınızda, terminal ekranı `less` adı verilen bir metin görüntüleyiciye geçer. Bu görüntüleyici içinde şu tuşları kullanarak gezinebilirsiniz:
-   **Aşağı/Yukarı Ok Tuşları:** Satır satır gezinme.
-   **Page Up / Page Down:** Sayfa sayfa gezinme.
-   **`/` (slash) tuşu:** Metin içinde arama yapmanızı sağlar. `/` yazıp aramak istediğiniz kelimeyi (örn: `/sort`) yazıp `Enter`'a basın. `n` tuşu bir sonraki eşleşmeye, `N` tuşu bir önceki eşleşmeye gider.
-   **`q` tuşu:** Görüntüleyiciden **çıkıp** terminale geri dönmenizi sağlar.

### Bir `man` Sayfasının Yapısı
Bir `man` sayfası genellikle şu bölümlerden oluşur:
-   **NAME:** Komutun adı ve tek satırlık kısa açıklaması.
-   **SYNOPSIS:** Komutun nasıl kullanılacağını gösteren sözdizimi. Köşeli parantez `[]` içindekiler isteğe bağlıdır.
-   **DESCRIPTION:** Komutun ne işe yaradığının detaylı açıklaması.
-   **OPTIONS:** Komutun alabileceği tüm parametrelerin (`-l`, `-a` gibi) ve açıklamalarının listesi.
-   **EXAMPLES:** Pratik kullanım örnekleri.
-   **SEE ALSO:** İlgili diğer komutların listesi.

## `--help` Parametresi

Çoğu komut, `man` sayfasını açmadan, temel kullanımı ve en yaygın parametreleri hakkında hızlı bir özet sunan `--help` (veya bazen `-h`) parametresini destekler.

`man` sayfasından daha az detaylıdır ama genellikle hızlı bir hatırlatma için yeterlidir.
```bash
cp --help
mkdir --help
```

## Ne Yapacağını Bilmediğinde: `apropos`

Bazen belirli bir işi yapmak istersiniz ama o işi yapan komutun adını bilmezsiniz. `apropos` komutu (veya `man -k`), `man` sayfalarının kısa açıklamaları (NAME bölümü) içinde anahtar kelime araması yapar.

**Örnek:** Dosyaları "kopyalamak" (copy) ile ilgili komutları bulmak istiyoruz.
```bash
apropos copy
```
Bu komut, açıklamasında "copy" kelimesi geçen tüm komutları ve `man` sayfalarını listeleyecektir. Çıktıda `cp`, `scp` gibi komutları görebilirsiniz.

**Örnek 2:** Kullanıcıları yönetmekle ilgili komutları bulalım.
```bash
apropos user
```
Bu arama `useradd`, `usermod`, `userdel` gibi komutları bulmanıza yardımcı olacaktır.

## Özet: Hangi Yardım Aracını Ne Zaman Kullanmalı?

| Araç | Ne Zaman Kullanılır? |
| :--- | :--- |
| **`--help`** | Bir komutun en sık kullanılan parametrelerini hızlıca hatırlamak için. |
| **`man`** | Bir komutun tüm detaylarını, tüm parametrelerini ve örneklerini öğrenmek için. **En kapsamlı kaynak.** |
| **`apropos`** | Belirli bir işi yapan komutun adını bilmediğinizde, anahtar kelime ile arama yapmak için. |

---
[➡️ Sonraki Konu: Kullanıcılar ve İzinler](./04-Kullanicilar-ve-Izinler.md)
