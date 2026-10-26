# DPhotos – Thiết kế hệ thống v1.2 (S0-04: chốt ERD v1, Sitemap, User Flow)

Hoàng Mạnh Dũng · Sprint 0 · Tài liệu thiết kế đã chốt, thay thế các mục 11.3–11.5, 12.3, 12.4 của tài liệu v1.1

## 1. Tóm tắt

**Mục tiêu S0-04:** thống nhất thiết kế hệ thống trước khi phát triển (Sprint 1).

| Hạng mục | Trạng thái |
| --- | --- |
| ERD v1 (quan hệ 1-N, N-N, phù hợp phạm vi MVP) | Đã chốt |
| Sitemap | Đã chốt |
| User Flow người dùng | Đã chốt |
| User Flow Admin | Đã chốt |
| Ghi thay đổi vào tài liệu DPhotos | Có trong mục 7 |
| Đánh dấu phiên bản thiết kế | Tag `design-v1.0`, tài liệu v1.2 (mục 8) |

## 2. Các quyết định đã chốt

| # | Vấn đề | Quyết định |
| --- | --- | --- |
| D1 | Route trang chi tiết hướng dẫn | MVP dùng `/PhotoGuides/Details/{id}` (khớp Sprint 2 và TC05). Cột `Slug` vẫn giữ unique trong DB; chuyển sang URL theo slug ở Giai đoạn 2 (NFR-06). |
| D2 | Lưu «camera 1x» chung chung (không gắn model thiết bị) | Thêm `DeviceType` và `ZoomFactor` vào `CameraSettings`. Khi chọn thiết bị/lens cụ thể thì dùng `DeviceId`, `DeviceLensId`. |
| D3 | Tìm kiếm không dấu (TC09, chữ «đ») | Không ảnh hưởng ERD v1. Quyết định ở Sprint 4: thêm cột `SearchText` chuẩn hóa bằng migration. |
| D4 | Kiểu dữ liệu `Aperture` | Đổi thành `decimal(4,2)` (lưu được f/0.95, f/1.45). Áp dụng cho `CameraSettings` và `DeviceLenses`. |
| D5 | Trang quản lý ảnh riêng `/Admin/Images` | Bỏ. Upload ảnh nằm trong form Create/Edit của từng đối tượng (hướng dẫn, tư thế, bối cảnh, góc chụp). Vẫn đáp ứng FR-29 và US-17. |
| D6 | Nơi nhập `CameraSettings` | Trong form sửa hướng dẫn (`/Admin/PhotoGuides`), không nằm ở `/Admin/Devices`. |
| D7 | `PhotoGuidePose` | Entity tường minh, có cột `SortOrder`. |

## 3. ERD v1

Quy ước: entity đặt tên số ít, bảng đặt tên số nhiều. Các bảng Identity (`AspNetUsers`, `AspNetRoles`, `AspNetUserRoles`, …) do ASP.NET Core Identity tạo; chỉ có Role `Admin` ở MVP; mật khẩu Admin lấy từ User Secrets hoặc biến môi trường, không ghi cứng trong code.

### 3.1. Các bảng và cột chính

