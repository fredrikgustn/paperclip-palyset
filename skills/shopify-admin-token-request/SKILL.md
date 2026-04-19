# Shopify Admin Token Request Skill

Purpose: obtain a valid Shopify Admin API access token for `palyset-se.myshopify.com` using app credentials, then verify token usability for theme validation work.

## Inputs Required
- `SHOPIFY_SHOP_DOMAIN` (example: `palyset-se.myshopify.com`)
- `SHOPIFY_ADMIN_API_CLIENT_ID`
- `SHOPIFY_ADMIN_API_SECRET`
- App redirect URI configured in Shopify app settings

## Scopes Required
- `read_themes`
- `read_content`

## Flow
1. Generate install URL:
   - `https://{shop}/admin/oauth/authorize?client_id={client_id}&scope=read_themes,read_content&redirect_uri={redirect_uri}&state={nonce}`
2. Merchant installs/approves app.
3. Capture `code` from redirect callback.
4. Exchange code for access token:
   - `POST https://{shop}/admin/oauth/access_token`
   - Body: `client_id`, `client_secret`, `code`
5. Store returned `access_token` in secrets as runtime token for API calls.

## Verification
Run both checks with `X-Shopify-Access-Token: {access_token}`:
- `GET /admin/api/2025-10/shop.json`
- `GET /admin/api/2025-10/themes.json`

Success criteria:
- HTTP `200` from both endpoints.
- At least one target theme ID identified for validation.

## Handoff Template
Post in task comment:
- Token validity: pass/fail
- Confirmed scopes: list
- API version used: e.g. `2025-10`
- Target theme ID(s)
- Rotation owner + expiry notes
