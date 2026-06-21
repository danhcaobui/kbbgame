# Vua · Dân · Nô Lệ — server

Game đối kháng 1v1 (kéo-búa-bao biến thể) với tài khoản thật, bảng xếp hạng chung và đấu online thời gian thực.
Một service duy nhất: Node + Express phục vụ luôn giao diện game, kèm Socket.IO + Postgres.

## Cấu trúc thư mục (giữ đúng như vầy)

```
kbbgame-server/
├── package.json
├── server.js
├── .gitignore
└── public/
    ├── index.html
    └── app.js
```

`index.html` và `app.js` nằm trong `public/`. `server.js` và `package.json` nằm ở gốc.

## Chạy thử ở máy (tuỳ chọn)

Cần Node 18+ và một Postgres. Đặt biến môi trường rồi chạy:
```bash
npm install
DATABASE_URL="postgres://user:pass@localhost:5432/kbb" npm start
```
Mở http://localhost:3000

## Deploy lên Railway (24/7)

1. **Đẩy code lên GitHub** (có `package.json` là đúng cho server):
   ```bash
   git add -A
   git commit -m "Server: Express + Socket.IO + Postgres"
   git push
   ```
2. **Tạo service từ repo** trên Railway → Railway nhận Node, tự chạy `npm start`.
3. **Thêm Postgres**: trong cùng project, bấm **New → Database → Add PostgreSQL**.
4. **Nối DB vào app**: vào service app → tab **Variables** → thêm biến
   - Tên: `DATABASE_URL`
   - Giá trị: tham chiếu tới Postgres, gõ `${{Postgres.DATABASE_URL}}`
   (Railway sẽ tự điền chuỗi kết nối nội bộ.)
5. *(Nên làm)* thêm biến `TOKEN_SECRET` = một chuỗi ngẫu nhiên dài (để ký token đăng nhập).
6. Railway tự deploy lại. Mở URL công khai của service là chơi được.

Lần deploy đầu, server tự tạo 2 bảng `users` và `matches` trong Postgres.

## Ghi chú thiết kế

- **Đăng nhập thật**: mật khẩu hash bằng bcrypt và lưu trong Postgres (phía server). Tài khoản dùng được trên mọi máy. Nhấn F5 vẫn giữ đăng nhập.
- **Bảng xếp hạng chung**: lấy từ Postgres, mọi người dùng chung, xếp theo số trận thắng.
- **Online do server làm trọng tài**: server quyết định thắng/thua mỗi ván rồi mới ghi điểm → khó gian lận. Có tìm trận ngẫu nhiên (ghép tự động) và phòng theo mã.
- **Đấu với máy = luyện tập**, KHÔNG tính vào bảng xếp hạng (tránh cày điểm ảo).
