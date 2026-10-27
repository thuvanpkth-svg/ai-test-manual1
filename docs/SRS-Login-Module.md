# Software Requirements Specification (SRS)
## Module: Login (Đăng nhập)
### Hệ thống: Perfex CRM – Anh Tester Demo
**Nguồn khảo sát:** https://crm.anhtester.com/admin/authentication
**Ngày khảo sát:** 2026-10-27
**Người thực hiện:** QA/Tester

---

## 1. Giới thiệu

### 1.1 Mục đích
Tài liệu này mô tả các yêu cầu chức năng và phi chức năng của module **Login** trong hệ thống Perfex CRM (bản demo của Anh Tester), dùng làm cơ sở để thiết kế test case, thực hiện kiểm thử và đối chiếu hành vi hệ thống.

### 1.2 Phạm vi
Module Login bao gồm 3 luồng chính:
- Đăng nhập (Login)
- Quên mật khẩu (Forgot Password)
- Đăng xuất (Logout)

### 1.3 Đối tượng sử dụng
Quản trị viên / nhân viên nội bộ truy cập khu vực quản trị (`/admin`) của hệ thống CRM.

### 1.4 Thuật ngữ
| Từ viết tắt | Ý nghĩa |
|---|---|
| SRS | Software Requirements Specification |
| CRM | Customer Relationship Management |

---

## 2. Mô tả tổng quan

Trang đăng nhập được truy cập tại URL: `https://crm.anhtester.com/admin/authentication`.

Giao diện gồm:
- Logo hệ thống ("Perfex CRM | Anh Tester Demo")
- Tiêu đề "Login"
- Form đăng nhập gồm:
  - Trường **Email Address** (input type `email`, bắt buộc)
  - Trường **Password** (input type `password`, bắt buộc)
  - Checkbox **Remember me**
  - Nút **Login** (submit)
  - Liên kết **Forgot Password?**

Sau khi đăng nhập thành công, hệ thống chuyển hướng tới trang **Dashboard** (`/admin/`).

---

## 3. Yêu cầu chức năng (Functional Requirements)

### FR-01: Đăng nhập với thông tin hợp lệ
**Mô tả:** Người dùng nhập đúng Email và Password đã đăng ký, bấm **Login**.
**Kết quả mong đợi:** Hệ thống xác thực thành công, chuyển hướng đến trang Dashboard, hiển thị menu điều hướng (Customers, Projects, Tasks, Contracts, Sales, Subscriptions, Expenses, Support, Leads, …) và avatar người dùng ở góc phải thanh điều hướng.
**Đã xác minh thực tế:** Đăng nhập với `admin@example.com` / `123456` → chuyển hướng thành công tới `/admin/` (Dashboard).

### FR-02: Đăng nhập với thông tin không hợp lệ
**Mô tả:** Người dùng nhập Email/Password sai (không khớp tài khoản tồn tại).
**Kết quả mong đợi:** Hệ thống không cho đăng nhập, hiển thị thông báo lỗi chung, không tiết lộ email hay password sai cụ thể.
**Đã xác minh thực tế:** Thông báo hiển thị: `Invalid email or password`.

### FR-03: Bỏ trống trường bắt buộc
**Mô tả:** Người dùng bấm **Login** khi để trống Email và/hoặc Password.
**Kết quả mong đợi:** Hệ thống chặn submit, hiển thị thông báo lỗi cho từng trường còn thiếu.
**Đã xác minh thực tế:**
- Bỏ trống cả 2 trường → hiển thị 2 thông báo: `The Password field is required.` và `The Email Address field is required.`

### FR-04: Checkbox "Remember me"
**Mô tả:** Cho phép hệ thống ghi nhớ phiên đăng nhập của người dùng trên thiết bị hiện tại cho các lần truy cập sau.
**Trạng thái mặc định:** Không được chọn (unchecked).

### FR-05: Chức năng Quên mật khẩu (Forgot Password)
**Đường dẫn:** `/admin/authentication/forgot_password`, truy cập qua liên kết "Forgot Password?" trên trang Login.
**Mô tả:** Người dùng nhập Email đã đăng ký và bấm **Confirm** để yêu cầu đặt lại mật khẩu.
**Giao diện:** 1 trường **Email Address** (bắt buộc) + nút **Confirm**.
**Lưu ý hành vi đã quan sát:** Nếu người dùng đã đăng nhập (có phiên hợp lệ) và truy cập trực tiếp URL này, hệ thống tự động chuyển hướng về Dashboard thay vì hiển thị form quên mật khẩu.

### FR-06: Đăng xuất (Logout)
**Đường dẫn:** `/admin/authentication/logout`, truy cập qua menu avatar người dùng ở góc phải thanh điều hướng (danh sách có liên kết "Logout").
**Kết quả mong đợi:** Hệ thống hủy phiên đăng nhập, chuyển hướng về lại trang Login.
**Lưu ý hành vi đã quan sát:** Nếu người dùng có timer công việc (task timer) đang chạy, hệ thống hiển thị hộp thoại xác nhận: *"Started tasks timers found! Are you sure you want to logout without stopping the timers?"* trước khi thực hiện đăng xuất.