| Bảng | Cột | Ghi chú |
| --- | --- | --- |
| **PhotoGuides** | Id PK; Title nvarchar(200); Slug nvarchar(250) unique; Summary nvarchar(500); Description nvarchar(max); CoverImageUrl nvarchar(500); PhotographerPosition nvarchar(500); SubjectPosition nvarchar(500); ShootingDistance nvarchar(100); CameraHeight nvarchar(100); CameraDirection nvarchar(200); LightDirection nvarchar(200); IsPublished bit; IsFeatured bit; CreatedAt datetime2; UpdatedAt datetime2 | Bảng trung tâm |
| **GuideSteps** | Id PK; PhotoGuideId FK; StepNumber int; Content nvarchar(1000) | Unique (PhotoGuideId, StepNumber) |
| **GuideImages** | Id PK; PhotoGuideId FK; ImageUrl nvarchar(500); Caption nvarchar(200); SortOrder int | Caption dùng làm alt text |
| **Poses** | Id PK; Name nvarchar(100); Description nvarchar(max); ImageUrl nvarchar(500) | Name unique |
| **Scenes** | Id PK; Name nvarchar(100); Type nvarchar(20) (Location/Condition); Description nvarchar(max); ImageUrl nvarchar(500); Icon nvarchar(10) null; IsFeatured bit; SortOrder int | Unique (Type, Name). Icon, IsFeatured, SortOrder phục vụ «danh mục nổi bật» ở trang chủ |
| **Subjects** | Id PK; Name nvarchar(100); Description nvarchar(max) | Name unique |
| **Styles** | Id PK; Name nvarchar(100); Description nvarchar(max) | Name unique |
| **ShootingAngles** | Id PK; Name nvarchar(100); Category nvarchar(20) (Angle/ShotSize/Viewpoint); Description nvarchar(max); ImageUrl nvarchar(500) | Unique (Category, Name) |
| **Devices** | Id PK; Name nvarchar(150); Type nvarchar(20) (Phone/Camera); Brand nvarchar(100); Model nvarchar(100) |  |
| **DeviceLenses** | Id PK; DeviceId FK; Name nvarchar(100); ZoomFactor decimal(3,1) null; FocalLengthEq int null; Aperture decimal(4,2) null; UseCase nvarchar(500); SortOrder int | Điện thoại: 0.5x, 1x, 2x, 3x, 5x |
| **CameraSettings** | Id PK; PhotoGuideId FK; DeviceId FK null; DeviceLensId FK null; DeviceType nvarchar(20); ZoomFactor decimal(3,1) null; ISO int null; ShutterSpeed nvarchar(20) null; Aperture decimal(4,2) null; FocalLength int null; WhiteBalance nvarchar(50) null; EV decimal(3,1) null; Notes nvarchar(500) | DeviceId null nghĩa là áp dụng chung; khi đó dùng DeviceType và ZoomFactor |

### 3.2. Quan hệ 1-N và quy tắc xóa (OnDelete)

| Quan hệ | OnDelete | Lý do |
| --- | --- | --- |
| PhotoGuides → GuideSteps | Cascade | Xóa hướng dẫn thì xóa các bước |
| PhotoGuides → GuideImages | Cascade | Xóa hướng dẫn thì xử lý ảnh liên quan (xóa cả file) |
| PhotoGuides → CameraSettings | Cascade | Thông số thuộc về hướng dẫn |
| Devices → DeviceLenses | Cascade | Lens thuộc về thiết bị |
| Devices → CameraSettings | **Restrict** | Tránh lỗi multiple cascade paths trên SQL Server; không xóa thiết bị đang dùng |
| DeviceLenses → CameraSettings | **Restrict** | Như trên |

Khi Restrict chặn việc xóa, Service bắt lỗi và hiển thị thông báo rõ ràng cho Admin, không để lộ exception.

### 3.3. Quan hệ N-N (6 bảng nối, khóa chính kép)

| Bảng nối | Khóa chính | Nối với | Phía PhotoGuide | Phía danh mục |
| --- | --- | --- | --- | --- |
| PhotoGuidePose | (PhotoGuideId, PoseId) + SortOrder | Poses | Cascade | Restrict |
| PhotoGuideScene | (PhotoGuideId, SceneId) | Scenes | Cascade | Restrict |
| PhotoGuideSubject | (PhotoGuideId, SubjectId) | Subjects | Cascade | Restrict |
| PhotoGuideStyle | (PhotoGuideId, StyleId) | Styles | Cascade | Restrict |
| PhotoGuideAngle | (PhotoGuideId, ShootingAngleId) | ShootingAngles | Cascade | Restrict |
| PhotoGuideDevice | (PhotoGuideId, DeviceId) | Devices | Cascade | Restrict |

**Quy tắc nghiệp vụ (kiểm tra ở Service, không đưa vào ERD):**

- `PhotoGuideDevice` là các thiết bị mà hướng dẫn phù hợp (dùng để lọc). `CameraSettings` là bộ thông số cụ thể; thiết bị trong `CameraSettings` phải thuộc `PhotoGuideDevice`.
- Xóa danh mục đang được hướng dẫn sử dụng thì bị chặn hoặc cảnh báo rõ (TC21).
- Lọc: giữa các nhóm là AND, trong một nhóm là OR (FR-10).

