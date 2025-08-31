# 1.1. Shell Nedir ve Terminal Kullanımı

[↩️ Geri: İçindekiler](../README.md)

Linux ve macOS dünyasına hoş geldiniz! Bu bölümde, komut satırının kalbi olan "Shell" ve "Terminal" kavramlarını, ekranda gördüğünüz komut isteminin ne anlama geldiğini ve ilk komutlarınızı nasıl çalıştıracağınızı öğreneceksiniz.

## Shell, Terminal ve Bash Nedir?

Bu üç terim sık sık birbirinin yerine kullanılsa da, aslında farklı şeyleri ifade ederler:

-   **Shell (Kabuk):** İşletim sisteminin çekirdeği (kernel) ile konuşmanızı sağlayan bir **programdır**. Siz komutları yazarsınız, shell bu komutları yorumlar ve işletim sisteminin anlaması için çekirdeğe iletir. Bir nevi tercümandır.

-   **Bash (Bourne Again Shell):** En yaygın kullanılan **shell türüdür**. Linux dağıtımlarının ve macOS'un varsayılan shell'idir. Bu rehberde odaklanacağımız shell Bash'tir. (`zsh`, `fish` gibi başka shell'ler de mevcuttur).

-   **Terminal (veya Terminal Emulator):** Shell'i çalıştırmak ve onunla etkileşim kurmak için kullandığınız **pencere veya uygulamadır**. Terminal, klavyeden girdilerinizi alır, shell'e gönderir ve shell'den gelen çıktıları ekranda gösterir.

> **Analoji:** Eğer Shell bir web sitesi ise (örneğin, google.com), Terminal o web sitesine girmek için kullandığınız tarayıcıdır (Chrome, Firefox).

## Komut İstemi (Command Prompt)

Terminali açtığınızda, komut yazmanızı bekleyen bir satır görürsünüz. Buna komut istemi denir ve size önemli bilgiler verir.

Senin örneğindeki gibi bir istemi inceleyelim:
`┌──(zeus㉿whoami)-[~/Desktop/powershell-rehberi]`
`└─$`

Bu, genellikle şu formatı takip eder: `kullanici@makineadi:mevcut_dizin$`

-   **`zeus`**: Mevcut kullanıcı adıdır.
-   **`whoami`**: Makinenin adıdır (hostname).
-   **`~/Desktop/powershell-rehberi`**: Şu anda içinde bulunduğunuz dizini (klasörü) gösterir. `~` (tilde) karakteri, her zaman sizin "ev" (home) dizininizi temsil eder (örn: `/home/zeus`).
-   **`$`**: Komutları normal bir kullanıcı olarak çalıştırdığınızı gösterir. Eğer bu işaret `#` olsaydı, bu "root" (süper kullanıcı/admin) olarak çalıştığınız anlamına gelirdi ve bu modda yapılan her işlem sisteme kalıcı zarar verebilir.

| İşaret | Anlamı | Yetki Seviyesi |
|:---:|:---|:---|
| **`$`** | Normal Kullanıcı | Kısıtlı |
| **`#`** | Root (Süper Kullanıcı) | Tam Yetki |

## İlk Komutlarınız

Hadi terminali kullanarak sistemle konuşmaya başlayalım. Aşağıdaki komutları yazıp `Enter`'a basın.

-   **`whoami`**: "Ben kimim?" diye sorar ve mevcut kullanıcı adınızı gösterir.
    ```bash
    whoami
    ```

-   **`hostname`**: "Bu makinenin adı ne?" diye sorar.
    ```bash
    hostname
    ```

-   **`date`**: Mevcut tarih ve saati gösterir.
    ```bash
    date
    ```

-   **`cal`**: İçinde bulunduğunuz ayın takvimini gösterir.
    ```bash
    cal
    ```

-   **`clear`**: Terminal ekranını temizler.
    ```bash
    clear
    ```
Bu komutlar sisteme zarar vermez, sadece bilgi almanızı sağlar. Onları denemekten çekinmeyin!

---
[➡️ Sonraki Konu: Temel Komutlar ve Dosya Sistemi](./02-Temel-Komutlar-ve-Dosya-Sistemi.md)
