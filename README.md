# QR-Based Ordering System for Restaurants

Hệ thống gọi món bằng mã QR cho quán ăn — khách quét mã QR tại bàn, xem thực đơn và gọi món ngay trên điện thoại; đơn được chuyển tới bếp theo thời gian thực và thu ngân chốt hoá đơn theo bàn.

Môn: **Nhập môn Công nghệ phần mềm** — Quy trình phát triển: **Waterfall**.

## Tài liệu

| Tài liệu | Định dạng |
|---|---|
| [Đề xuất dự án](./ProjectProposal.md) | Markdown |
| [Đề xuất dự án](./ProjectProposal.html) | HTML |

## Tính năng chính

1. **Gọi món qua QR theo bàn** — mỗi bàn có một mã QR riêng, nhiều khách cùng bàn gọi vào một phiên.
2. **Xử lý đơn ở bếp theo thời gian thực** — trạng thái món: chờ, đang làm, xong, huỷ.
3. **Thanh toán và hoá đơn theo bàn** — chốt hoá đơn, tạo mã QR chuyển khoản (VietQR) kèm số tiền và mã hoá đơn.
4. **Quản lý thực đơn, bàn, QR; đăng nhập, phân quyền** — ba vai trò nhân viên: bếp, thu ngân, quản lý.
5. **Thống kê doanh thu** — theo ngày, tuần, tháng và món bán chạy; chỉ tính hoá đơn đã thanh toán.

## Kiến trúc & Công nghệ

- **Kiến trúc:** client-server, ba lớp.
- **Frontend:** HTML/CSS/JavaScript với React (ưu tiên giao diện điện thoại).
- **Backend:** Node.js (Express); cập nhật thời gian thực bằng WebSocket (Socket.IO).
- **Cơ sở dữ liệu:** PostgreSQL.
- **Thanh toán:** tạo mã VietQR tại quầy, thu ngân xác nhận đã nhận tiền bằng tay (không tích hợp cổng thanh toán).

```
Điện thoại khách        Màn hình bếp / quầy thu ngân / máy quản lý
(trình duyệt)                     (trình duyệt)
        \                              /
         \----- HTTP + WebSocket ------/
                      |
              Server (Node.js, Express)
                      |
               PostgreSQL
```

## Phạm vi

- Phạm vi một quán, một chi nhánh.
- **Ngoài phạm vi:** quản lý kho, nhân sự và lương, đặt bàn trước, giao hàng, khuyến mãi, tự động đối soát thanh toán, ứng dụng mobile riêng.

## Phát triển

Dự án theo mô hình **Waterfall**, mỗi giai đoạn kết thúc bằng thành phẩm cụ thể:

| Giai đoạn | Thời gian |
|---|---|
| Phân tích yêu cầu | 2 tuần |
| Thiết kế phần mềm | 2 tuần |
| Cài đặt | 5 tuần |
| Kiểm thử | 2 tuần |
| Triển khai, bảo trì | 1 tuần |

## Nhóm thực hiện

Nhóm 4 thành viên, mỗi người khoảng 8 giờ mỗi tuần. Chi tiết phân công và chi phí dự kiến xem tại [Đề xuất dự án](./ProjectProposal.md).

## Giấy phép

Dự án phục vụ mục đích học tập trong môn Nhập môn Công nghệ phần mềm.
