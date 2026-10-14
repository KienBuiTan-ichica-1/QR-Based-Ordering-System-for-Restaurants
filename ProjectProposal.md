# Project Proposal: Hệ thống gọi món bằng QR cho quán ăn

Môn: Nhập môn Công nghệ phần mềm. Quy trình: Waterfall.

## 1. Bảng đánh giá thành viên

| MSSV | Họ tên | % đóng góp (tối đa 100%) | Chữ ký |
|---|---|---|---|
| | | | |
| | | | |
| | | | |
| | | | |

## 2. Phát biểu bài toán sơ lược

Quán ăn nhỏ thường ghi order bằng giấy. Khách phải gọi nhân viên mỗi lần muốn gọi thêm món, đơn dễ ghi sai hoặc thất lạc, bếp không biết đơn nào đến trước, và cuối ngày chủ quán phải cộng tay để biết doanh thu. Giờ cao điểm các vấn đề này nặng hơn vì nhân viên không đủ người.

Dự án xây dựng một web app cho phép khách quét mã QR dán trên bàn, xem thực đơn và gọi món ngay trên điện thoại. Đơn được chuyển đến bếp tức thì. Thu ngân gộp các đơn của một bàn thành một hoá đơn để thanh toán. Quản lý cập nhật thực đơn và xem thống kê doanh thu.

Phần mềm có bốn nhóm người dùng: khách, nhân viên bếp, thu ngân và quản lý.

**Môi trường hoạt động**
- Khách dùng trình duyệt trên điện thoại (Chrome, Safari), không cần cài ứng dụng.
- Nhân viên dùng trình duyệt trên máy tính bảng hoặc máy tính ở bếp và quầy.
- Server chạy trên máy chủ Windows (Windows Server) có kết nối Internet.

**Ràng buộc thiết kế và triển khai**
- Web app, giao diện ưu tiên màn hình điện thoại.
- Frontend: HTML/CSS/JavaScript với React. Backend: Node.js (Express). Cập nhật thời gian thực bằng WebSocket (Socket.IO). Cơ sở dữ liệu: PostgreSQL.
- Thanh toán tại quầy: hệ thống tạo mã QR chuyển khoản theo chuẩn VietQR, có sẵn số tiền và mã hoá đơn. Thu ngân xác nhận đã nhận tiền bằng tay. Không tích hợp cổng thanh toán, không tự động đối soát với ngân hàng.
- Phạm vi một quán, một chi nhánh.
- Tài liệu theo markdown kèm html, quản lý mã nguồn bằng Git.

**Ngoài phạm vi:** quản lý kho, nhân sự và lương, đặt bàn trước, giao hàng, khuyến mãi, tự động đối soát thanh toán, ứng dụng mobile riêng.

## 3. Giải pháp đề xuất

### 3.1. Phần mềm

#### 3.1.1. Danh sách các chức năng phần mềm

Bảng câu chuyện người dùng:

| Nhu cầu | Yêu cầu |
|---|---|
| Là khách, tôi muốn quét QR ở bàn để xem thực đơn mà không cần tải ứng dụng | Xem thực đơn qua QR |
| Là khách, tôi muốn chọn món, số lượng và ghi chú (ít cay, không hành), rồi gửi đơn | Gọi món |
| Là khách, tôi muốn gọi thêm món khi đang ăn mà không phải gọi nhân viên | Gọi thêm vào bàn đang mở |
| Là khách, tôi muốn biết món của mình đang chờ, đang làm hay đã xong | Theo dõi trạng thái món |
| Là nhân viên bếp, tôi muốn thấy đơn mới ngay khi khách gửi, theo thứ tự đến | Nhận đơn thời gian thực |
| Là nhân viên bếp, tôi muốn cập nhật trạng thái món và báo hết món | Cập nhật trạng thái, báo hết món |
| Là thu ngân, tôi muốn chốt hoá đơn của bàn để hệ thống tính tổng tiền và hiện mã QR thanh toán | Chốt hoá đơn, tạo QR thanh toán |
| Là thu ngân, tôi muốn xác nhận đã nhận tiền để đóng bàn cho khách mới | Xác nhận thanh toán, đóng bàn |
| Là quản lý, tôi muốn thêm, sửa, ẩn món, đổi giá và tạo QR cho từng bàn | Quản lý thực đơn, bàn, QR |
| Là nhân viên, tôi muốn đăng nhập để chỉ thấy chức năng thuộc vai trò của mình | Đăng nhập, phân quyền |
| Là quản lý, tôi muốn xem doanh thu theo ngày, tuần, tháng và món bán chạy | Thống kê doanh thu |
| Là khách, tôi muốn gửi đơn và nhận phản hồi trong vòng 3 giây | Phản hồi nhanh |

Năm chức năng chính và quy định liên quan:

| # | Chức năng | Quy định |
|---|---|---|
| 1 | Gọi món qua QR theo bàn | Mỗi bàn có một mã QR riêng. Mỗi bàn chỉ có một phiên gọi món mở tại một thời điểm, nhiều khách cùng bàn gọi vào cùng phiên. Món đang ẩn hoặc hết thì không gọi được. |
| 2 | Xử lý đơn ở bếp theo thời gian thực | Trạng thái món: chờ, đang làm, xong, huỷ. Chỉ huỷ được món còn ở trạng thái chờ. |
| 3 | Thanh toán và hoá đơn theo bàn | Khi thu ngân chốt hoá đơn, hệ thống tính tổng các món chưa huỷ của phiên, không cho gọi thêm, rồi tạo mã QR chuyển khoản chứa số tiền và mã hoá đơn. Sau khi thu ngân xác nhận đã nhận tiền (QR hoặc tiền mặt), phiên đóng và bàn sẵn sàng cho khách mới. |
| 4 | Quản lý thực đơn, bàn, QR; đăng nhập, phân quyền | Ba vai trò nhân viên: bếp, thu ngân, quản lý. Chỉ quản lý được sửa thực đơn, giá và bàn. |
| 5 | Thống kê doanh thu | Chỉ tính hoá đơn đã thanh toán. Chỉ quản lý được xem. |

