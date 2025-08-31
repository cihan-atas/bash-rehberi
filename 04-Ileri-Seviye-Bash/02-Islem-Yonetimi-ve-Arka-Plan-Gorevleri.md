# 4.2. İşlem Yönetimi ve Arka Plan Görevleri

[↩️ Geri: İçindekiler](../README.md)

Bir Linux sistemi, aynı anda çalışan yüzlerce işlemden (process) oluşur. İşlem yönetimi, bu çalışan programları izleme, kontrol etme ve yönetme yeteneğidir. Bu bölümde, sistemde neler olup bittiğini nasıl göreceğimizi, yanıt vermeyen programları nasıl sonlandıracağımızı ve uzun süren görevleri terminali meşgul etmeden nasıl çalıştıracağımızı öğreneceğiz.

## İşlemleri Listeleme

-   **`ps` - Process Status:** Sistemdeki çalışan işlemlerin anlık bir görüntüsünü verir. Kendi başına pek kullanışlı değildir, genellikle parametrelerle kullanılır.
    -   **`ps aux`**: En yaygın kullanımdır. Sistemdeki **tüm** kullanıcıların (`a`) çalıştırdığı, bir terminale bağlı olmayanlar dahil (`x`) tüm işlemleri kullanıcı odaklı (`u`) formatta gösterir.
    ```bash
    ps aux
    ```
    Bu komutun çıktısını genellikle `grep` ile birleştirerek belirli bir işlemi ararız:
    ```bash
    ps aux | grep nginx
    ```

-   **`top` / `htop` - Real-time Process Viewer:** `ps`'nin aksine, `top` komutu sistemdeki işlemlerin gerçek zamanlı, sürekli güncellenen bir listesini sunar. CPU ve bellek kullanımına göre sıralanmış olarak gelir, bu da en çok kaynak tüketen işlemleri bulmak için harikadır.
    -   `htop`, `top`'un daha renkli, kullanıcı dostu ve interaktif bir versiyonudur. Çoğu sistemde varsayılan olarak gelmez ama `sudo apt install htop` veya `sudo yum install htop` ile kolayca kurulabilir. **Şiddetle tavsiye edilir.**

## İşlemleri Sonlandırma: `kill`

Yanıt vermeyen veya istenmeyen bir işlemi sonlandırmak için `kill` komutu kullanılır. `kill` komutu, bir işleme belirli bir **sinyal (signal)** gönderir. Komutun çalışması için işlemin **PID (Process ID)** numarasını bilmeniz gerekir.

1.  **PID'i Bulma:**
    `ps aux | grep <islem_adi>` veya daha temiz bir sonuç için `pgrep <islem_adi>` komutunu kullanın.
    ```bash
    pgrep firefox
    ```

2.  **Sinyal Gönderme:**
    -   **TERM (15 - Terminate):** Varsayılan sinyaldir. İşleme "lütfen düzgün bir şekilde kapan" der. Bu, işlemin dosyalarını kaydedip temiz bir şekilde çıkış yapmasına olanak tanır.
        ```bash
        kill 1234  # 1234 PID'li işleme TERM sinyali gönder
        ```
    -   **KILL (9 - Kill):** En güçlü sinyaldir. "Acil durum çekici" gibidir. İşleme kendini kapatma şansı vermez, doğrudan işletim sistemi tarafından sonlandırılır. Sadece `TERM` sinyaline yanıt vermeyen işlemler için kullanılmalıdır.
        ```bash
        kill -9 1234 # veya kill -KILL 1234
        ```
-   **`pkill` ve `killall`:** PID ile uğraşmak yerine doğrudan işlem adına göre sinyal gönderirler.
    ```bash
    pkill firefox    # Adında "firefox" geçen tüm işlemlere TERM sinyali gönder
    killall firefox  # Sadece tam adı "firefox" olan işlemlere TERM sinyali gönder
    ```
    > ⚠️ **DİKKAT:** Bu komutlar, aynı adı paylaşan birden çok işlemi (örneğin, birden çok Chrome sekmesi) aynı anda sonlandırabilir.

## Arka Plan Görevleri (Background Jobs)

PowerShell'deki "Jobs" gibi, Bash de uzun süren bir komutu terminali kilitlemeden arka planda çalıştırmanıza olanak tanır.

### Bir Komutu Arka Plana Atma: `&`
Bir komutun sonuna `&` (ampersand) işareti eklemek, o komutu hemen arka planda başlatır ve terminali size geri verir.
```bash
# Büyük bir dosyayı indirme işlemini arka planda başlat
wget https://releases.ubuntu.com/22.04.3/ubuntu-22.04.3-desktop-amd64.iso &
```
Komut başladığında, size şöyle bir çıktı verilir: `[1] 5678`. Buradaki `[1]` **iş (job) numarası**, `5678` ise **PID**'dir.

### Görevleri Yönetme
-   **`jobs`**: Mevcut shell oturumunda çalışan tüm arka plan işlerini listeler.
    ```bash
    jobs
    ```
-   **`fg` - Foreground (Ön Plan):** Arka plandaki bir işi tekrar ön plana, yani terminalin kontrolüne alır.
    ```bash
    fg %1 # İş numarası 1 olan görevi ön plana al
    ```
    İş ön plana alındıktan sonra, `Ctrl + C` ile sonlandırılabilir veya `Ctrl + Z` ile duraklatılabilir.

-   **`Ctrl + Z` ve `bg` - Background (Arka Plan):**
    Ön planda çalışan bir işlemi `Ctrl + Z` ile **durdurabilirsiniz (sonlandırmaz)**. Durdurulan bu işi, `bg` komutu ile arka planda çalışmaya devam ettirebilirsiniz.
    ```bash
    ping google.com
    # ...ping çalışırken Ctrl + Z'ye basın...
    # İşlem durduruldu.
    
    bg %1 # Durdurulan işi arka planda devam ettir
    ```

### Terminali Kapattıktan Sonra Çalışmaya Devam Etme: `nohup` ve `screen`/`tmux`
Normalde, bir terminali kapattığınızda, o terminalden başlattığınız tüm arka plan işleri de sonlandırılır. Bunu önlemek için:
-   **`nohup` (No Hang Up):** Bir komutun başına `nohup` eklemek, o komutun terminal kapansa bile çalışmaya devam etmesini sağlar. Çıktıları varsayılan olarak `nohup.out` adlı bir dosyaya yazılır.
    ```bash
    nohup ./uzun_suren_script.sh &
    ```
-   **`screen` / `tmux`:** Bunlar, içinden birden çok terminal oturumu başlatabileceğiniz "terminal çoklayıcılardır". Bu programlar içinde başlattığınız bir işlem, siz ana terminalden çıksanız bile çalışmaya devam eder. Daha sonra aynı oturuma geri bağlanıp kaldığınız yerden devam edebilirsiniz. Uzun süreli ve interaktif görevler için en profesyonel yöntem budur.

---
[➡️ Sonraki Konu: Zamanlanmış Görevler: `cron`](./03-Zamanlanmis-Gorevler-Cron.md)
