# 🇻🇳 ECOVACS APP & SERVER TRUNG GIAN VIỆT NAM (EcovacsNoiDiaVN)

> **Ứng dụng iOS Native (SwiftUI)** điều khiển trực tiếp Robot Ecovacs nội địa Trung Quốc thông qua hạ tầng **Server Trung Gian VN (`icouser.net`)**, triệt để loại bỏ lỗi khóa vùng (geoblock) và không bị kiểm tra định vị GPS tại Việt Nam.

---

## 📌 1. Thông Tin Định Danh Ứng Dụng

- **Tên hiển thị (Display Name):** `EcovacsNoiDiaVN`
- **Mã gói định danh (Bundle Identifier):** `com.robot.vn.app`
- **Hệ điều hành yêu cầu:** iOS 15.0 trở lên
- **Công nghệ cốt lõi:** Swift 5.0, SwiftUI, Apple Network.framework (NWConnection Socket TLS 8883), Apple Keychain, Combine

---

## 🌐 2. Hạ Tầng Server Trung Gian Đang Kết Nối

Ứng dụng được thiết kế tương thích hoàn hảo với cụm server trung gian bạn đã cung cấp:

| Dịch vụ | Địa chỉ Domain / Cổng | Vai trò kỹ thuật |
| :--- | :--- | :--- |
| **Service Discovery** | `https://app.icouser.net/api/appsvr/service/list?area=CN` | Tự động phát hiện và cập nhật các endpoint server trung gian khi mở app |
| **Auth Gateway** | `https://app.icouser.net` | Đăng nhập tài khoản, xác thực `loginCheckMobile` & `getAuthCode` |
| **Portal User API** | `https://app.icouser.net/api/users/user.do` | Quản lý phiên làm việc, cấp User Token, truy xuất danh sách Robot |
| **MQTT Broker TLS** | `iot.icouser.net:8883` | Kết nối Socket thời gian thực, điều khiển tức thì (~8ms) |
| **Realm** | `icouser.net` | Không gian định danh tài khoản nội địa trên server trung gian |

---

## ✨ 3. Các Tính Năng Đã Triển Khai

- **Tự động Service Discovery:** Tự động gọi endpoint trung gian khi mở app để tải danh sách địa chỉ mới nhất.
- **Đăng nhập & Lưu phiên an toàn:** Lưu mật khẩu và token mã hóa trong **iOS Keychain** bảo mật của Apple, tự động gia hạn token ngầm (Silent Auto-Refresh).
- **Quản lý thiết bị Robot:** Hiển thị danh sách thiết bị kèm tên thân thiện (T10 TURBO, T9 AIVI, T20, X1...), mức pin % thực tế và trạng thái sạc.
- **Điều khiển dọn dẹp:** Bắt đầu (Start), Tạm dừng (Pause), Tiếp tục (Resume), Dừng hẳn (Stop) và Gọi robot quay về trạm sạc pin (Charge).
- **Điều chỉnh công suất:**
  - 4 mức lực hút bụi: *Yên tĩnh*, *Tiêu chuẩn*, *Mạnh (Max)*, *Cực đại (Max+)*.
  - 4 mức lượng nước lau sàn: *Ít nước*, *Vừa phải*, *Nhiều nước*, *Tối đa*.
- **Bản đồ LiDAR thời gian thực (Live Map Canvas):** Vẽ sơ đồ sàn, định vị tọa độ robot, trạm sạc và quỹ đạo dọn dẹp.
- **Theo dõi & Đặt lại tuổi thọ linh kiện (Consumables):** Giám sát chổi chính, chổi phụ, lọc HEPA, cảm biến rơi kèm nút đặt lại 100% khi vừa thay mới.
- **Cài đặt Server linh hoạt:** Cho phép chuyển đổi hoặc cấu hình IP/Domain máy chủ trung gian riêng theo nhu cầu.

---

## 🏗️ 4. Cấu Trúc Dự Án Mã Nguồn

