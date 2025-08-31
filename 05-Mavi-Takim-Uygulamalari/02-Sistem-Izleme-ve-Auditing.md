# 5.2. Sistem İzleme ve Auditing

[↩️ Geri: İçindekiler](../README.md)

Log analizi bize geçmişte ne olduğunu söylerken, **sistem izleme (monitoring)** bize *şu anda* ne olduğunu gösterir. **Auditing (denetim)** ise, normalde loglanmayan çok daha detaylı olayları (örneğin, bir dosyayı kimin okuduğu, bir sistem çağrısını kimin yaptığı) kaydetmemizi sağlar. Bu ikisi, Mavi Takım'ın proaktif olarak anormallikleri tespit etmesi ve olaylara müdahale etmesi için kritik öneme sahiptir.

## Canlı Sistem İzleme Araçları

-   **`top` / `htop`**: [İşlem Yönetimi](./../04-Ileri-Seviye-Bash/02-Islem-Yonetimi-ve-Arka-Plan-Gorevleri.md) bölümünde gördüğümüz bu araçlar, canlı izlemenin temelidir. Beklenmedik şekilde yüksek CPU veya bellek tüketen bir işlem, bir sorunun veya zararlı bir aktivitenin ilk işareti olabilir.

-   **`lsof` - List Open Files:** Linux'ta "her şey bir dosyadır" felsefesi vardır. Bu komut, sistemdeki işlemler tarafından o anda açık olan **tüm** dosyaları (normal dosyalar, ağ bağlantıları, kütüphaneler vb.) listeler.
    -   **Neden Önemli?** Bir işlemin hangi dosyalara eriştiğini, hangi ağ bağlantılarını kurduğunu veya hangi portları dinlediğini görmek için paha biçilmezdir.
    ```bash
    # Apache işlemi tarafından açılmış tüm dosyaları ve ağ bağlantılarını listele
    sudo lsof -p $(pgrep apache2)

    # 80 numaralı portu dinleyen işlemi bul
    sudo lsof -i :80
    ```

-   **`ss` - Socket Statistics:** [Ağ Araçları](./../04-Ileri-Seviye-Bash/04-Ag-Araclari.md) bölümünde gördüğümüz bu komut, canlı ağ bağlantılarını izlemek için en iyi araçtır. `-t` (TCP), `-p` (process) parametreleri ile kullanıldığında, hangi işlemin hangi bağlantıyı kurduğunu gösterir.
    ```bash
    # Kurulmuş olan tüm TCP bağlantılarını ve bu bağlantıları kuran işlemleri göster
    sudo ss -tp
    ```
    Bu komutun çıktısında, sisteminizden dışarıya, bilinmeyen bir IP adresine bağlantı kurmuş şüpheli bir işlem (`bash`, `nc`, `python` gibi) görmek, acil müdahale gerektiren bir durum olabilir.

## Derinlemesine Denetim: `auditd`

`auditd`, Linux çekirdeğindeki denetim (auditing) sistemini yöneten servistir. `auditd` ile, normalde loglanmayan çok hassas olayları izlemek için kurallar tanımlayabilirsiniz.

-   **Neleri İzleyebilir?**
    -   Belirli bir dosyaya yapılan tüm okuma/yazma/çalıştırma erişimleri (örn: `/etc/shadow`).
    -   Belirli sistem çağrılarının (syscalls) kullanımı (örn: `chmod`, `chown`).
    -   Bir kullanıcının çalıştırdığı tüm komutlar.
    -   Başarısız dosya erişim denemeleri.

`auditd`'nin logları genellikle `/var/log/audit/audit.log` dosyasında tutulur ve bu logları okumak ve aramak için `ausearch` ve `aureport` komutları kullanılır.

### `auditd` Kurallarını Ayarlama

Kurallar genellikle `/etc/audit/rules.d/` altındaki `.rules` dosyalarına yazılır.

**Örnek Kurallar:**

1.  **/etc/shadow dosyasındaki her değişikliği izle:** Bu dosya, kullanıcı parolalarının hash'lerini tuttuğu için çok kritiktir.
    ```
    # /etc/audit/rules.d/shadow.rules
    -w /etc/shadow -p wa -k shadow_access
    ```
    -   **`-w`**: İzlenecek dosya (watch).
    -   **`-p wa`**: İzinler (permissions) - **w**rite (yazma), **a**ttribute change (nitelik değişikliği).
    -   **`-k`**: Kurala bir anahtar kelime (key) ata, böylece loglarda araması kolay olur.

2.  **`chmod` ve `chown` sistem çağrılarının kullanımını izle:** Bir saldırganın dosya izinlerini değiştirerek yetki yükseltmeye çalışmasını tespit edebilir.
    ```
    # /etc/audit/rules.d/permissions.rules
    -a always,exit -F arch=b64 -S chmod -S fchmod -S fchmodat -k perm_mod
    -a always,exit -F arch=b64 -S chown -S fchown -S fchownat -k perm_mod
    ```

Kuralları ekledikten sonra, `auditd` servisini yeniden başlatmak veya `augenrules --load` komutunu çalıştırmak gerekir.

### Denetim Loglarını Sorgulama: `ausearch`

`ausearch`, karmaşık `audit.log` dosyasını sorgulamak için kullanılır.
```bash
# 'shadow_access' anahtar kelimesiyle etiketlenmiş tüm olayları ara
ausearch -k shadow_access

# Belirli bir kullanıcı tarafından oluşturulan tüm denetim olaylarını bul
ausearch -ua zeus
```
`auditd` yapılandırmak ve loglarını analiz etmek başlı başına bir uzmanlık alanı olsa da, temellerini bilmek bir Mavi Takım analisti için sistemde olan bitenler hakkında eşsiz bir görünürlük sağlar.

---
[➡️ Sonraki Konu: Güvenlik Script'leri ve Otomasyon](./03-Guvenlik-Scriptleri-ve-Otomasyon.md)
