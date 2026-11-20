# Đề 3 · Website mua quần áo

> **Mã:** DE03 · **Độ khó:** 3/8 · **Vai trò:** 1 (khách mua: khách vãng lai hoặc thành viên) · **Nền tảng:** Website (desktop và mobile)

Website bán quần áo của một thương hiệu thời trang: khách duyệt và tìm sản phẩm, chọn đúng màu và size, đặt hàng không cần tài khoản, thanh toán khi nhận hàng hoặc chuyển khoản QR, theo dõi đơn và tự đổi size, trả hàng. Đây là đề đầu tiên có **dữ liệu từ máy chủ** (tồn kho, giá, đơn hàng) và **thanh toán**. Đề gồm 4 tính năng; mỗi tính năng có 1 PRD và 1 SRS riêng.

## Tính năng

| **Mã** | **Tính năng** | **Phạm vi** | **PRD** | **SRS** |
| --- | --- | --- | --- | --- |
| TN01 | Duyệt & tìm sản phẩm | Trang chủ, danh mục, lọc size, màu, giá; sắp xếp; tìm kiếm không dấu | [PRD](docs/TN01-duyet-tim-san-pham/PRD.md) | [SRS](docs/TN01-duyet-tim-san-pham/SRS.md) |
| TN02 | Chi tiết sản phẩm & chọn size | Ảnh theo màu, tồn kho theo biến thể, gợi ý size, thêm giỏ, yêu thích, báo có hàng | [PRD](docs/TN02-chi-tiet-san-pham-chon-size/PRD.md) | [SRS](docs/TN02-chi-tiet-san-pham-chon-size/SRS.md) |
| TN03 | Giỏ hàng & thanh toán | Sửa giỏ, phí giao, mã giảm giá, khách vãng lai, COD, QR, thẻ/ví, giữ hàng | [PRD](docs/TN03-gio-hang-thanh-toan/PRD.md) | [SRS](docs/TN03-gio-hang-thanh-toan/SRS.md) |
| TN04 | Đơn hàng, huỷ & đổi trả | Đơn của tôi, tra cứu đơn, theo dõi giao, huỷ, đổi size, trả hàng hoàn tiền | [PRD](docs/TN04-don-hang-doi-tra/PRD.md) | [SRS](docs/TN04-don-hang-doi-tra/SRS.md) |

Thứ tự làm gợi ý: TN01 → TN02 → TN03 → TN04.

## Vòng đời đơn hàng (dùng chung cả đề)

```mermaid
stateDiagram-v2
  [*] --> CHO_THANH_TOAN : Đặt hàng QR, thẻ, ví, TN03
  [*] --> CHO_XAC_NHAN : Đặt hàng COD, TN03
  CHO_THANH_TOAN --> CHO_XAC_NHAN : Nhận đủ tiền, TN03
  CHO_THANH_TOAN --> DA_HUY : Quá 15 phút hoặc khách huỷ, TN03, TN04
  CHO_XAC_NHAN --> DANG_CHUAN_BI : Shop xác nhận
  CHO_XAC_NHAN --> DA_HUY : Khách huỷ, TN04
  DANG_CHUAN_BI --> DA_HUY : Yêu cầu huỷ được duyệt, TN04
  DANG_CHUAN_BI --> DANG_GIAO : Bàn giao vận chuyển
  DANG_GIAO --> DA_GIAO : Giao thành công
  DANG_GIAO --> GIAO_THAT_BAI : Thất bại 3 lần, TN04
  GIAO_THAT_BAI --> DA_HUY : Hàng về shop
  DA_GIAO --> CO_DOI_TRA : Khách tạo yêu cầu đổi trả, TN04
  DA_GIAO --> HOAN_THANH : Hết 7 ngày
  CO_DOI_TRA --> HOAN_THANH : Yêu cầu kết thúc, hết 7 ngày
  DA_HUY --> [*]
  HOAN_THANH --> [*]
```

Các bước của shop (xác nhận, đóng gói, duyệt đổi trả) và đơn vị vận chuyển do hệ thống nội bộ cập nhật; đề chỉ thiết kế **phía khách**.

## Bản đồ tính năng và màn hình

```mermaid
flowchart LR
  subgraph TN01["TN01 · Duyệt & tìm"]
    S01["SCR-01 Trang chủ"]
    S02["SCR-02 Danh sách sản phẩm"]
    S03["SCR-03 Bộ lọc"]
    S04["SCR-04 Tìm kiếm"]
  end
  subgraph TN02["TN02 · Chi tiết & size"]
    S05["SCR-05 Chi tiết sản phẩm"]
    S06["SCR-06 Bảng size & gợi ý"]
    S07["SCR-07 Yêu thích"]
  end
  subgraph TN03["TN03 · Giỏ & thanh toán"]
    S08["SCR-08 Giỏ hàng"]
    S09["SCR-09 Thanh toán"]
    S10["SCR-10 Kết quả đặt hàng"]
  end
  subgraph TN04["TN04 · Đơn & đổi trả"]
    S11["SCR-11 Đơn của tôi"]
    S12["SCR-12 Chi tiết đơn"]
    S13["SCR-13 Yêu cầu đổi trả"]
    S14["SCR-14 Tra cứu đơn"]
  end
  S01 --> S02
  S01 --> S04 --> S02
  S02 <--> S03
  S02 --> S05
  S05 <--> S06
  S07 --> S05
  S05 -->|Thêm giỏ| S08
  S05 -->|Mua ngay| S09
  S08 --> S09 --> S10
  S10 --> S12
  S11 --> S12
  S14 --> S12
  S12 --> S13
  S12 -->|Mua lại| S08
```

