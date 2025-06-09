# 📂 FileBrowser ile Nginx Web Dizin Yönetimi

Bu rehber, Nginx üzerinden yayınlanan bir web sitesinin `/var/www/html` dizinini kolayca yönetebilmek için [FileBrowser](https://filebrowser.org/) aracını nasıl kurabileceğinizi ve sistem servisi olarak nasıl yapılandırabileceğinizi açıklar.

## 🧰 Gereksinimler

- Ubuntu 20.04/22.04
- Nginx kurulu olması
- root veya sudo yetkisi

## 🔧 Kurulum Adımları

### 1. FileBrowser’ı İndirme ve Kurma

```bash
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash
```

### 2. FileBrowser'ı Başlatma

```bash
filebrowser -r /var/www/html -p 8080 --address 0.0.0.0
```

> `-r` ile kök dizin belirlenir. `-p` port numarasıdır. `--address 0.0.0.0` tüm IP’lerden erişimi sağlar.

### 3. UFW Üzerinden Port Açma

```bash
ufw allow 8080
ufw reload
```

### 4. FileBrowser’ı Servis Olarak Tanımlama

```bash
sudo nano /etc/systemd/system/filebrowser.service
```

#### İçerik:

```ini
[Unit]
Description=FileBrowser
After=network.target

[Service]
ExecStart=/usr/local/bin/filebrowser -r /var/www/html -p 8080 --address 0.0.0.0
Restart=always
User=root

[Install]
WantedBy=multi-user.target
```

### 5. Servisi Başlatma ve Etkinleştirme

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable --now filebrowser
```

### 6. Durum Kontrolü

```bash
systemctl status filebrowser
```

## 🌐 Web Arayüzüne Erişim

Tarayıcınızdan `http://sunucu-ip-adresi:8080` adresine gidin. İlk girişte kullanıcı adı ve şifre: `admin/admin` olacaktır.

## 🛡️ Güvenlik Notu

İlk girişten sonra admin şifresini mutlaka değiştirin. Dilerseniz FileBrowser'ı bir domain altına taşıyarak Nginx üzerinden HTTPS ile erişim sağlayabilirsiniz.

Aşağıdaki sayfa üzerinden şifrenizi değiştirebilirsiniz.

![image](https://github.com/user-attachments/assets/10bd4ebe-4b73-4cc4-890c-055c0292d5f8)


## Not

FileBrowserı yayınlamış olduğunuz portu sadece kendi IP adresiniz için erişime izin vermeniz önerilir. WAF gibi koruma sistemleri kullanıyor iseniz FileBrowser için oluşturmuş olduğunuz domain adresine sadece kendi IP adresine erişimi açık tutunuz.
