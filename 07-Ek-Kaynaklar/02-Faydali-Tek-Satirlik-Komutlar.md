# 7.2. Faydalı Tek Satırlık Komutlar (Cheatsheet)

[↩️ Geri: İçindekiler](../README.md)

Bu bölüm, günlük görevlerde hayat kurtaran, Bash'in gücünü ve esnekliğini gösteren pratik, tek satırlık komutların bir koleksiyonudur.

### Dosya ve Dizin Yönetimi

-   **Belirli bir türdeki tüm dosyaları bulup silme:**
    ```bash
    find . -type f -name "*.tmp" -print0 | xargs -0 rm
    ```

-   **Bir dizindeki en büyük 10 dosyayı bulma:**
    ```bash
    find . -type f -exec ls -lh {} + | sort -rh -k 5 | head -n 10
    ```

-   **Mevcut dizindeki tüm alt dizinlerin boyutunu gösterme:**
    ```bash
    du -h --max-depth=1 . | sort -rh
    ```

-   **Bir komutun çıktısını hem ekrana yazdırma hem de dosyaya kaydetme (`tee`):**
    ```bash
    ls -l | tee dosya_listesi.txt
    ```

-   **En son değiştirilen dosyayı bulma:**
    ```bash
    ls -lt | head -n 2
    ```

### Ağ İşlemleri

-   **Kendi genel IP adresinizi öğrenme:**
    ```bash
    curl ifconfig.me
    ```

-   **Belirli bir portu hangi işlemin kullandığını bulma:**
    ```bash
    sudo lsof -i :80
    ```

-   **Basit bir web sunucusu başlatma (mevcut dizindeki dosyaları paylaşmak için):**
    ```bash
    # Python 3
    python3 -m http.server 8000
    # Python 2
    # python -m SimpleHTTPServer 8000
    ```

-   **Bir portun açık olup olmadığını hızlıca test etme:**
    ```bash
    nc -zv google.com 443
    ```

### Sistem ve Süreç Yönetimi

-   **En çok CPU tüketen ilk 5 işlemi listeleme:**
    ```bash
    ps aux --sort=-%cpu | head -n 6
    ```

-   **En çok bellek tüketen ilk 5 işlemi listeleme:**
    ```bash
    ps aux --sort=-%mem | head -n 6
    ```

-   **Bir kullanıcının çalıştırdığı tüm işlemleri sonlandırma:**
    ```bash
    pkill -u <kullanici_adi>
    ```

-   **Bir komutun çalışma süresini ölçme (`time`):**
    ```bash
    time find / -name "myfile.txt"
    ```

### Metin İşleme ve Veri Çıkarma

-   **Bir log dosyasındaki en çok tekrar eden IP adreslerini sayma:**
    ```bash
    cat access.log | awk '{print $1}' | sort | uniq -c | sort -nr | head
    ```

-   **Bir dosyadan tüm boş satırları silme:**
    ```bash
    grep -v '^$' dosya.txt
    ```

-   **Bir dosyanın sondan 100. satırından sonuna kadar olan kısmı gösterme:**
    ```bash
    tail -n +100 dosya.txt
    ```

-   **Komut geçmişinizde en çok kullandığınız 10 komutu listeleme:**
    ```bash
    history | awk '{print $2}' | sort | uniq -c | sort -nr | head -n 10
    ```

-   **Bir dosyadan yinelenen satırları kaldırıp sadece benzersiz olanları bırakma:**
    ```bash
    sort dosya.txt | uniq > benzersiz_dosya.txt
    ```

-   **Tüm komut geçmişinizi temizleme:**
    ```bash
    cat /dev/null > ~/.bash_history && history -c
    ```

Bu tek satırlık komutlar, Bash'in "küçük araçları birleştirerek büyük işler yapma" felsefesinin en güzel örnekleridir.

---
[↩️ Geri: İçindekiler](../README.md)
