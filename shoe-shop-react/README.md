# Shoe Shop Management — React + Tailwind + Firebase

This is a React/Vite conversion of the supplied `Pasted text.txt` shoe-shop application. It keeps the same business concept: public catalogue, owner login, products and size-wise stock, counter billing, UPI payment details, sales/profit reports, CSV export, shop settings and JSON backup/restore.

## 1. Setup

1. Install Node.js LTS.
2. Open this folder in VS Code.
3. Run `npm install`.
4. Copy `.env.example` to `.env` and paste the Firebase web-app config values.
5. Run `npm run dev` and open the local URL shown by Vite.

## 2. Firebase services

Enable **Authentication → Email/Password**, **Cloud Firestore**, and **Storage** in the same Firebase project used by the old app. Create the owner account in Firebase Authentication.

The app intentionally keeps the existing Firestore paths so existing data can be reused:

- `products/{productId}` — public catalogue fields (name, person, type, use, sell, sizes, img)
- `pub/shop` — public shop name, WhatsApp and address
- `sales/{saleId}` — bills and report data
- `owner/costs` — private buying prices keyed by product ID
- `owner/settings` — private UPI ID

Product images are now uploaded to Firebase Storage and the download URL is saved in `products/{productId}.img`. Old Base64 image values can still display, but newly uploaded images use Storage.

## 3. Important security setup before production

Do not launch with open Firestore or Storage rules. The original HTML file contained Firebase client configuration but did not include the rules, so the project cannot safely assume the database is protected. Configure rules so:

- Anyone can read `products` and `pub/shop` only.
- Only the authorized shop owner can create/update/delete products, sales, costs and settings.
- Public users must never read `sales`, `owner/costs` or `owner/settings`.
- Storage uploads are allowed only to the authenticated owner, with image MIME-type and size limits.

For a single-owner shop, restrict writes to the exact owner UID in the rules. Do not rely only on hiding the admin pages in React.

## 4. Existing data and limitations to test

- Existing product and shop documents use the same collection/document structure.
- Existing bills should remain readable if their fields match the old app.
- Existing sales editing/deletion and old-device import are not yet included in this first React conversion; retain the old app backup before switching.
- UPI information is shown for the cashier; the app does not verify payment automatically. Confirm payment in the UPI app before saving a bill.
- Stock deduction for a new bill uses a Firestore transaction to avoid overselling when two counters update the same item simultaneously.
- The original old app stores buying prices in one owner document; this conversion preserves that format for compatibility. For a multi-owner or larger deployment, move private data to per-document records and add a trusted server/API layer.

## 5. Build

Run `npm run build`. Deploy the generated `dist/` folder to Firebase Hosting or another static host after testing Firebase rules and all shop workflows.
