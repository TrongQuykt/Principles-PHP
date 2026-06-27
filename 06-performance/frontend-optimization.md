# Frontend Optimization

**Priority: 6** | Nhóm: [Performance](./README.md)

---

## Nguyên tắc chính

Hiệu năng Frontend ảnh hưởng trực tiếp đến trải nghiệm người dùng (UX) và điểm SEO (Core Web Vitals).

---

## Core Web Vitals (Google)

| Chỉ số | Tên đầy đủ | Mục tiêu | Giải thích |
|---|---|---|---|
| **LCP** | Largest Contentful Paint | < 2.5s | Thời gian phần tử lớn nhất xuất hiện |
| **FID** | First Input Delay | < 100ms | Thời gian phản hồi khi user tương tác |
| **CLS** | Cumulative Layout Shift | < 0.1 | Mức độ thay đổi bố cục bất thình lình |

---

## Chiến lược tối ưu Asset

### 1. Hình ảnh — Image Optimization

- **Định dạng**: Ưu tiên WebP hoặc Avif thay vì JPEG/PNG.
- **Kích thước**: Luôn serve ảnh đúng kích thước hiển thị (không load ảnh 4K cho thumbnail).
- **Lazy Loading**: Dùng `loading="lazy"` cho ảnh dưới màn hình (below the fold).
- **Explicit Dimensions**: Luôn set `width` và `height` để tránh CLS.

### 2. JavaScript — Code Splitting

- **Dynamic Import**: Chỉ load các component/module khi thực sự cần.
- **Tree Shaking**: Loại bỏ code thừa không được sử dụng.
- **Minification**: Nén code (UglifyJS, Terser).
- **Third-party scripts**: Hạn chế dùng quá nhiều tracking pixel hoặc widget nặng.

### 3. CSS — Critical Path

- **Critical CSS**: Inline CSS cần thiết cho phần trên cùng trang web (Above the fold).
- **Minimize CSS**: Loại bỏ các class không sử dụng (PurgeCSS).

---

## Rendering Strategies (Next.js context)

| Chiến lược | Khi nào dùng | Ảnh hưởng Performance |
|---|---|---|
| **SSR** (Server Side Rendering) | Dữ liệu thay đổi liên tục, cần SEO | TTFB cao hơn, nhưng SEO tốt |
| **SSG** (Static Site Gen) | Trang ít thay đổi (Blog, landing) | Load cực nhanh, LCP thấp |
| **ISR** (Incremental Static) | System lớn cần static nhưng data cần cập nhật | Kết hợp tốt nhất giữa SSG và SSR |
| **CSR** (Client Side Rendering) | Dashboard, ứng dụng tương tác cao | LCP chậm, cần Loading Skeletons |

---

## Browser Caching & Networking

- **HTTP/2 & HTTP/3**: Cho phép download đồng thời nhiều file qua 1 connection.
- **Service Workers**: Cache resource để hỗ trợ offline hoặc load nhanh hơn.
- **Prefetch/Preload**: Gợi ý trình duyệt tải trước các resource quan trọng.

---

## Checklist — Frontend Performance

- [ ] LCP của trang chủ nhỏ hơn 2.5 giây
- [ ] Mọi hình ảnh đều có thuộc tính width/height
- [ ] Sử dụng định dạng WebP cho hầu hết hình ảnh
- [ ] Không có file JS nào có kích thước quá lớn (> 500kb uncompressed)
- [ ] Đã xử lý trạng thái Loading (Skeleton/Spinner) cho các tác vụ async

---

## Liên kết

- [Core Web Vitals (Web.dev)](https://web.dev/vitals/)
- [Next.js Optimization Guide](https://nextjs.org/docs/app/building-your-application/optimizing)
