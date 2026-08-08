# VS Code Tunnel trên Điện Thoại với Termux

Hướng dẫn cài đặt và sử dụng Visual Studio Code Tunnel trên điện thoại Android/iPhone thông qua Termux để truy cập môi trường lập trình từ trình duyệt web.

## Yêu cầu

### Android

* Cài đặt ứng dụng **Termux** từ Google Play Store:

  * https://play.google.com/store/apps/details?id=com.termux

### iPhone / iPad (iOS)

* Cài đặt ứng dụng **Termius** hoặc ứng dụng Terminal tương tự từ App Store:

  * https://apps.apple.com/us/app/termius-terminal-ssh-client/id549039908

> **Lưu ý:** Termux hiện chỉ hỗ trợ Android. Trên iOS bạn cần sử dụng ứng dụng SSH/Terminal khác hoặc kết nối tới một máy Linux từ xa.

---

## Bước 1: Cài đặt Ubuntu trong Termux

Cập nhật Termux và cài đặt `proot-distro`:

```bash
pkg update && pkg upgrade -y && pkg install proot-distro -y
```

Cài đặt Ubuntu:

```bash
proot-distro install ubuntu
```

Đăng nhập vào Ubuntu:

```bash
proot-distro login ubuntu
```

---

## Bước 2: Cài đặt các gói cần thiết

Trong môi trường Ubuntu, chạy:

```bash
apt update && apt install wget tar xz-utils git -y
```

---

## Bước 3: Tải VS Code CLI

Xóa các tệp cũ (nếu có):

```bash
rm -f code vscode_cli.tar.gz
```

Tải VS Code CLI:

```bash
wget -O vscode_cli.tar.gz "https://code.visualstudio.com/sha/download?build=stable&os=cli-alpine-arm64"
```

Giải nén:

```bash
tar -xf vscode_cli.tar.gz
```

---

## Bước 4: Khởi động VS Code Tunnel

Chạy lệnh:

```bash
./code tunnel
```

Lần đầu chạy, chương trình sẽ yêu cầu đăng nhập tài khoản Microsoft hoặc GitHub.

Sau khi đăng nhập thành công, VS Code sẽ tạo một Tunnel và cung cấp liên kết truy cập từ trình duyệt.

---

## Bước 5: Truy cập VS Code từ trình duyệt

Mở trình duyệt trên bất kỳ thiết bị nào và truy cập:

```text
https://vscode.dev/tunnel
```

Đăng nhập cùng tài khoản đã sử dụng ở bước trước để kết nối tới thiết bị.

---

## Khởi động lại sau này

Mỗi khi cần sử dụng lại VS Code Tunnel:

```bash
proot-distro login ubuntu
cd ~
./code tunnel
```
## Termux tránh shutdown

- Bật Acquire wakelock trên thanh thông báo của máy

- Bật chạy nền ứng dụng Termux

- Để tránh việc Termux shutdown trong lúc làm việc, tải file platform-tools.zip về máy rồi giải nén. Sau đó truy cập vào cmd của thư mục platform-tools rồi chạy các lệnh sau:

```bash
adb shell "/system/bin/device_config put activity_manager max_phantom_processes 2147483647"
adb shell "/system/bin/device_config set_sync_disabled_for_tests persistent; /system/bin/device_config put activity_manager max_phantom_processes 2147483647"
```
---

## Cài đặt NodeJS, Npm trên Termux

```bash
wget https://nodejs.org/dist/v24.19.0/node-v24.19.0-linux-arm64.tar.xz
tar -xf node-v24.19.0-linux-arm64.tar.xz
cp -r node-v24.19.0-linux-arm64/* /usr/local/
rm -rf node-v24.19.0-linux-arm64*
ln -sf /usr/local/bin/node /usr/bin/node
ln -sf /usr/local/bin/npm /usr/bin/npm
ln -sf /usr/local/bin/npx /usr/bin/npx
hash -r
```