### 3.4. Chỉ mục đề xuất

`PhotoGuides.Slug` (unique), `PhotoGuides.IsPublished`, `PhotoGuides.Title`, các khóa ngoại ở bảng nối, `GuideSteps (PhotoGuideId, StepNumber)` unique.

### 3.5. Kiểm tra ERD với phạm vi MVP

| Yêu cầu | Được đáp ứng bởi |
| --- | --- |
| FR-01, 02 (Card, chi tiết) | PhotoGuides, GuideImages, 6 bảng nối |
| FR-11 (tạo dáng) | Poses + PhotoGuidePose |
| FR-12 (vị trí chụp) | Các cột vị trí trong PhotoGuides |
| FR-13 (góc chụp) | ShootingAngles + PhotoGuideAngle |
| FR-14, 15 (camera, thông số) | CameraSettings, DeviceLenses |
| FR-19, 24 (thiết bị) | Devices, DeviceLenses |
| FR-02 (các bước) | GuideSteps |
| FR-05..10 (lọc) | Bảng nối + Devices.Type |
| FR-30 (trang chủ) | IsFeatured, Scenes.IsFeatured |
| FR-26, 27 (Admin) | ASP.NET Core Identity |
| FR-31 (Draft/Published) | IsPublished (cột có sẵn, chức năng ở Giai đoạn 2) |

Không có bảng nào của Giai đoạn 2/3 (Purposes, Favorites, Ratings, Comments, UserUploads) trong ERD v1.

## 4. Sitemap (đã chốt)

| Đường dẫn | Mô tả | Giai đoạn |
| --- | --- | --- |
| `/` | Trang chủ: Hero + tìm kiếm, danh mục nổi bật, hướng dẫn nổi bật, thiết bị | MVP |
| `/PhotoGuides` | Danh sách + tìm kiếm + bộ lọc + phân trang | MVP |
| `/PhotoGuides/Details/{id}` | Chi tiết hướng dẫn; ID không tồn tại trả về trang 404 | MVP |
| `/Devices` | Danh sách thiết bị (Điện thoại / Máy ảnh) | MVP |
| `/Devices/Details/{id}` | Chi tiết thiết bị, camera/lens | MVP |
| `/Account/Login` | Đăng nhập Admin (không hiển thị nút cho khách) | MVP |
| `/Account/Logout` | Đăng xuất (POST) | MVP |
| `/Account/AccessDenied` | Từ chối truy cập (403) | MVP |
| `/Admin` | Dashboard (Role = Admin) | MVP |
| `/Admin/PhotoGuides` | Index, Create, Edit, Delete; gồm GuideSteps, GuideImages, CameraSettings, upload ảnh | MVP |
| `/Admin/Poses`, `/Admin/Scenes`, `/Admin/Subjects`, `/Admin/Styles`, `/Admin/ShootingAngles` | CRUD danh mục | MVP |
| `/Admin/Devices` | CRUD thiết bị và lens | MVP |
| `/Simulator` | Mô phỏng camera | Giai đoạn 2 |
| Đăng ký / Yêu thích / Hồ sơ | Tài khoản người dùng | Giai đoạn 3 |

Toàn bộ `/Admin/*` thuộc Area `Admin`, áp dụng `[Authorize(Roles = "Admin")]` một lần cho cả khu vực.

## 5. User Flow người dùng (đã chốt)

Đường ngắn nhất (mục tiêu tối đa 3 thao tác): Trang chủ → nhập từ khóa hoặc chọn danh mục (1) → chọn một hướng dẫn (2) → xem chi tiết (3).

1. Người dùng mở **Trang chủ**.
2. Chọn cách tìm: nhập từ khóa, bấm danh mục nổi bật, xem tất cả hướng dẫn, hoặc vào mục Thiết bị.
3. Nếu tìm bằng từ khóa mà **không có kết quả**: hiển thị thông báo và gợi ý bỏ bớt bộ lọc, quay lại bước lọc.
4. Ở danh sách, dùng **bộ lọc** để thu hẹp (trạng thái lọc giữ trên URL, có nút xóa bộ lọc).
5. Chọn một hướng dẫn (Card) để mở trang chi tiết.
6. Trang chi tiết lần lượt: ảnh mẫu → cách tạo dáng → vị trí chụp → góc máy/cỡ cảnh → camera → thông số → các bước thực hiện.
7. Người dùng đi chụp theo hướng dẫn.

