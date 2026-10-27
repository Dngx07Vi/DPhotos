# DPhotos
## S0-06 – Chọn CSS Framework và chốt Design Tokens

### 1. CSS Framework

DPhotos sử dụng **Bootstrap 5.3** làm CSS framework chính.

> **Không sử dụng đồng thời Bootstrap và Tailwind CSS.**

#### Lý do lựa chọn

- Template ASP.NET Core MVC có sẵn Bootstrap, Razor và Tag Helper có thể sử dụng trực tiếp.
- Bootstrap cung cấp các component phù hợp với wireframe DPhotos:
  - Navbar
  - Card
  - Offcanvas
  - Accordion / Collapse
  - Pagination
  - Badge
  - Form
  - Table / `table-responsive`
- Có sẵn hệ thống grid responsive, giúp giảm lượng CSS phải tự xây dựng trong giai đoạn MVP.
- Phù hợp với giao diện DPhotos có cấu trúc đơn giản, tập trung vào hình ảnh.

#### Đánh đổi

Bootstrap mặc định có giao diện khá phổ biến. DPhotos sẽ giữ Bootstrap làm nền tảng nhưng ghi đè Design Tokens riêng (`--dp-*`) để tạo giao diện tối giản, thống nhất và không sử dụng màu thừa.

#### Lưu ý Validation

Validation của ASP.NET Core MVC / jQuery Unobtrusive sử dụng class `input-validation-error`, không tự động chuyển thành `is-invalid` của Bootstrap.

Khi triển khai Form Admin cần map trạng thái validation sang Bootstrap trong Sprint 5.

---

### 2. Design Tokens

#### 2.1. Color

| Token           | Giá trị               | Ghi chú                                       |
| --------------- | --------------------- | --------------------------------------------- |
| `bg`            | `#FFFFFF`             | Nền trang                                     |
| `bg-subtle`     | `#F8F9FA`             | Nền Hero, footer, khối phụ                    |
| `bg-image`      | `#E9ECEF`             | Nền ảnh khi chưa tải hoặc thiếu ảnh           |
| `text`          | `#212529`             | Chữ chính                                     |
| `text-muted`    | `#5C636A`             | Chữ phụ, đảm bảo độ tương phản AA             |
| `border`        | `#DEE2E6`             | Viền Card, đường phân cách                    |
| `border-strong` | `#868E96`             | Viền input, checkbox                          |
| `primary`       | `#2563EB`             | Nút chính, chip, checkbox đang chọn, liên kết |
| `primary-hover` | `#1D4ED8`             | Hover / active                                |
| `on-primary`    | `#FFFFFF`             | Chữ trên nền primary                          |
| `focus-ring`    | `rgba(37,99,235,.35)` | Focus bằng bàn phím (`:focus-visible`)        |
| `danger`        | Bootstrap default     | Validation, xác nhận xóa                      |
| `success`       | Bootstrap default     | Trạng thái thành công                         |
| `warning`       | Bootstrap default     | Cảnh báo                                      |

---

#### 2.2. Typography

Font chính:

```text
Inter, system-ui, sans-serif
```

Nếu sử dụng Google Fonts phải bao gồm subset `vietnamese`. Có thể self-host font để tránh phụ thuộc dịch vụ bên ngoài.

Font weight sử dụng:

- `400` – Regular
- `600` – Semi Bold
- `700` – Bold

Typography scale:

| Thành phần | Mobile | Desktop |
| ---------- | -----: | ------: |
| Display    |   26px |    44px |
| H1         |   24px |    32px |
| H2         |   18px |    26px |
| H3         |   17px |    20px |
| Body       |   16px |    16px |
| Small      |   13px |    13px |

Ô nhập liệu sử dụng cỡ chữ tối thiểu **16px** để tránh vấn đề usability trên mobile.

---

#### 2.3. Border Radius

| Token  | Giá trị | Sử dụng               |
| ------ | ------: | --------------------- |
| `sm`   |     4px | Thành phần nhỏ        |
| `md`   |     8px | Button, input, search |
| `lg`   |    12px | Card, ảnh lớn         |
| `pill` |   999px | Chip / tag            |

---

#### 2.4. Spacing

Spacing sử dụng đơn vị cơ sở **4px**.

| Token     |   px | Bootstrap             |
| --------- | ---: | --------------------- |
| `space-1` |  4px | `*-1`                 |
| `space-2` |  8px | `*-2`                 |
| `space-3` | 12px | Custom `--dp-space-3` |
| `space-4` | 16px | `*-3`                 |
| `space-5` | 24px | `*-4`                 |
| `space-6` | 32px | Custom `--dp-space-6` |
| `space-7` | 48px | `*-5`                 |
| `space-8` | 64px | Custom `--dp-space-8` |

---

### 3. Button Style

#### Primary

- Background: `primary`
- Text: `on-primary`
- Hover / active: `primary-hover`
- Border-radius: `md`
- Font-weight: `600`

