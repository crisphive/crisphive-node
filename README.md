# CrispHive Node

The official Node.js / TypeScript SDK for the
[CrispHive API](https://docs.crisphive.com/).

Typed access to the public `/v1` API — customers, bookings, catalog, team and
fleet. Works in Node.js and the browser (built on [axios](https://github.com/axios/axios)).

## Requirements

Node.js 16+ (ES2017+). TypeScript definitions are bundled.

## Installation

```sh
npm install @crisphive/sdk
```

## Authentication

Every request is authenticated with a secret API key sent as a bearer token.
Create keys from your CrispHive business dashboard. **The key prefix selects the
data environment:**

- `chsk_live_…` → live (production) data
- `chsk_test_…` → sandbox (isolated test) data

Load keys from the environment — never commit them.

## Usage

```ts
import { Configuration, CustomerApi } from "@crisphive/sdk";

const config = new Configuration({
  accessToken: process.env.CRISPHIVE_API_KEY, // SDK adds the "Bearer " prefix
});

const customers = new CustomerApi(config);

async function main() {
  // List customers.
  const { data } = await customers.listCustomers({ limit: 20 });
  for (const c of data.data?.customers ?? []) {
    console.log(c.id, c.full_name);
  }

  // Create a customer.
  const created = await customers.createCustomer({
    customerCreateRequest: { full_name: "Ada Lovelace", email: "ada@example.com" },
  });
  console.log("created", created.data.data?.customer_id);
}

main().catch(console.error);
```

## Pagination

List methods accept `{ page, limit }` and return a `meta` object (`total`,
`count`, `per_page`, `current_page`, `total_pages`).

## Idempotency

`create` calls (customers, bookings) accept an `Idempotency-Key` so retries never
create a duplicate. Pass it via the per-request axios options.

## Errors

A non-2xx response rejects with an axios error; read `err.response?.status` and
`err.response?.data` for the CrispHive error code.

## Documentation

- Docs: https://docs.crisphive.com
- API reference: https://docs.crisphive.com/technical-reference
- Webhooks: https://docs.crisphive.com/webhook

## License

[MIT](LICENSE)