## Cài đặt MySQL trên Termux

```bash
pkg update && pkg install mariadb -y

mariadb-install-db

mariadbd-safe &

#Kết nối MySQL với Code Tunnel
mariadb -u root -h 127.0.0.1

#Thay đổi passowrd Database
ALTER USER 'root'@'localhost' IDENTIFIED BY 'matkhaucuaban';
FLUSH PRIVILEGES;
EXIT;
```
## Cài đặt Redis trên Termux

```bash
pkg update && pkg install redis -y
#Thay đổi passowrd Redis
nano $PREFIX/etc/redis.conf
#Tìm dòng "# requirepass foobared"
#Bỏ dấu "# ở đầu dòng và sửa lại thành mật khẩu của bạn:"
requirepass matkhaucuaban
```
## Di chuyển code vào hệ thống và tạo file start-all.sh

```bash
proot-distro login ubuntu -- bash -c "mv ~/code /usr/local/bin/ 2>/dev/null || mv ./code /usr/local/bin/ 2>/dev/null || chmod +x /usr/local/bin/code 2>/dev/null"
```

## Tạo file start-all.sh
```bash
printf '#!/data/data/com.termux/files/usr/bin/bash\n\n' > ~/start-all.sh
printf 'echo "=========================================="\n' >> ~/start-all.sh
printf 'echo "1. Dang khoi dong MariaDB (MySQL)..."\n' >> ~/start-all.sh
printf 'echo "=========================================="\n' >> ~/start-all.sh
printf 'if ! pgrep -x "mariadbd" > /dev/null; then\n' >> ~/start-all.sh
printf '    mariadbd-safe > /dev/null 2>&1 &\n' >> ~/start-all.sh
printf '    sleep 2\n' >> ~/start-all.sh
printf '    echo "[OK] MariaDB Server da duoc bat!"\n' >> ~/start-all.sh
printf 'else\n' >> ~/start-all.sh
printf '    echo "[!] MariaDB Server dang chay roi."\n' >> ~/start-all.sh
printf 'fi\n\n' >> ~/start-all.sh

printf 'echo "=========================================="\n' >> ~/start-all.sh
printf 'echo "2. Dang khoi dong Redis Server..."\n' >> ~/start-all.sh
printf 'echo "=========================================="\n' >> ~/start-all.sh
printf 'if ! pgrep -x "redis-server" > /dev/null; then\n' >> ~/start-all.sh
printf '    redis-server --daemonize yes > /dev/null 2>&1\n' >> ~/start-all.sh
printf '    sleep 1\n' >> ~/start-all.sh
printf '    echo "[OK] Redis Server da duoc bat!"\n' >> ~/start-all.sh
printf 'else\n' >> ~/start-all.sh
printf '    echo "[!] Redis Server dang chay roi."\n' >> ~/start-all.sh
printf 'fi\n\n' >> ~/start-all.sh

printf 'echo "=========================================="\n' >> ~/start-all.sh
printf 'echo "3. Dang khoi dong VS Code Tunnel trong Ubuntu..."\n' >> ~/start-all.sh
printf 'echo "=========================================="\n\n' >> ~/start-all.sh
printf 'proot-distro login ubuntu -- code tunnel\n' >> ~/start-all.sh

chmod +x ~/start-all.sh
```
## Chạy 1 lần bằng cách gõ dev
```bash
echo "alias dev='~/start-all.sh'" >> ~/.bashrc && source ~/.bashrc
```

## Ghi chú

* Thiết bị cần có kết nối Internet ổn định.
* Không đóng Termux khi đang sử dụng Tunnel.
* Đăng nhập bằng tài khoản Microsoft hoặc GitHub để sử dụng VS Code Tunnel.
* Hiệu năng phụ thuộc vào cấu hình điện thoại và chất lượng mạng.
* Khi tải một công cụ mới thì nên reload lại trang để vsccode cập nhật.