**Nhánh phụ:** Thiết bị → Danh sách thiết bị → Chi tiết thiết bị (camera/lens, tiêu cự). ID hướng dẫn hoặc thiết bị không tồn tại → trang 404.

## 6. User Flow Admin (đã chốt)

1. Truy cập `/Admin`.
2. **Chưa đăng nhập** → chuyển về `/Account/Login`; đăng nhập sai thì báo lỗi chung chung và ở lại trang đăng nhập.
3. Đã đăng nhập nhưng **Role khác Admin** → Access Denied.
4. Role = Admin → vào Dashboard.
5. Chọn khu vực: hướng dẫn, danh mục, thiết bị.
6. **Thêm / Sửa:** nhập dữ liệu và upload ảnh → kiểm tra hợp lệ (validate). Không hợp lệ: hiển thị lỗi và quay lại form. Hợp lệ: lưu vào database rồi quay lại danh sách.
7. **Xóa:** hộp xác nhận. Hủy thì không xóa; đồng ý thì xóa. Nếu danh mục/thiết bị đang được sử dụng thì bị chặn kèm thông báo rõ.
8. **Đăng xuất** (POST) bất cứ lúc nào.

Mọi thao tác Admin được kiểm tra Authentication và Authorization **ở server**; không chỉ ẩn nút trên giao diện. Form POST có Anti-forgery token.

## 7. Thay đổi so với tài liệu v1.1

| Mục v1.1 | Thay đổi |
| --- | --- |
| 11.1 | Sửa ô lỗi về bảng Users (dùng ASP.NET Core Identity; mật khẩu được hash) |
| 11.3, bảng 6 (Scenes) | Thêm Icon, IsFeatured, SortOrder; unique (Type, Name) |
| 11.3, bảng 11 (DeviceLenses) | Aperture đổi thành decimal(4,2) |
| 11.3, bảng 12 (CameraSettings) | Aperture đổi thành decimal(4,2); thêm DeviceType, ZoomFactor |
| 11.3, bảng 3 (GuideSteps) | Ràng buộc unique (PhotoGuideId, StepNumber) |
| 11.4 | Thêm bảng OnDelete; Devices/DeviceLenses → CameraSettings là Restrict; PhotoGuidePose là entity tường minh có SortOrder |
| 11.5 | Thay sơ đồ ERD bằng bản v1 đã chốt |
| 12.3 | Bỏ `/Admin/Images`; thêm `/Admin`, `/Account/Logout`, `/Account/AccessDenied`; chốt route chi tiết theo `{id}`; CameraSettings nhập trong form hướng dẫn |
| 12.4 | Bổ sung nhánh «không có kết quả», nhánh Thiết bị, nhánh đăng nhập sai và xác nhận xóa |

## 8. Đánh dấu phiên bản và checklist hoàn thành S0-04

- Nhánh: `docs/s0-04-finalize-erd-sitemap-flow`
- Commit: `docs: finalize ERD v1, sitemap and user flows (#<số issue>)`
- Pull Request: tiêu đề theo quy ước commit, mô tả có `Closes #<số issue>`, Squash and merge rồi xóa nhánh.
- Sau khi merge: gắn tag `design-v1.0` trên `main`. Tag `v1.0.0` dành cho bản MVP phát hành.
- Cập nhật «Phiên bản tài liệu» thành **1.2** và thêm dòng mới vào «Lịch sử thay đổi» của tài liệu DPhotos.

| Việc cần làm | Xong |
| --- | --- |
| Chốt ERD v1 | ☐ |
| Kiểm tra quan hệ 1-N | ☐ |
| Kiểm tra quan hệ N-N | ☐ |
| Chốt Sitemap | ☐ |
| Chốt User Flow người dùng | ☐ |
| Chốt User Flow Admin | ☐ |
| Kiểm tra ERD phù hợp phạm vi MVP | ☐ |
| Ghi thay đổi vào tài liệu DPhotos | ☐ |
| Gắn tag `design-v1.0` | ☐ |
