# Khớp contract FE ↔ API khách hàng (task 25)

Báo cáo đóng tracking **#25**. Path/field/enum CRUD đã khớp từ đầu (DTO API viết theo FE); task này siết validation FE/mock và ghi nhận phần address/price.

**Nhánh Duy:** `feature/internal-customer-fe-contract`

---

## Phần Khôi — address / price (đã làm)

PR `#45` `fix(web): align customer address/price FE contract`:

- `addAddress` / `updateAddress` typed `CustomerAddress` (API 201/200 + body)
- `proposePrice` ghi chú 204 No Content

Không nằm trong diff Duy task 25.

---

## Phần Duy — CRUD / B2C — đã làm + báo cáo

### Wire contract (đã khớp, xác nhận)

| API | FE | Status / body |
|-----|-----|----------------|
| `GET /customers?q&type&isActive&page&pageSize` | `customersApi.list` | 200 `Page<CustomerListItem>` |
| `GET /customers/{id}` | `customersApi.get` | 200 `CustomerDetail` |
| `POST /customers` | `customersApi.create` | **201** `CustomerDetail` |
| `PUT /customers/{id}` | `customersApi.update` | 200 `CustomerDetail` |
| `PATCH /customers/{id}/active` | `customersApi.setActive({ isActive })` | 200 `CustomerDetail` |

Enum JSON: `b2c` / `b2b` (lowercase). Field camelCase khớp `CustomerInput` / detail.

### Thay đổi FE (Duy)

| File | Nội dung |
|------|----------|
| `customerValidation.ts` (mới) | Normalize/validate phone + tax giống `CustomerService` |
| `CustomerFormModal.tsx` | Chặn phone/tax/SĐT liên hệ sai trước khi gọi API; normalize `+84` khi submit |
| `mock.ts` | POST/PUT fail **400** phone/tax/B2C terms (không silent coerce); giữ **409** B2B→B2C còn nợ |
| `api.ts` | Comment status create 201 / update 200 / setActive body |

### Stub chấp nhận (chưa wire sales)

| Field | API thật | Ghi chú |
|-------|----------|---------|
| `orderCount` | luôn `0` | List vẫn hiện cột; đếm đơn khi có sales |
| `recentOrders` | luôn `[]` | Tab đơn: empty “Khách chưa có đơn hàng” |

### Checklist smoke (API thật, `VITE_USE_MOCK=false`)

- [ ] List lọc `q` / `type` / `isActive`
- [ ] Create B2C / B2B; B2C không gửi MST/credit/net
- [ ] Phone hoặc tax sai → lỗi form (không tới API) / mock 400
- [ ] Lock / unlock + lọc inactive
- [ ] B2B→B2C còn nợ → 409 (nếu có data nợ)

---

## Đóng tracking

1. Khôi — address/price contract (#45) ✅  
2. Duy — CRUD contract + validation/mock (mục này) ✅  
