# Lịch rảnh cộng tác viên (Web tĩnh)

Web đơn giản 1 trang (`index.html`), không cần build, không cần server riêng.
- Cộng tác viên tick khung giờ rảnh theo 7 ngày (Thứ 2 -> Chủ nhật) x 24 giờ/ngày.
- Trang "Xem tổng hợp" tự tính các khung giờ mà **tất cả** cộng tác viên đều rảnh.
- Xóa 1 cộng tác viên khỏi danh sách: nhập đúng mật khẩu `Need` vào ô mật khẩu quản trị, nút Xóa sẽ hiện ra.

## Bước 1: Tạo Firebase project (miễn phí)

Web tĩnh vẫn cần 1 nơi lưu dữ liệu dùng chung để nhiều cộng tác viên ở nhiều máy khác nhau cùng thấy nhau. Dùng Firestore (Google, miễn phí):

1. Vào https://console.firebase.google.com -> "Add project" -> đặt tên bất kỳ.
2. Trong project, vào **Build > Firestore Database** -> "Create database" -> chọn **Start in test mode**.
3. Vào **Project settings** (icon bánh răng) > tab **General** > mục "Your apps" > bấm icon `</>`  (Web) để tạo 1 web app.
4. Copy đoạn `firebaseConfig = {...}` được hiển thị.

## Bước 2: Dán config vào code

Mở `index.html`, tìm đoạn:

```js
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  ...
};
```

Thay toàn bộ bằng config vừa copy ở Bước 1.

## Bước 3 (khuyến nghị): Giới hạn quyền ghi Firestore

Mặc định "test mode" cho phép AI cả thế giới đọc/ghi trong 30 ngày rồi tự khóa lại. Vì web không có đăng nhập, cứ để test mode cũng được cho nhóm nội bộ nhỏ dùng tạm. Nếu muốn an toàn hơn (chặn spam), vào Firestore > Rules, đổi thành:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /thoigianhop_responses/{docId} {
      allow read: if true;
      allow write: if request.resource.data.name is string
                   && request.resource.data.name.size() < 100;
    }
  }
}
```

## Bước 4: Đưa lên GitHub + GitHub Pages

```bash
git init
git add index.html README.md
git commit -m "Web lich ranh cong tac vien"
git branch -M main
git remote add origin <URL_REPO_GITHUB_CUA_BAN>
git push -u origin main
```

Sau đó vào repo trên GitHub -> **Settings > Pages** -> Source chọn nhánh `main`, thư mục `/ (root)` -> Save. Sau vài phút, GitHub cho 1 link dạng `https://<username>.github.io/<repo>/` để gửi cho cộng tác viên điền.

## Ghi chú

- Mỗi cộng tác viên điền lại **đúng tên cũ** thì dữ liệu sẽ được cập nhật (ghi đè), không tạo bản trùng.
- "Hợp lệ" hiện tại nghĩa là khung giờ mà **100% cộng tác viên đã điền** đều rảnh (giao nhau). Nếu 1 người chưa điền hoặc bận thì khung giờ đó không được tính.
