---
title: "Configure Amazon Cognito Login for CloudFront"
weight: 2
chapter: false
pre: "<b>5.7.2.</b> "
---

## Step 1 – Open the existing App Client

In **AWS Console → Amazon Cognito → User pools → `techvina-user-pool` → App clients**, open the **`techvina-frontend`** App Client created in **5.5.2**. Choose **Login pages → Edit**.

## Step 2 – Set CloudFront callback and sign-out URLs

Use the CloudFront domain created in **5.7.1**:

| Field | Value |
|---|---|
| Allowed callback URLs | `https://<CF_DOMAIN>/` |
| Default redirect URL | `https://<CF_DOMAIN>/` |
| Allowed sign-out URLs | `https://<CF_DOMAIN>/` |
| Identity providers | `Cognito user pool` |
| OAuth 2.0 grant types | `Authorization code grant` |
| OpenID Connect scopes | `openid`, `email`, `phone` |

Optionally retain `http://localhost:5173/` for local development; the URL must match the frontend's `redirect_uri` exactly. Do **not** generate a Client Secret for the SPA.

Actual deployment example:

```text
https://d3mmtr3bxttvgc.cloudfront.net/
```

Choose **Save changes**. Verify the new callback and sign-out entries under **Managed login pages configuration**.


## Step 3 – Verify the domain and App Client

Inspect **User Pool → Domain** and **Applications → App clients**. The actual deployment uses:

```text
User Pool ID: ap-southeast-1_Gt4JGeuQM
App Client ID: 57285s3j4in7s3s292velui1er
Cognito Domain: https://ap-southeast-1gt4jgeuqm.auth.ap-southeast-1.amazoncognito.com
```

For a different AWS account, use its own **User Pool ID, App Client ID, and Domain** rather than these example values.

The backend validates JWTs against the User Pool issuer and the App Client configured in **5.5.3**. The frontend uses OAuth Authorization Code + PKCE and the `VITE_COGNITO_*` settings from **5.7.3**.

The `ADMIN`, `STAFF`, `CUSTOMER` groups, initial ADMIN account, email confirmation, and password have already been created in **5.5.2**; do not recreate them here.
