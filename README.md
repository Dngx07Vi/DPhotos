# DPhotos
## S0-05 – Wireframe Figma cho 3 màn hình chính

### Phạm vi

Thiết kế wireframe độ trung thực thấp cho 3 màn hình chính của DPhotos trên hai kích thước:

- Mobile: 390px
- Desktop: 1440px

Wireframe sử dụng khung xám cho hình ảnh, màu trung tính và chưa áp dụng đầy đủ design tokens/UI hoàn thiện. Mục tiêu của S0-05 là xác định bố cục, cấu trúc nội dung, User Flow và responsive trước khi triển khai UI chính thức.

### Các màn hình

| Màn hình | Mobile | Desktop |
|---|---|---|
| Trang chủ | 1A | 1B |
| Danh sách hướng dẫn | 2A | 2C |
| Chi tiết hướng dẫn | 3A | 3B |

#### 1. Trang chủ

Bao gồm:

- Header / Navigation
- Hero và ô tìm kiếm
- Danh mục nổi bật
- Hướng dẫn nổi bật
- Khu vực thiết bị

#### 2. Trang danh sách hướng dẫn

Bao gồm:

- Tìm kiếm
- Bộ lọc
- Số lượng kết quả
- Danh sách Card hướng dẫn
- Phân trang
- Phiên bản Mobile có layout bộ lọc riêng

#### 3. Trang chi tiết hướng dẫn

Đảm bảo đầy đủ 9 nhóm nội dung theo Functional Requirements:

1. Cách tạo dáng
2. Vị trí chụp
3. Góc chụp & cỡ cảnh
4. Camera đề xuất
5. Thông số chụp
6. Các bước thực hiện
7. Thiết bị phù hợp
8. Bối cảnh & phong cách
9. Ghi chú

### User Flow

Luồng chính:

```text
Trang chủ
   ↓
Tìm kiếm / Chọn danh mục
   ↓
Danh sách hướng dẫn
   ↓
Chọn Card
   ↓
Chi tiết hướng dẫn
```

Các điểm vào danh sách gồm:

- Ô tìm kiếm trên Hero
- Danh mục nổi bật
- Nút “Xem tất cả hướng dẫn”

Trang danh sách hỗ trợ tìm kiếm, bộ lọc và thao tác xóa bộ lọc.

### Responsive

#### Mobile – 390px

- Menu dạng hamburger
- Card hiển thị 1 cột
- Bộ lọc mở thành khu vực riêng
- Thông số camera hiển thị dạng 2 cột
- Các nút và vùng chạm được bố trí phù hợp với thao tác trên mobile

#### Desktop – 1440px

- Container nội dung tối đa khoảng 1200px
- Sidebar bộ lọc khoảng 280px
- Danh sách Card hiển thị dạng grid 3–4 cột
- Thông số chụp hiển thị dạng bảng

### Prototype

Các interaction chính được thiết lập để mô phỏng User Flow giữa các màn hình:

```text
Home Mobile → List Mobile → Detail Mobile
Home Desktop → List Desktop → Detail Desktop
```

Prototype được kiểm tra bằng chế độ **Present** trong Figma.

### Figma

[ DPhotos – Wireframe S0-05 ](https://www.figma.com/design/jwEqlDV1EKz765CxEEe0Ph/DPhotos-%E2%80%93-Wireframe--S0-05-)

