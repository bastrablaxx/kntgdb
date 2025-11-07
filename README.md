This is a [Next.js](https://nextjs.org) project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app).

## Getting Started

First, run the development server:

```bash
npm run dev
# or
yarn dev
# or
pnpm dev
# or
bun dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `app/page.js`. The page auto-updates as you edit the file.

This project uses [`next/font`](https://nextjs.org/docs/app/building-your-application/optimizing/fonts) to automatically optimize and load [Geist](https://vercel.com/font), a new font family for Vercel.

## Learn More

To learn more about Next.js, take a look at the following resources:

- [Next.js Documentation](https://nextjs.org/docs) - learn about Next.js features and API.
- [Learn Next.js](https://nextjs.org/learn) - an interactive Next.js tutorial.

You can check out [the Next.js GitHub repository](https://github.com/vercel/next.js) - your feedback and contributions are welcome!

## Deploy on Vercel

The easiest way to deploy your Next.js app is to use the [Vercel Platform](https://vercel.com/new?utm_medium=default-template&filter=next.js&utm_source=create-next-app&utm_campaign=create-next-app-readme) from the creators of Next.js.

Check out our [Next.js deployment documentation](https://nextjs.org/docs/app/building-your-application/deploying) for more details.

## Database Migration (Payment Fields)

To enable payment tracking in transactions, ensure your database has these columns:

```sql
ALTER TABLE transaksi 
  ADD COLUMN IF NOT EXISTS payment_received DECIMAL(12,2) DEFAULT 0,
  ADD COLUMN IF NOT EXISTS change_amount DECIMAL(12,2) DEFAULT 0;
```

You can run the migration with MySQL client:

```bash
# From project root
# Option 1: Run full setup (creates tables and adds payment columns)
mysql -u <user> -p <database_name> < manual_db_setup.sql

# Option 2: Only run the ALTER TABLE snippet above in your SQL client
```

## API: POST /api/transaksi

Request body example:

```json
{
  "items": [
    { "id": 1, "nama": "Beras Premium 5kg", "harga": 75000, "jumlah": 2 },
    { "id": 4, "nama": "Mie Instan Ayam", "harga": 3500, "jumlah": 10 }
  ],
  "payment_received": 200000
}
```

Response:

```json
{
  "success": true,
  "transaksi_id": 123,
  "change": 17500
}
```

Notes:
- Server validates `payment_received >= total`. If not, returns `400`.
- `change` is calculated server-side as `payment_received - total`.
- Stock reduction and transaction details behave as before.
