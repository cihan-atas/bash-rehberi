# 4.4. Ağ Araçları

[↩️ Geri: İçindekiler](../README.md)

Linux, doğası gereği bir ağ işletim sistemidir ve ağ sorunlarını teşhis etmek, bağlantıları analiz etmek ve ağ üzerinden veri alışverişi yapmak için yerleşik olarak gelen çok zengin bir araç setine sahiptir. Bu bölümde, her Bash kullanıcısının bilmesi gereken en temel ve güçlü ağ araçlarından bazılarını ele alacağız.

## Bağlantı Testi ve Teşhis

-   **`ping`**: Bir ana makinenin (host) ağ üzerinde erişilebilir olup olmadığını ve ağ gecikmesini (latency) test etmek için en temel araçtır. ICMP "echo request" paketleri gönderir ve "echo reply" bekler.
    ```bash
    # google.com'a 4 paket gönder
    ping -c 4 google.com
    ```

-   **`traceroute`** (veya `mtr`): Bir paketin, hedef ana makineye ulaşana kadar geçtiği tüm yönlendiricileri (router) adım adım gösterir. Bir ağ bağlantısı sorununda, sorunun nerede (kendi ağınızda mı, servis sağlayıcıda mı?) olduğunu bulmak için çok kullanışlıdır.
    ```bash
    traceroute google.com
    ```

-   **`dig` / `nslookup`**: DNS (Domain Name System) sorguları yapmak için kullanılır. Bir alan adının IP adresini (veya tam tersini), mail sunucusu (MX) kayıtlarını ve diğer DNS bilgilerini öğrenmenizi sağlar. `dig` genellikle daha detaylı ve tercih edilen araçtır.
    ```bash
    # Bir alan adının A (adres) kaydını sorgula
    dig github.com

    # Google'ın DNS sunucusunu kullanarak sorgu yap
    dig @8.8.8.8 github.com
    ```

## Web ile Etkileşim

-   **`curl` (Client URL):** Komut satırının İsviçre çakısıdır. HTTP, HTTPS, FTP ve daha birçok protokol üzerinden veri transferi yapmak için kullanılır. Web sitelerinin içeriğini indirmek, API'larla etkileşime geçmek ve dosya indirmek/yüklemek için vazgeçilmezdir.
    ```bash
    # Bir web sayfasının HTML içeriğini terminale yazdır
    curl https://example.com

    # Bir API'ye JSON verisi ile POST isteği gönderme
    curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' https://api.example.com/data

    # Bir dosyanın sadece başlık (header) bilgilerini al (-I)
    curl -I https://google.com
    ```

-   **`wget`**: `curl`'e benzer, ancak temel amacı web'den dosyaları **indirmektir**. Özellikle büyük dosyaları indirmek veya bir web sitesini tamamen (recursive) indirmek için daha kullanışlıdır. İndirme işlemini yarıda kesip daha sonra devam ettirme (`-c` parametresi) gibi güzel özellikleri vardır.
    ```bash

    # Bir dosyayı indir
    wget https://releases.ubuntu.com/22.04/ubuntu-22.04.3-desktop-amd64.iso
    ```

## Soket ve Bağlantı Analizi

-   **`netstat` - Network Statistics (Eski):** Ağ bağlantılarını, yönlendirme tablolarını, arayüz istatistiklerini vb. gösteren klasik bir araçtır. Hala birçok sistemde bulunsa da, yerini `ss` almıştır.

-   **`ss` - Socket Statistics (Modern):** `netstat`'in modern, daha hızlı ve daha detaylı alternatifidir. `netstat`'in yaptığı her şeyi ve daha fazlasını yapar.
    ```bash
    # Dinlemede olan (listening) tüm TCP ve UDP portlarını göster
    ss -tuln
    ```
    -   **`t`**: TCP soketleri
    -   **`u`**: UDP soketleri
    -   **`l`**: Sadece dinlemede olan (listening) soketler
    -   **`n`**: Servis isimleri yerine port numaralarını göster (daha hızlı)

-   **`nmap` - Network Mapper:** Bir ağ keşif ve güvenlik tarama aracıdır. Sistemlerde varsayılan olarak gelmez ama her güvenlik uzmanının ve sistem yöneticisinin kurması gereken bir araçtır.
    -   Bir ana makinedeki açık portları tarar.
    -   Ağdaki canlı ana makineleri keşfeder.
    -   Hedef sistemlerde çalışan servisleri ve işletim sistemi sürümlerini tespit etmeye çalışır.
    ```bash
    # Bir sunucudaki en yaygın 1000 portu tara
    nmap webserver.example.com

    # Belirli bir portun açık olup olmadığını hızlıca kontrol et
    nmap -p 80,443 webserver.example.com

    # Agresif tarama (versiyon tespiti, script taraması, traceroute)
    nmap -A 192.168.1.1
    ```

---
[➡️ Sonraki Bölüm: Mavi Takım Uygulamaları](../05-Mavi-Takim-Uygulamalari/01-Log-Analizi.md)
