# 3.1. İlk Script ve Shebang

[↩️ Geri: İçindekiler](../README.md)

Şimdiye kadar tüm komutları doğrudan terminale yazıp çalıştırdık. **Bash script'i**, bu komutları bir metin dosyasına yazarak, onları tek bir komutla toplu halde ve tekrar tekrar çalıştırılabilir hale getirmektir. Bu, otomasyonun temelidir.

## Bash Script'i Nedir?

En basit tanımıyla bir Bash script'i, normalde terminale yazacağınız komutları içeren, çalıştırılabilir bir metin dosyasıdır. Geleneksel olarak, Bash script dosyaları `.sh` uzantısıyla biter (zorunlu olmasa da iyi bir pratiktir).

## İlk Script'imizi Yazalım: `merhaba.sh`

1.  **Dosyayı Oluşturma:**
    Herhangi bir metin editörü (nano, vim, VS Code vb.) ile `merhaba.sh` adında bir dosya oluşturalım. Terminalde `nano` kullanarak yapalım:
    ```bash
    nano merhaba.sh
    ```

2.  **İçeriği Yazma:**
    Editör açıldığında, içine aşağıdaki satırları yazın:

    ```bash
    #!/bin/bash

    # Bu bir yorum satırıdır ve script'in ne yaptığını açıklar.
    # Bu script, ekrana bir selamlama mesajı ve mevcut tarihi yazdırır.

    echo "Merhaba, Dünya!"
    echo "Bugünün tarihi:"
    date
    ```

3.  **Kaydetme ve Çıkma:**
    `nano`'da dosyayı kaydetmek için `Ctrl + O` tuşlarına basın, dosya adını onaylamak için `Enter`'a basın ve editörden çıkmak için `Ctrl + X`'e basın.

### Shebang (`#!`) Nedir?

Script'in en başındaki `#!/bin/bash` satırı çok özel bir anlama sahiptir ve **Shebang** olarak adlandırılır.
-   `#!`: Bu karakterler, işletim sistemine "bu script'i çalıştırmak için bir yorumlayıcı (interpreter) kullan" der.
-   `/bin/bash`: Yorumlayıcının yolunu belirtir. Yani, "bu dosyanın içindeki komutları `/bin/bash` programını kullanarak çalıştır" anlamına gelir.

Shebang, script'inizin her zaman doğru shell ile (bu durumda Bash) çalışmasını garanti eder, kullanıcının o anki varsayılan shell'i ne olursa olsun. Bu nedenle, her Bash script'inin başına bunu eklemek **zorunlu bir en iyi pratiktir**.

## Script'i Çalıştırılabilir Hale Getirme

Bir script dosyası oluşturduğunuzda, varsayılan olarak "çalıştırma" (execute) iznine sahip değildir. Bunu `ls -l` ile kontrol edebiliriz:
```bash
ls -l merhaba.sh
# Çıktı şuna benzer olacaktır: -rw-r--r-- ...
```
Gördüğünüz gibi, `x` (execute) izni kimsede yok. Çalıştırma iznini `chmod` komutu ile eklememiz gerekir.

```bash
# Sadece mevcut kullanıcı için çalıştırma izni ekle
chmod u+x merhaba.sh
```
Şimdi izinleri tekrar kontrol edelim:
```bash
ls -l merhaba.sh
# Çıktı artık şuna benzer: -rwxr--r-- ...
```
Artık `x` iznimiz var!

## Script'i Çalıştırma

Bir script'i çalıştırmak için, terminale dosyanın yolunu yazmanız gerekir. Eğer script mevcut dizindeyse, başına `./` ekleyerek "bu dizindeki dosyayı çalıştır" demelisiniz.

```bash
./merhaba.sh
```

**Örnek Çıktı:**
```
Merhaba, Dünya!
Bugünün tarihi:
Sun Oct 27 11:45:12 EET 2024
```

> **Neden `./`?**
> Güvenlik nedeniyle, Linux sistemleri varsayılan olarak mevcut dizini çalıştırılabilir dosyaların arandığı `PATH` ortam değişkenine dahil etmez. Başına `./` koyarak, shell'e tam olarak hangi dosyayı çalıştırmak istediğimizi belirtmiş oluruz. Bu, aynı isimde ama farklı bir yerdeki (belki de zararlı) bir komutun yanlışlıkla çalıştırılmasını önler.

Tebrikler! İlk Bash script'inizi başarıyla yazdınız ve çalıştırdınız. Artık otomasyon dünyasına ilk adımınızı attınız.

---
[➡️ Sonraki Konu: Değişkenler ve Parametreler](./02-Degiskenler-ve-Parametreler.md)
