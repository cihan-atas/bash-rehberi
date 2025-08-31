# Kapsamlı Bash Rehberi

**Linux/macOS Sistem Yönetiminden Siber Güvenliğe**

---

Bu rehber, Bash kabuğunu ve komut satırı kullanımını temel seviyeden başlayarak, siber güvenlik (Mavi ve Kırmızı Takım) uygulamalarına kadar uzanan geniş bir yelpazede öğretmek amacıyla hazırlanmıştır.

## 🚀 Başlarken

Bu rehber, konuların birbirini takip ettiği modüler bir yapıda tasarlanmıştır. Eğer Bash'e yeni başlıyorsanız, konuları sırayla takip etmenizi öneririz. Belirli bir konuda bilgi arıyorsanız, aşağıdaki içindekiler tablosundan doğrudan ilgili sayfaya atlayabilirsiniz.

## 📚 İçindekiler

### [Bölüm 1: Bash Temelleri](./01-Bash-Temelleri/)
*   [1.1. Shell Nedir ve Terminal Kullanımı](./01-Bash-Temelleri/01-Shell-Nedir-ve-Terminal-Kullanimi.md)
*   [1.2. Temel Komutlar ve Dosya Sistemi](./01-Bash-Temelleri/02-Temel-Komutlar-ve-Dosya-Sistemi.md)
*   [1.3. Yardım Alma: `man` ve `apropos`](./01-Bash-Temelleri/03-Yardim-Alma-man-ve-apropos.md)
*   [1.4. Kullanıcılar ve İzinler](./01-Bash-Temelleri/04-Kullanicilar-ve-Izinler.md)

### [Bölüm 2: Pipeline ve Metin İşleme](./02-Pipeline-ve-Metin-Isleme/)
*   [2.1. Pipeline ve Yönlendirme](./02-Pipeline-ve-Metin-Isleme/01-Pipeline-ve-Yonlendirme.md)
*   [2.2. Metin Arama: `grep`](./02-Pipeline-ve-Metin-Isleme/02-Metin-Arama-grep.md)
*   [2.3. Metin Manipülasyonu: `sed`, `awk`, `cut`](./02-Pipeline-ve-Metin-Isleme/03-Metin-Manipulasyonu-sed-awk-cut.md)
*   [2.4. Komutları Birleştirme: `xargs` ve `find`](./02-Pipeline-ve-Metin-Isleme/04-Komutlari-Birlestirme-xargs-ve-find.md)

### [Bölüm 3: Bash Scripting](./03-Bash-Scripting/)
*   [3.1. İlk Script ve Shebang](./03-Bash-Scripting/01-Ilk-Script-ve-Shebang.md)
*   [3.2. Değişkenler ve Parametreler](./03-Bash-Scripting/02-Degiskenler-ve-Parametreler.md)
*   [3.3. Akış Kontrolü: `if`, `for`, `while`](./03-Bash-Scripting/03-Akis-Kontrolu-If-For-While.md)
*   [3.4. Fonksiyonlar ve Hata Yönetimi](./03-Bash-Scripting/04-Fonksiyonlar-ve-Hata-Yonetimi.md)

### [Bölüm 4: İleri Seviye Bash](./04-Ileri-Seviye-Bash/)
*   [4.1. Uzak Yönetim: `ssh` ve `scp`](./04-Ileri-Seviye-Bash/01-Uzak-Yonetim-SSH-ve-SCP.md)
*   [4.2. İşlem Yönetimi ve Arka Plan Görevleri](./04-Ileri-Seviye-Bash/02-Islem-Yonetimi-ve-Arka-Plan-Gorevleri.md)
*   [4.3. Zamanlanmış Görevler: `cron`](./04-Ileri-Seviye-Bash/03-Zamanlanmis-Gorevler-Cron.md)
*   [4.4. Ağ Araçları](./04-Ileri-Seviye-Bash/04-Ag-Araclari.md)

### [Bölüm 5: Mavi Takım Uygulamaları](./05-Mavi-Takim-Uygulamalari/)
*   [5.1. Log Analizi (`/var/log`, `journalctl`)](./05-Mavi-Takim-Uygulamalari/01-Log-Analizi.md)
*   [5.2. Sistem İzleme ve Auditing](./05-Mavi-Takim-Uygulamalari/02-Sistem-Izleme-ve-Auditing.md)
*   [5.3. Güvenlik Script'leri ve Otomasyon](./05-Mavi-Takim-Uygulamalari/03-Guvenlik-Scriptleri-ve-Otomasyon.md)

### [Bölüm 6: Kırmızı Takım Uygulamaları](./06-Kirmizi-Takim-Uygulamalari/)
*   [6.1. Keşif ve Bilgi Toplama](./06-Kirmizi-Takim-Uygulamalari/01-Kesif-ve-Bilgi-Toplama.md)
*   [6.2. Reverse Shell ve Erişim](./06-Kirmizi-Takim-Uygulamalari/02-Reverse-Shell-ve-Erisim.md)
*   [6.3. Kalıcılık Mekanizmaları](./06-Kirmizi-Takim-Uygulamalari/03-Kalicilik-Mekanizmalari.md)
*   [6.4. İz Temizleme](./06-Kirmizi-Takim-Uygulamalari/04-Iz-Temizleme.md)

### [Bölüm 7: Ek Kaynaklar](./07-Ek-Kaynaklar/)
*   [7.1. Kaynaklar ve Topluluk](./07-Ek-Kaynaklar/01-Kaynaklar-ve-Topluluk.md)
*   [7.2. Faydalı Tek Satırlık Komutlar (Cheatsheet)](./07-Ek-Kaynaklar/02-Faydali-Tek-Satirlik-Komutlar.md)