#### 3.1.2. Kiến trúc tổng thể phần mềm

Hệ thống theo mô hình client-server, ba lớp.

```
 Điện thoại khách        Màn hình bếp / quầy thu ngân / máy quản lý
 (trình duyệt)                     (trình duyệt)
        \                              /
         \----- HTTP + WebSocket ------/
                      |
              Server (Node.js, Express)
              - API gọi món, thực đơn, hoá đơn, QR thanh toán, thống kê
              - Xác thực và phân quyền
              - Socket.IO đẩy đơn mới / đổi trạng thái
                      |
               PostgreSQL
```

- **Giao diện khách:** mở từ link trong mã QR, link chứa mã bàn. Dùng cho chức năng 1.
- **Giao diện nhân viên:** một web app chung, hiển thị màn hình theo vai trò. Màn hình bếp cho chức năng 2, màn hình thu ngân cho chức năng 3, màn hình quản lý cho chức năng 4 và 5.
- **Server:** xử lý nghiệp vụ, kiểm tra quyền, ghi và đọc dữ liệu. Khi có đơn mới hoặc đổi trạng thái, server đẩy thông báo qua WebSocket tới bếp và tới khách của bàn đó.
- **Cơ sở dữ liệu:** lưu thực đơn, bàn, phiên gọi món, đơn, hoá đơn, tài khoản.

### 3.2. Phần cứng

| Thiết bị | Yêu cầu |
|---|---|
| Điện thoại khách | Có camera quét QR và trình duyệt hiện đại, có mạng |
| Màn hình bếp | Máy tính bảng hoặc máy tính, 1 thiết bị, luôn kết nối mạng |
| Quầy thu ngân | Máy tính hoặc máy tính bảng, 1 thiết bị |
| Máy quản lý | Máy tính hoặc điện thoại của chủ quán |
| Server | Máy chủ Windows (Windows Server), tối thiểu 2 vCPU, 4 GB RAM, 40 GB ổ đĩa |
| Mạng | Wi-Fi ổn định trong quán |
| Máy in hoá đơn | Không bắt buộc |

## 4. Kế hoạch phát triển

Nhóm dùng mô hình Waterfall. Mỗi giai đoạn kết thúc bằng thành phẩm cụ thể, giai đoạn sau chỉ bắt đầu khi giai đoạn trước nộp xong.

| Giai đoạn | Thời gian | Thành phẩm |
|---|---|---|
| Phân tích yêu cầu | 2 tuần | Tài liệu đặc tả yêu cầu: bảng yêu cầu chi tiết, use case, mô tả nghiệp vụ |
| Thiết kế phần mềm | 2 tuần | Tài liệu thiết kế: sơ đồ kiến trúc, thiết kế CSDL, thiết kế API, bản phác giao diện |
| Cài đặt | 5 tuần | Mã nguồn trên Git, bản chạy được của năm chức năng |
| Kiểm thử | 2 tuần | Kế hoạch và ca kiểm thử, báo cáo kết quả, danh sách lỗi và việc sửa |
| Triển khai, bảo trì | 1 tuần | Bản triển khai trên server, hướng dẫn cài đặt và sử dụng |

## 5. Kế hoạch nhân sự và chi phí

### 5.1. Nhân sự

Nhóm 4 thành viên, mỗi người khoảng 8 giờ mỗi tuần.

| Thành viên | Phụ trách | Công việc chung |
|---|---|---|
| Thành viên 1 (nhóm trưởng) | Chức năng 3: thanh toán, hoá đơn | Điều phối, họp nhóm, theo dõi tiến độ, tổng hợp bài nộp |
| Thành viên 2 | Chức năng 1: giao diện khách, gọi món qua QR | Viết tài liệu và kiểm thử phần mình làm |
| Thành viên 3 | Chức năng 2: bếp, thời gian thực | Viết tài liệu và kiểm thử phần mình làm |
| Thành viên 4 | Chức năng 4 và 5: quản lý, phân quyền, thống kê; thiết kế CSDL | Viết tài liệu và kiểm thử phần mình làm |

Ở các giai đoạn phân tích và thiết kế, cả nhóm cùng tham gia, mỗi người viết phần của chức năng mình phụ trách. Khi kiểm thử, các thành viên kiểm thử chéo phần của nhau.

### 5.2. Chi phí dự kiến

| Khoản | Đơn giá ước tính | Thành tiền |
|---|---|---|
| Thuê máy chủ Windows (VPS) | 400.000 đ/tháng × 3 tháng | 1.200.000 đ |
| Tên miền | 250.000 đ/năm | 250.000 đ |
| In mã QR dán bàn | 5.000 đ × 10 bàn | 50.000 đ |
| Công sức nhóm | 4 người × 8 giờ × 12 tuần | Không tính tiền |
| **Tổng** | | **1.500.000 đ** |

Các khoản tiền là ước tính, nhóm sẽ cập nhật theo giá thực tế khi triển khai.
