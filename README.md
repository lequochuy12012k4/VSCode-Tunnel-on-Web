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
apt update && apt install wget -y
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

## Các lệnh cài đặt nhanh

Có thể chạy toàn bộ các lệnh sau theo thứ tự:

```bash
pkg install proot-distro -y

proot-distro install ubuntu

proot-distro login ubuntu

apt update && apt install wget -y

rm -f code vscode_cli.tar.gz

wget -O vscode_cli.tar.gz "https://code.visualstudio.com/sha/download?build=stable&os=cli-alpine-arm64"

tar -xf vscode_cli.tar.gz

./code tunnel
```

---

## Ghi chú

* Thiết bị cần có kết nối Internet ổn định.
* Không đóng Termux khi đang sử dụng Tunnel.
* Đăng nhập bằng tài khoản Microsoft hoặc GitHub để sử dụng VS Code Tunnel.
* Hiệu năng phụ thuộc vào cấu hình điện thoại và chất lượng mạng.
* Khi tải một công cụ mới thì nên reload lại trang để vsccode cập nhật.
