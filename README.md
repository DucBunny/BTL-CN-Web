# Hệ thống quản lý chung cư

## Tổng quan

- Dự án gồm 2 phần chính: `backend` (Node.js + Express + Sequelize) và `frontend` (Vite + React + Tailwind).
- Docker và `docker-compose.yaml` có sẵn ở root để khởi động toàn bộ stack.

## Yêu cầu môi trường

- Node.js >= 16 (các script và devDependencies dùng Babel, nodemon, Vite)
- npm hoặc yarn
- Docker (tùy chọn, để chạy bằng container)
- MySQL (có thể dùng Docker DB) — dự án sử dụng `mysql2` và Sequelize

## Cấu trúc thư mục

```
├── backend/
│   ├── Dockerfile
│   ├── package.json
│   └── src/
│       ├── server.js             # Entry server
│       ├── config/               # Cấu hình (database, email, cloudinary, vnpay...)
│       ├── controllers/          # Controller
│       ├── models/               # Sequelize models
│       ├── repositories/         # Data access layer
│       ├── services/             # Business logic
│       ├── routes/               # Express routes
│       ├── middlewares/          # Middlewares
│       ├── migrations/           # Sequelize migrations
│       └── seeders/              # Seed dữ liệu
├── frontend/
│   ├── Dockerfile
│   ├── package.json
│   └── src/                      # Mã nguồn React + Vite
├── docker-compose.yaml
└── README.md

```

## Cách cài đặt & khởi chạy — Backend

1. Sao chép `.env` (nếu có) hoặc tạo file mới

   - Tạo file `backend/.env` và thêm biến môi trường cần thiết (ví dụ):
     - `DB_HOST=localhost`
     - `DB_USER=root`
     - `DB_PASS=yourpassword`
     - `DB_NAME=your_db_name`
     - `DB_DIALECT=mysql`
     - `PORT=5000`
     - `JWT_SECRET=your_jwt_secret`
     - `CLOUDINARY_*`, `SMTP_*`, `VNPAY_*` (kiểm tra `src/config` để biết đầy đủ)

2. Cài dependencies

```bash
cd backend
npm install
```

3. Chạy ở môi trường phát triển

```bash
npm run dev
```

- Script `dev` trong `backend/package.json` dùng `nodemon` + `babel-node` để chạy mã ES6/ESNext.

4. Migrate & Seed (Sequelize)

- Chạy migrations và seed thủ công:

```bash
npx sequelize-cli db:migrate
npx sequelize-cli db:seed:all
```

- Hoặc dùng script tiện lợi (xóa DB, tạo lại, migrate, seed):

```bash
npm run db:reset
```

5. Build & chạy production

```bash
npm run build
npm run production
```

## Cách cài đặt & khởi chạy — Frontend

1. Cài dependencies

```bash
cd frontend
npm install
```

2. Chạy ở môi trường phát triển

```bash
npm run dev
```

- Mặc định FE dùng Vite; mở trình duyệt tới địa chỉ console báo.

3. Build production

```bash
npm run build
npm run preview   # kiểm tra bản build local
```

4. Biến môi trường frontend

- Nếu FE cần endpoint của BE, sử dụng biến môi trường có tiền tố `VITE_`, ví dụ `VITE_API_URL=https://api.example.com`.
- Tạo file `.env` trong `frontend/` nếu cần.

## Chạy bằng Docker / docker-compose

- Cấu hình trong `docker-compose.yaml` có sẵn tại root; để chạy toàn bộ stack:

```bash
# từ thư mục root của dự án
docker compose up --build
```

- Hoặc dùng `docker-compose up --build` nếu hệ thống dùng docker-compose v1.

## Database & Migrations

- Migration files nằm trong `backend/src/migrations` (hoặc `backend/migrations` tùy cấu trúc), models trong `backend/src/models`.
- Các lệnh thường dùng:

```bash
npx sequelize-cli db:migrate            # chạy migration
npx sequelize-cli db:migrate:undo:all   # revert tất cả
npx sequelize-cli db:seed:all           # chạy seed
npm run db:reset                        # script tích hợp (drop/create/migrate/seed)
```

## Lưu ý triển khai

- Kiểm tra `backend/.env` trước khi chạy production — biến môi trường quan trọng: kết nối DB, secrets, cấu hình email và cloudinary.
- Nếu dùng Docker, cân nhắc lưu credentials vào Docker secrets hoặc biến môi trường của CI/CD.

## Gợi ý debug & troubleshooting

- Backend logs: xem console nơi `npm run dev` đang chạy.
- Kiểm tra lỗi kết nối DB: xác nhận `DB_HOST`, `DB_USER`, `DB_PASS`, `DB_NAME`.
- Nếu migration không chạy, kiểm tra phiên bản `sequelize-cli` và cấu hình `config/config.js`.

## Thông tin thêm

- Mã nguồn BE: [backend](backend)
- Mã nguồn FE: [frontend](frontend)
