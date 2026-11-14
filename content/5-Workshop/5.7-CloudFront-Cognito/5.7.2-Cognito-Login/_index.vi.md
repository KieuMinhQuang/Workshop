---
title: "Cấu hình Amazon Cognito Login cho CloudFront"
weight: 2
chapter: false
pre: "<b>5.7.2.</b> "
---

## Step 1 – Mở App Client đã tạo

Trong **AWS Console → Amazon Cognito → User pools → `techvina-user-pool` → App clients**, chọn App Client **`techvina-frontend`** đã tạo tại **5.5.2**. Mở tab **Login pages → Edit**.

## Step 2 – Cập nhật Callback URL và Sign-out URL

Sử dụng domain CloudFront của mục **5.7.1**:

| Trường | Giá trị |
|---|---|
| Allowed callback URLs | `https://<CF_DOMAIN>/` |
| Default redirect URL | `https://<CF_DOMAIN>/` |
| Allowed sign-out URLs | `https://<CF_DOMAIN>/` |
| Identity providers | `Cognito user pool` |
| OAuth 2.0 grant types | `Authorization code grant` |
| OpenID Connect scopes | `openid`, `email`, `phone` |

Có thể giữ thêm `http://localhost:5173/` nếu cần phát triển cục bộ; các URL thêm vào phải khớp chính xác với `redirect_uri` của Frontend. **Không tạo App Client Secret** cho SPA.

Ví dụ triển khai thực tế:

```text
https://d3mmtr3bxttvgc.cloudfront.net/
```

Nhấn **Save changes**. Kiểm tra **Managed login pages configuration** hiển thị Callback và Sign-out URL mới.


## Step 3 – Kiểm tra Domain và App Client

Kiểm tra domain tại **User Pool → Domain** và App Client tại **Applications → App clients**. Giá trị đã sử dụng trong quá trình triển khai:

```text
User Pool ID: ap-southeast-1_Gt4JGeuQM
App Client ID: 57285s3j4in7s3s292velui1er
Cognito Domain: https://ap-southeast-1gt4jgeuqm.auth.ap-southeast-1.amazoncognito.com
```

Khi triển khai trên AWS account khác, lấy **User Pool ID, App Client ID và Domain mới** tại đây; không dùng lại thông số của tài khoản triển khai mẫu.

Backend tiếp tục xác minh JWT bằng issuer của User Pool và App Client ID theo mục **5.5.3**. Frontend sử dụng OAuth Authorization Code + PKCE, cấu hình qua các biến `VITE_COGNITO_*` ở **5.7.3**.

Việc tạo nhóm `ADMIN`, `STAFF`, `CUSTOMER`, tài khoản ADMIN, xác minh email và mật khẩu đã thực hiện ở **5.5.2**; không tạo trùng các tài nguyên ở bước này.
