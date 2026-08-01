---
name: Create and track a REDX parcel
description: Book a last-mile delivery parcel with REDX and follow it to delivery.
api: openapi/shopup-redx-openapi.yml
operations: [listPickupStores, listAreas, createParcel, trackParcel, getParcelInfo]
---

# Create and track a REDX parcel

Use REDX (ShopUp's last-mile courier network in Bangladesh) to book a delivery and
track it. All requests go to `https://openapi.redx.com.bd/v1.0.0-beta` (use
`https://sandbox.redx.com.bd/v1.0.0-beta` while testing).

## Authentication
Every call requires the merchant access token in the `API-ACCESS-TOKEN` header,
formatted as `Bearer {jwt_token}`. A missing/invalid token returns
`401 {"message":"Unauthorized","status_code":401}`.

## Steps
1. **Pick a pickup store** — call `listPickupStores` (GET `/pickup/stores`) and
   choose the `pickup_store_id` to ship from. If none exist, register one with
   `createPickupStore` (POST `/pickup/store`).
2. **Resolve the delivery area** — call `listAreas` (GET `/areas`) and match the
   customer's area to get its `delivery_area_id` (and `delivery_area` name).
3. **Create the parcel** — call `createParcel` (POST `/parcel`) with the required
   fields: `customer_name`, `customer_phone`, `delivery_area`, `delivery_area_id`,
   `customer_address`, `cash_collection_amount`, `parcel_weight` (grams), and
   `value`. Include `pickup_store_id` and an optional `merchant_invoice_id`.
   Keep the returned tracking id.
4. **Track** — call `trackParcel` (GET `/parcel/track/{trackingId}`) for the status
   timeline, or `getParcelInfo` (GET `/parcel/info/{trackingId}`) for full detail.

## Notes
- Errors use a `{ message, status_code }` envelope (not RFC 9457).
- No idempotency-key mechanism is documented — avoid blind retries on `createParcel`.
- Versioning is in the URI path (`v1.0.0-beta`).