## Bối cảnh chung

Shop bán chủ yếu qua quảng cáo mạng xã hội; phần lớn khách vào web bằng điện thoại, mua lần đầu và không muốn tạo tài khoản. Hai nỗi lo lớn nhất khi mua quần áo online là **không vừa size** và **mất tiền mà không nhận được hàng như ý**. Bài toán thiết kế chính là giúp khách chọn đúng size, thấy rõ tổng tiền từ sớm, và tin rằng đổi trả là dễ dàng.

| **Nhóm người dùng** | **Nhu cầu chính** |
| --- | --- |
| Khách vào từ quảng cáo, dùng điện thoại | Thấy đúng món đang quảng cáo, mua nhanh không cần tài khoản |
| Khách quen (thành viên) | Địa chỉ đã lưu, xem lại đơn cũ, mua lại |
| Khách lo về size | Gợi ý size, bảng size riêng từng mẫu, đổi size miễn phí |
| Khách quen trả tiền khi nhận hàng | COD, hoặc QR được xác nhận ngay |

Ngoài phạm vi cả đề: app di động, trang quản trị (sản phẩm, đơn, kho, duyệt đổi trả), đánh giá sản phẩm, chat với shop, điểm thưởng và hạng thành viên, trả góp, nhiều kho và nhiều chi nhánh, bán hàng quốc tế.

## Quy ước dùng chung

- Tiền tệ VND, không có số lẻ; định dạng `499.000 ₫`.
- Địa chỉ theo đơn vị hành chính 2 cấp: Tỉnh/Thành phố → Phường/Xã.
- Thành viên đăng nhập bằng số điện thoại và OTP theo luồng chuẩn, không mô tả trong bộ tài liệu này.
- Mã BR, MSG, UC đánh số liên tục trong cả đề: TN01 dùng BR-01–10, MSG-01–06, UC-01–02; TN02 dùng BR-11–20, MSG-07–13, UC-03–05; TN03 dùng BR-21–34, MSG-14–26, UC-06–07; TN04 dùng BR-35–46, MSG-27–36, UC-08–10.
- Breakpoint: mobile 360–767 px, tablet 768–1023 px, desktop ≥ 1024 px. Thiết kế mobile trước.

## Danh sách màn hình

| **Mã** | **Màn hình** | **Mô tả chi tiết ở** |
| --- | --- | --- |
| SCR-01 | Trang chủ | SRS TN01 |
| SCR-02 | Danh sách sản phẩm (danh mục, kết quả tìm) | SRS TN01 |
| SCR-03 | Bộ lọc | SRS TN01 |
| SCR-04 | Tìm kiếm | SRS TN01 |
| SCR-05 | Chi tiết sản phẩm | SRS TN02 |
| SCR-06 | Bảng size & gợi ý size | SRS TN02 |
| SCR-07 | Yêu thích | SRS TN02 |
| SCR-08 | Giỏ hàng | SRS TN03 |
| SCR-09 | Thanh toán | SRS TN03 |
| SCR-10 | Kết quả đặt hàng | SRS TN03 |
| SCR-11 | Đơn của tôi | SRS TN04 |
| SCR-12 | Chi tiết đơn | SRS TN04 |
| SCR-13 | Yêu cầu đổi trả | SRS TN04 |
| SCR-14 | Tra cứu đơn | SRS TN04 |

## Cấu trúc repo

```text
de03-website-mua-quan-ao/
├── README.md
└── docs/
    ├── TN01-duyet-tim-san-pham/          PRD.md · SRS.md
    ├── TN02-chi-tiet-san-pham-chon-size/ PRD.md · SRS.md
    ├── TN03-gio-hang-thanh-toan/         PRD.md · SRS.md
    └── TN04-don-hang-doi-tra/            PRD.md · SRS.md
```

## Cách dùng bộ tài liệu

1. Đọc README để nắm vòng đời đơn hàng và danh sách màn hình.
2. Với mỗi tính năng: đọc PRD (vì sao làm, cho ai, đo bằng gì), rồi SRS (quy tắc, trạng thái, use case, màn hình, thông báo).
3. Thiết kế **cả mobile và desktop** cho mỗi màn; mobile là ưu tiên.
4. Thiết kế màn hình theo đúng mã SCR, mã MSG trong SRS để dễ đối chiếu khi review.
5. Sơ đồ viết bằng Mermaid, xem trực tiếp trên GitHub hoặc trình xem Markdown có hỗ trợ Mermaid.