### FR-07: Điều hướng khi chưa đăng nhập
**Mô tả:** Người dùng chưa đăng nhập truy cập các URL thuộc khu vực `/admin/*` (ngoại trừ các trang authentication) sẽ bị chuyển hướng về trang Login.

---

## 4. Yêu cầu phi chức năng (Non-functional Requirements)

| Hạng mục | Yêu cầu |
|---|---|
| Bảo mật | Mật khẩu phải được che (input type password), không hiển thị dạng plain text mặc định |
| Bảo mật | Thông báo lỗi đăng nhập sai không được tiết lộ email có tồn tại trong hệ thống hay không (generic message) |
| Khả dụng | Form phải validate dữ liệu đầu vào cả phía client (type=email) và phía server (thông báo required) |
| Khả dụng | Giao diện responsive, hiển thị tốt trên các kích thước màn hình khác nhau |
| Hiệu năng | Thời gian phản hồi khi submit đăng nhập cần dưới 3 giây trong điều kiện mạng bình thường |
| Khả năng truy cập | Các trường input có label rõ ràng ("Email Address", "Password") hỗ trợ screen reader |

---

## 5. Quy tắc nghiệp vụ (Business Rules)

- BR-01: Email và Password đều là trường bắt buộc.
- BR-02: Trường Email phải đúng định dạng email (ràng buộc HTML5 `type="email"`).
- BR-03: Hệ thống không phân biệt rõ lỗi "sai email" hay "sai mật khẩu" — luôn trả về thông báo gộp "Invalid email or password".
- BR-04: Người dùng đã đăng nhập không thể truy cập lại các trang Login/Forgot Password — tự động điều hướng về Dashboard.
- BR-05: Khi đăng xuất mà có task timer đang chạy, hệ thống yêu cầu xác nhận trước khi hủy phiên.

---

## 6. Luồng xử lý (Flow)

### 6.1 Luồng đăng nhập thành công
1. Người dùng truy cập `/admin/authentication`.
2. Nhập Email hợp lệ đã đăng ký.
3. Nhập đúng Password tương ứng.
4. (Tuỳ chọn) Chọn "Remember me".
5. Bấm **Login**.
6. Hệ thống xác thực → chuyển hướng tới Dashboard (`/admin/`).

### 6.2 Luồng đăng nhập thất bại (sai thông tin)
1–4. Giống trên nhưng Email hoặc Password không khớp dữ liệu hệ thống.
5. Bấm **Login**.
6. Hệ thống hiển thị lỗi `Invalid email or password`, ở lại trang Login, các trường input bị xoá trắng.

### 6.3 Luồng bỏ trống trường bắt buộc
1. Bấm **Login** khi chưa nhập Email/Password.
2. Hệ thống hiển thị thông báo lỗi tương ứng cho từng trường, không submit.

### 6.4 Luồng quên mật khẩu
1. Từ trang Login, bấm "Forgot Password?".
2. Nhập Email đã đăng ký.
3. Bấm **Confirm**.
4. Hệ thống gửi email hướng dẫn đặt lại mật khẩu (cần kiểm thử thêm nội dung email/luồng reset thực tế).

### 6.5 Luồng đăng xuất
1. Từ bất kỳ trang nào trong khu vực quản trị, bấm avatar người dùng.
2. Chọn "Logout".
3. Nếu có task timer đang chạy → hộp thoại xác nhận hiển thị → người dùng xác nhận.
4. Hệ thống huỷ phiên, chuyển hướng về trang Login.

---

## 7. Thông báo lỗi đã ghi nhận (Error Messages)

| Tình huống | Thông báo |
|---|---|
| Bỏ trống Email | `The Email Address field is required.` |
| Bỏ trống Password | `The Password field is required.` |
| Sai Email hoặc Password | `Invalid email or password` |

---

## 8. Tiêu chí chấp nhận (Acceptance Criteria)

- [ ] Đăng nhập thành công với tài khoản hợp lệ chuyển hướng đúng tới Dashboard.
- [ ] Đăng nhập sai thông tin hiển thị thông báo lỗi chung, không đăng nhập được.
- [ ] Bỏ trống Email/Password hiển thị thông báo lỗi tương ứng, không gửi request đăng nhập không hợp lệ.
- [ ] Trường Password luôn được ẩn ký tự (masked).
- [ ] Chức năng "Remember me" lưu phiên đăng nhập đúng theo kỳ vọng.
- [ ] Chức năng Forgot Password gửi được yêu cầu đặt lại mật khẩu với Email hợp lệ.
- [ ] Logout huỷ phiên đăng nhập, không thể quay lại trang nội bộ bằng nút Back của trình duyệt sau khi logout.
- [ ] Người dùng đã đăng nhập không truy cập lại được trang Login/Forgot Password (tự động redirect).

---

## 9. Ghi chú / Hạn chế khảo sát

- Chưa kiểm thử: giới hạn số lần đăng nhập sai (account lockout/rate limiting), captcha, đa yếu tố xác thực (2FA/MFA), nội dung email reset password, chính sách độ mạnh mật khẩu khi đổi mật khẩu.
- Các mục trên cần được xác nhận thêm với đội phát triển hoặc kiểm thử bổ sung nếu hệ thống có hỗ trợ.
