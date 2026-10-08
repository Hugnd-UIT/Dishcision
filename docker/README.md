# 🐳 Hướng dẫn vận hành Docker - Dishcision

Tài liệu tổng hợp các câu lệnh cần thiết để build, khởi chạy và quản lý hệ thống Docker (PostgreSQL, Redis, Backend, Frontend).

---

## 🚀 1. Chuẩn bị ban đầu

Di chuyển vào thư mục `docker`:
```bash
cd docker
```

Tạo file cấu hình môi trường `.env` từ file mẫu:
```bash
cp .env.example .env
```
*(Nếu cần, hãy mở file `.env` lên để thay đổi mật khẩu database và redis).*

---

## ⚡ 2. Khởi động và Build hệ thống

### Khởi động toàn bộ hệ thống (chạy ngầm)
```bash
docker compose up -d
```

### Build lại mã nguồn và khởi động lại
Dùng khi bạn vừa sửa code Backend hoặc Frontend:
```bash
docker compose up -d --build
```

### Chỉ khởi động một dịch vụ cụ thể
```bash
# Chỉ chạy Database và Redis
docker compose up -d postgres redis

# Chỉ chạy Backend
docker compose up -d --build backend

# Chỉ chạy Frontend (Web Admin)
docker compose up -d --build frontend
```

---

## 📊 3. Kiểm tra và Giám sát (Monitoring)

### Kiểm tra danh sách container và trạng thái Healthcheck
```bash
docker compose ps
```

### Xem Log thời gian thực (Real-time logs)
```bash
# Xem log toàn bộ hệ thống
docker compose logs -f

# Xem log riêng Backend
docker compose logs -f backend

# Xem log riêng Database
docker compose logs -f postgres

# Xem 100 dòng log gần nhất của Backend
docker compose logs --tail=100 -f backend
```

### Theo dõi lượng RAM & CPU tiêu hao (Cực kỳ quan trọng trên VPS)
```bash
docker stats
```

---

## 🛑 4. Dừng và Quản lý vòng đời

### Tạm dừng các container (không xóa dữ liệu)
```bash
docker compose stop
```

### Tiếp tục chạy lại các container đã dừng
```bash
docker compose start
```

### Khởi động lại toàn bộ hệ thống
```bash
docker compose restart
```

### Tắt và xóa các container + mạng
```bash
docker compose down
```

### ⚠️ XÓA SẠCH DỮ LIỆU DATABASE & REDIS (Cẩn thận khi dùng)
Lệnh này sẽ xóa cả các ổ đĩa dữ liệu (volumes):
```bash
docker compose down -v
```

---

## 🛠️ 5. Truy cập trực tiếp vào Container (Debug / Thao tác thủ công)

### Truy cập vào dòng lệnh bên trong Backend
```bash
docker exec -it dishcision-backend sh
```

### Truy cập vào PostgreSQL bằng `psql`
```bash
docker exec -it dishcision-postgres psql -U user -d db
```

### Truy cập vào Redis bằng `redis-cli`
```bash
docker exec -it dishcision-redis redis-cli -a secret
```

### Chạy Prisma Migration thủ công
```bash
docker exec -it dishcision-backend npx prisma migrate deploy
```

---

## 🌐 6. Bảng cổng kết nối (Port Mapping)

| Dịch vụ | Container Name | Cổng ngoài (Host) | Cổng trong Container | Mục đích |
| :--- | :--- | :--- | :--- | :--- |
| **Frontend** | `dishcision-frontend` | `80` | `80` | Giao diện Web Admin |
| **Backend** | `dishcision-backend` | `3000` | `3000` | REST API / Websocket |
| **PostgreSQL**| `dishcision-postgres`| Nội bộ mạng Docker | `5432` | Cơ sở dữ liệu chính |
| **Redis** | `dishcision-redis` | Nội bộ mạng Docker | `6379` | Cache & Pub/Sub |
