# Khớp contract FE — Danh mục (Doanh: form / variant)

Phần Doanh: form tạo/sửa sản phẩm + variant/tier modal. Cường làm list/detail/category riêng.

## Kết luận rà soát

API DTO và FE types **đã khớp tên field / status**. Không cần đổi `ProductRequest` / `ProductResponse` / `VariantRequest` / controllers.

| Hạng mục | FE | API | Kết quả |
|----------|----|-----|---------|
| Product write body | `ProductInput` | `ProductRequest` | khớp (`isPublished`, `isActive`, `gallery`) |
| Product response | `ProductDetail` | `ProductResponse` | khớp |
| Variant write | `VariantInput` + tiers | `VariantRequest` + `TierInput` | khớp |
| Variant on product | `VariantWithTiers` | `VariantWithTiersResponse` | khớp (`available`, `isActive`) |
| POST `/products` | expect body | **201** | khớp |
| PUT `/products/{id}` | expect body | **200** | khớp |
| Variant POST/PUT | `void` (client 204) | **204** | khớp |

## Chỗ đã sửa (FE mock)

[`hbsisms-web-internal/src/features/products/mock.ts`](../hbsisms-web-internal/src/features/products/mock.ts):

- Trước: POST create **ghi đè** `imageUrl` / `gallery` thành `null` → lệch API thật và form.
- Sau: giữ URL từ body; gallery `[]` / mảng; publish rules giống API (create không publish; update publish cần variant active).

## Khóa contract bằng Jackson test

```powershell
cd hbsisms-api
.\mvnw.cmd test "-Dtest=ProductDtoJacksonTest,VariantDtoJacksonTest,CategoryDtoJacksonTest"
```

- `ProductDtoJacksonTest` — serialize/deserialize `isPublished` / `isActive` / `gallery`
- `VariantDtoJacksonTest` — `tiers`, `isActive`, `available`, `VariantOption`

## Swagger / OpenAPI

SpringDoc UI: `/swagger-ui.html` (hoặc `/swagger-ui/index.html`).

Đã verify bằng test (Docker):

```powershell
.\mvnw.cmd test "-Dtest=CatalogOpenApiContractTest"
```

`GET /v3/api-docs` có `ProductRequest.isPublished` / `isActive`, `VariantRequest.tiers`, `TierInput.minQtyKhay`, `VariantWithTiersResponse.available` — **1 test pass**.

## Smoke form (thủ công)

1. Tạo sản phẩm + ảnh/gallery → reload detail thấy URL
2. Thêm variant + 2 bậc giá → GET detail tiers đúng thứ tự
3. Bật B2C khi chưa có variant → lỗi 400 message API
4. Bật B2C sau khi có variant active → OK
5. Trùng mã / SKU → 409

## Phạm vi không làm (Cường)

CategoriesPage, ProductsPage list/search filters, Category CRUD contract chi tiết.
