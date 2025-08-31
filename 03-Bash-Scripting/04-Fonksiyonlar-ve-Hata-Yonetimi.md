# 3.4. Fonksiyonlar ve Hata Yönetimi

[↩️ Geri: İçindekiler](../README.md)

Script'lerimiz karmaşıklaştıkça, kod tekrarı yapmaya ve hataların nerede oluştuğunu takip etmekte zorlanmaya başlarız. **Fonksiyonlar**, kodumuzu yeniden kullanılabilir bloklara ayırarak bu sorunu çözer. **Hata yönetimi** ise, script'imizin beklenmedik bir sorunla karşılaştığında kontrolsüz bir şekilde çökmesini engeller.

## Fonksiyonlar

Fonksiyonlar, belirli bir görevi yerine getiren, isimlendirilmiş kod bloklarıdır. Bir kez yazılır ve ihtiyaç duyulduğunda adı çağrılarak tekrar tekrar kullanılırlar. Bu, script'i daha modüler, okunabilir ve yönetilebilir hale getirir.

**Sözdizimi:**
```bash
fonksiyon_adi() {
    # Çalıştırılacak kodlar
    # Parametreler $1, $2, ... şeklinde alınır
}
```
veya
```bash
function fonksiyon_adi {
    # Çalıştırılacak kodlar
}
```
Her iki sözdizimi de geçerlidir. İlk yöntem daha yaygındır.

**Örnek:** Tarih ve saati belirli bir formatta yazdıran bir fonksiyon oluşturalım.
```bash
#!/bin/bash

# Fonksiyon tanımı
log_mesaji_yazdir() {
    # Fonksiyona verilen ilk parametre ($1)
    local mesaj="$1"
    local tarih=$(date "+%Y-%m-%d %H:%M:%S")
    echo "[$tarih] - $mesaj"
}

# Fonksiyonu çağırma
log_mesaji_yazdir "Script başlatıldı."
# ... diğer işlemler ...
sleep 2
log_mesaji_yazdir "Yedekleme tamamlandı."
```
> **💡 `local` Değişkenler:** Bir fonksiyon içinde `local` anahtar kelimesi ile tanımlanan bir değişken, sadece o fonksiyonun içinde geçerlidir. Script'in geri kalanındaki aynı isimli değişkenlerle çakışmasını önler. Bu, iyi bir programlama pratiğidir.

### Fonksiyonlardan Değer Döndürme

Diğer dillerin aksine, Bash fonksiyonları doğrudan bir değer (örn: bir metin) "return" etmez. `return` komutu, sadece 0 ile 255 arasında bir **çıkış kodu (exit code)** döndürmek için kullanılır (`0` başarı, diğerleri hata).

Bir fonksiyondan bir değer almak için iki yaygın yöntem vardır:
1.  **Komut Çıktısı (Command Substitution):** Fonksiyonun içine `echo` ile istediğiniz değeri yazdırır ve fonksiyonu `$(...)` içinde çağırırsınız.
    ```bash
    get_kullanici_sayisi() {
        who | wc -l
    }

    aktif_kullanicilar=$(get_kullanici_sayisi)
    echo "Aktif kullanıcı sayısı: $aktif_kullanicilar"
    ```
2.  **Global Değişken Kullanma:** Fonksiyon, script'in genelinde tanımlanmış bir değişkenin değerini değiştirir. (Daha az tercih edilen bir yöntemdir).

## Hata Yönetimi

Varsayılan olarak, bir script içindeki bir komut hata verdiğinde, script çalışmaya devam eder. Bu, beklenmedik sonuçlara yol açabilir. Örneğin, `cd` komutu başarısız olursa, bir sonraki `rm -rf *` komutu yanlışlıkla tüm ev dizininizi silebilir!

### `set` Komutu ile Script Davranışını Değiştirme

Script'lerimizi daha güvenli hale getirmek için, dosyanın başına (shebang'den sonra) bazı `set` seçenekleri eklemek en iyi pratiktir.

```bash
#!/bin/bash
set -euo pipefail
```
Bu satır ne anlama geliyor?
-   **`set -e` (veya `set -o errexit`):** Herhangi bir komut sıfırdan farklı bir çıkış koduyla (yani bir hatayla) sonlanırsa, script'i **hemen sonlandırır**. Bu, en önemli güvenlik önlemidir.
-   **`set -u` (veya `set -o nounset`):** Tanımlanmamış bir değişken kullanılmaya çalışıldığında script'i **hemen sonlandırır**. Yazım hatalarından kaynaklanan sorunları önler.
-   **`set -o pipefail`:** Bir pipeline'daki (`|`) herhangi bir komut hata verirse, tüm pipeline'ın çıkış kodunu o komutun hata kodu olarak ayarlar. Normalde sadece en son komutun çıkış kodu dikkate alınır.

### Çıkış Kodlarını (`$?`) Kontrol Etme

`set -e` kullanmak istemediğiniz durumlarda, bir komutun başarılı olup olmadığını manuel olarak kontrol edebilirsiniz. `$?` özel değişkeni, en son çalışan komutun çıkış kodunu tutar.

```bash
#!/bin/bash

# set -e kullanmadığımızı varsayalım
tar -czf yedek.tar.gz /var/www/

if [[ $? -eq 0 ]]; then
    log_mesaji_yazdir "Yedekleme başarılı."
else
    log_mesaji_yazdir "YEDEKLEME BAŞARISIZ OLDU!"
    # Burada bir e-posta gönderme gibi bir eylem de olabilir
    exit 1 # Script'i hata koduyla sonlandır
fi
```

Bu iki konsepti birleştirmek—kodu fonksiyonlara bölmek ve `set -euo pipefail` ile sağlamlaştırmak—size profesyonel, güvenilir ve bakımı kolay Bash script'leri yazma gücü verir.

---
[➡️ Sonraki Bölüm: İleri Seviye Bash](../04-Ileri-Seviye-Bash/01-Uzak-Yonetim-SSH-ve-SCP.md)