```text
App Ecovacs Server Trung Gian VN/
├── README.md                                  # Tài liệu kiến trúc và hướng dẫn
├── .github/
│   └── workflows/
│       └── build-ipa.yml                      # CI/CD tự động build file IPA trên GitHub
├── EcovacsNoiDiaVN.xcodeproj/
│   └── project.pbxproj                        # Cấu hình dự án Xcode (Bundle ID: com.robot.vn.app)
└── EcovacsNoiDiaVN/
    ├── App/
    │   ├── EcovacsNoiDiaVNApp.swift           # Điểm khởi chạy ứng dụng @main
    │   └── AppState.swift                     # Quản lý trạng thái chuyển màn hình
    ├── Core/
    │   ├── ServerConfig.swift                 # Cấu hình máy chủ icouser.net & custom
    │   ├── Constants.swift                    # Thông số API, model mapping, mã lỗi tiếng Việt
    │   ├── CryptoHelper.swift                 # Thuật toán MD5 và chữ ký authSign Ecovacs
    │   └── KeychainManager.swift              # Lưu trữ an toàn Apple Keychain
    ├── Models/
    │   ├── ServiceListResponse.swift          # Model parse kết quả /api/appsvr/service/list
    │   ├── DeviceModel.swift                  # Mô hình dữ liệu Robot
    │   ├── RobotState.swift                   # Trạng thái pin, dọn dẹp, lực hút, mức nước
    │   ├── ConsumablesModel.swift             # Dữ liệu hao mòn phụ kiện
    │   └── MapModel.swift                     # Dữ liệu bản đồ LiDAR và tọa độ
    ├── Services/
    │   ├── ServiceDiscoveryService.swift      # Fetch endpoint động từ Server Trung Gian
    │   ├── EcovacsAuthService.swift           # Đăng nhập 3 bước qua Gateway Trung Gian
    │   ├── EcovacsDeviceService.swift         # REST API điều khiển thiết bị
    │   └── EcovacsMQTTService.swift           # Socket TLS 8883 tới iot.icouser.net
    ├── ViewModels/
    │   ├── LoginViewModel.swift               # Logic đăng nhập & kiểm tra server
    │   ├── DeviceListViewModel.swift          # Danh sách robot & quản lý socket
    │   └── RobotDetailViewModel.swift         # Bảng điều khiển toàn diện
    ├── Views/
    │   ├── LoginView.swift                    # Giao diện đăng nhập
    │   ├── ServerSettingsView.swift           # Màn hình cấu hình Server Trung Gian
    │   ├── DeviceListView.swift               # Giao diện chọn thiết bị
    │   ├── RobotDetailView.swift              # Màn hình điều khiển chính
    │   ├── LiveMapView.swift                  # Canvas bản đồ LiDAR
    │   └── ConsumablesView.swift              # Giao diện linh kiện hao mòn
    └── Resources/
        ├── Info.plist                         # Cấu hình bundle com.robot.vn.app & quyền mạng
        └── Assets.xcassets/                   # AppIcon và Color Assets
```

---

## 📲 5. Hướng Dẫn Đóng Gói File IPA

### Cách 1: Tự động đóng gói qua GitHub Actions (Khuyên dùng - Không cần Mac)
1. Khởi tạo Git repository trong thư mục này và push lên GitHub.
2. Vào tab **Actions** trên GitHub > Chọn workflow **Build & Release EcovacsNoiDiaVN IPA** > Bấm **Run workflow**.
3. Hệ thống máy chủ macOS của GitHub sẽ tự động biên dịch và xuất ra file `EcovacsNoiDiaVN_v1.0.0.ipa` tại mục Releases hoặc Artifacts.
4. Tải file `.ipa` về điện thoại và cài đặt bằng **TrollStore** (vĩnh viễn, không lo thu hồi chứng chỉ) hoặc **Sideloadly** / **AltStore**.

### Cách 2: Mở trực tiếp bằng Xcode trên máy tính Mac
1. Mở file `EcovacsNoiDiaVN.xcodeproj`.
2. Chọn thiết bị iPhone của bạn hoặc Generic iOS Device.
3. Chọn scheme `EcovacsNoiDiaVN` và bấm `Cmd + R` để chạy thử nghiệm hoặc `Product -> Archive` để xuất file IPA.