#### Secondary / Outline

- Border: `text`
- Background: `bg`
- Hover background: `bg-subtle`
- Sử dụng cho các hành động như:
  - `Xem chi tiết`
  - `Xóa bộ lọc`

#### Interaction

- Chiều cao tối thiểu: **40px trên desktop**
- Chiều cao tối thiểu: **44px trên màn hình cảm ứng**
- Quy tắc 44px áp dụng cho:
  - Button
  - Pagination
  - Filter chip
  - Checkbox
  - Icon menu
- Phải có trạng thái `focus-visible`.
- Phải có trạng thái `disabled`.
- Hover không phải là trạng thái tương tác duy nhất.

---

### 4. Responsive

DPhotos sử dụng phương pháp **mobile-first** và breakpoint theo Bootstrap.

| Kích thước kiểm tra | Bootstrap     | Layout                                                           |
| ------------------- | ------------- | ---------------------------------------------------------------- |
| 360 / 390px         | XS `<576px`   | Card 1 cột, menu hamburger, Filter Offcanvas, thông số dạng card |
| 768px               | MD            | Card 2 cột, Filter vẫn dạng Offcanvas                            |
| 1024px              | LG `≥992px`   | Sidebar Filter, Card 2 cột                                       |
| 1440px              | XXL `≥1400px` | Container 1200px, Sidebar 280px, Card 3 cột; Trang chủ 4 cột     |

#### Mục tiêu responsive

- Không vỡ bố cục.
- Không xuất hiện horizontal overflow.
- Bảng dữ liệu rộng phải sử dụng `table-responsive`.
- Văn bản dài phải có khả năng xuống dòng.
- Các thành phần tương tác phải thuận tiện trên màn hình cảm ứng.
- Giao diện phải sử dụng được từ khoảng **360px**.

---

### 5. Hình ảnh

Card sử dụng tỷ lệ ảnh cố định **4:3**.

Quy tắc:

- Sử dụng Bootstrap `ratio` khi phù hợp.
- Ảnh sử dụng `img-fluid`.
- Ảnh không tải hoặc chưa có ảnh sử dụng `bg-image`.
- Sử dụng `loading="lazy"` cho ảnh không nằm trong vùng hiển thị ban đầu.
- Giữ tỷ lệ ảnh để tránh layout shift.

---

### 6. Cách tích hợp

Bootstrap được sử dụng làm framework UI duy nhất.

Thứ tự CSS:

```text
bootstrap.css
      ↓
tokens.css
      ↓
site.css
```

Tạo file:

```text
wwwroot/css/tokens.css
```

Các Design Tokens của DPhotos sử dụng prefix `--dp-*`.

Các token cần thiết được map sang Bootstrap variables khi phù hợp:

```css
:root {
    --dp-primary: #2563EB;
    --dp-primary-hover: #1D4ED8;
    --dp-muted: #5C636A;

    --dp-radius-md: 8px;

    --dp-space-3: 12px;
    --dp-space-6: 32px;
    --dp-space-8: 64px;

    --bs-primary: var(--dp-primary);
    --bs-primary-rgb: 37, 99, 235;
    --bs-link-color: var(--dp-primary);
    --bs-secondary-color: var(--dp-muted);
    --bs-border-radius: var(--dp-radius-md);
    --bs-body-font-family: Inter, system-ui, sans-serif;
}

.btn-primary {
    --bs-btn-bg: var(--dp-primary);
    --bs-btn-border-color: var(--dp-primary);
    --bs-btn-hover-bg: var(--dp-primary-hover);
    --bs-btn-hover-border-color: var(--dp-primary-hover);
    --bs-btn-active-bg: var(--dp-primary-hover);
}

@media (min-width: 1200px) {
    .container {
        max-width: 1200px;
    }
}
```

Bootstrap được ghim ở phiên bản **5.3.x** và không sử dụng Tailwind CSS hoặc thêm một CSS framework khác.

---

### 7. Liên hệ với Figma S0-05

S0-05 là **low-fidelity wireframe**, vì vậy các màn hình wireframe vẫn có thể giữ màu xám và placeholder.

S0-06 bổ sung hệ thống Design Tokens để sử dụng cho:

- UI Design tiếp theo.
- Figma Variables / Styles.
- CSS implementation.
- Bootstrap override.
- Các component dùng chung trong Sprint 1 trở đi.

Trong Figma cần tạo Variables / Styles tương ứng với Design Tokens và rà soát các điểm lệch với quy chuẩn:

- Vùng chạm tối thiểu 44px trên màn hình cảm ứng.
- Search input sử dụng radius 8px.
- Button sử dụng radius 8px.
- Card / ảnh lớn sử dụng radius 12px.
- Typography và màu sắc UI chính thức tuân theo Design Tokens khi chuyển từ wireframe sang UI.

---
