# 1.4. Kullanıcılar ve İzinler

[↩️ Geri: İçindekiler](../README.md)

Linux, en başından beri çok kullanıcılı bir işletim sistemi olarak tasarlanmıştır. Bu, birden çok kullanıcının aynı anda sistemi kullanabileceği ve her kullanıcının dosyalarının diğerlerinden güvende tutulacağı anlamına gelir. Bu güvenliğin temelini **kullanıcı hesapları**, **gruplar** ve **dosya izinleri** oluşturur.

## Kullanıcılar ve Gruplar

-   **Kullanıcı (User):** Sistemdeki her bir hesabı temsil eder. Her kullanıcının bir kullanıcı adı (username) ve bir ev dizini (`/home/kullaniciadi`) vardır.
-   **Grup (Group):** Birden çok kullanıcıyı bir araya toplayan bir yapıdır. İzinleri tek tek kullanıcılara vermek yerine bir gruba vererek yönetimi kolaylaştırır. Her kullanıcı en az bir gruba (genellikle kendi adıyla aynı olan bir birincil gruba) üyedir.

### Temel Kullanıcı Komutları

-   **`whoami`**: Mevcut kullanıcı adını gösterir.
    ```bash
    whoami
    ```
-   **`id`**: Mevcut kullanıcının kullanıcı ID'sini (UID), birincil grup ID'sini (GID) ve üye olduğu diğer tüm grupları gösterir.
    ```bash
    id
    ```
-   **`sudo` - Superuser Do**: Normal bir kullanıcının, kendi parolasıyla, geçici olarak "root" (süper kullanıcı) yetkileriyle tek bir komut çalıştırmasını sağlar. Sistemde değişiklik yapacak veya yönetici izni gerektiren komutların başına `sudo` eklenir.
    ```bash
    # Paket listesini güncellemek yönetici izni gerektirir
    sudo apt update

    # Başka bir kullanıcının sahip olduğu bir dosyayı düzenlemek
    sudo nano /etc/hosts
    ```

## Dosya İzinlerini Anlama

Linux'taki her dosya ve dizinin bir **sahibi (owner)**, bir **grubu (group)** ve bu üç kategori için ayrı ayrı tanımlanmış **izinleri (permissions)** vardır:
1.  **Sahip (Owner):** Dosyayı oluşturan kullanıcı.
2.  **Grup (Group):** Dosyanın atandığı grup. Bu grubun üyeleri, grup için tanımlanan izinlere sahip olur.
3.  **Diğerleri (Others):** Sahip veya grup üyesi olmayan diğer tüm kullanıcılar.

İzinleri görmek için `ls -l` komutunu kullanırız.
```bash
ls -l rapor.txt
```
**Örnek Çıktı:**
`-rw-r--r-- 1 zeus developers 1024 Oct 26 10:30 rapor.txt`

Bu çıktıyı parçalara ayıralım:

-   **`-rw-r--r--`**: İzin bloğu.
-   **`1`**: Hard link sayısı.
-   **`zeus`**: Dosyanın sahibi (owner).
-   **`developers`**: Dosyanın grubu (group).
-   **`1024`**: Dosyanın boyutu (byte).
-   **`Oct 26 10:30`**: Son değiştirilme tarihi.
-   **`rapor.txt`**: Dosyanın adı.

### İzin Bloğunu Okuma (`-rw-r--r--`)

Bu 10 karakterlik blok, dosyanın türünü ve izinlerini gösterir.

-   **İlk Karakter:** Dosya türü (`-`: normal dosya, `d`: dizin, `l`: sembolik link).
-   **Sonraki 3 Karakter (`rw-`):** **Sahibin** izinleri (Okuma, Yazma, Çalıştırma Yok).
-   **Ortadaki 3 Karakter (`r--`):** **Grubun** izinleri (Okuma, Yazma Yok, Çalıştırma Yok).
-   **Son 3 Karakter (`r--`):** **Diğerlerinin** izinleri (Okuma, Yazma Yok, Çalıştırma Yok).

| İzin | Harf | Anlamı |
|:---:|:---:|:---|
| **r** | Read | **Okuma:** Dosyanın içeriğini görebilir, dizinin içini listeleyebilir. |
| **w** | Write | **Yazma:** Dosyanın içeriğini değiştirebilir, dizin içinde dosya oluşturabilir/silebilir. |
| **x** | Execute| **Çalıştırma:** Dosyayı bir program gibi çalıştırabilir, dizinin içine `cd` ile girebilir. |

## İzinleri Değiştirme: `chmod`

`chmod` (change mode) komutu, bir dosyanın veya dizinin izinlerini değiştirmenizi sağlar. İki yaygın kullanım şekli vardır:

### 1. Sembolik Yöntem (Harflerle)
`u` (user/sahip), `g` (group), `o` (others), `a` (all/tümü) harflerini; `+` (izin ekle), `-` (izin çıkar), `=` (izni ayarla) operatörleriyle kullanırız.

```bash
# Bir script dosyasına sahip için çalıştırma izni ekle
chmod u+x script.sh

# Grup için yazma iznini kaldır
chmod g-w rapor.txt

# Diğerleri için tüm izinleri kaldır
chmod o=--- rapor.txt # veya chmod o-rwx rapor.txt

# Herkese okuma izni ver
chmod a+r gizli_dosya.txt
```

### 2. Sayısal (Octal) Yöntem
Her izin türüne bir sayısal değer atanır:
-   **Okuma (r): 4**
-   **Yazma (w): 2**
-   **Çalıştırma (x): 1**

Bu sayıları toplayarak bir izin seti oluştururuz. Örneğin, `rwx` (okuma + yazma + çalıştırma) izni `4 + 2 + 1 = 7` olur.

| Sayı | İzinler | Anlamı |
|:---:|:---:|:---|
| **7** | `rwx` | Tam Yetki |
| **6** | `rw-` | Okuma ve Yazma |
| **5** | `r-x` | Okuma ve Çalıştırma |
| **4** | `r--` | Sadece Okuma |
| **0** | `---` | Hiçbir İzin Yok |

`chmod` komutuna üç haneli bir sayı verilir: [Sahip][Grup][Diğerleri].
```bash
# Sahip: rwx (7), Grup: r-x (5), Diğerleri: r-x (5) -> "script.sh" için yaygın bir izin
chmod 755 script.sh

# Sahip: rw- (6), Grup: r-- (4), Diğerleri: r-- (4) -> "rapor.txt" için yaygın bir izin
chmod 644 rapor.txt
```

## Sahipliği Değiştirme: `chown`

`chown` (change owner) komutu, bir dosyanın veya dizinin sahibini ve/veya grubunu değiştirir. Bu komut genellikle `sudo` gerektirir.
```bash
# rapor.txt dosyasının sahibini 'ahmet' yap
sudo chown ahmet rapor.txt

# rapor.txt dosyasının sahibini 'ahmet' ve grubunu 'sales' yap
sudo chown ahmet:sales rapor.txt
```

---
[➡️ Sonraki Bölüm: Pipeline ve Metin İşleme](../02-Pipeline-ve-Metin-Isleme/01-Pipeline-ve-Yonlendirme.md)
