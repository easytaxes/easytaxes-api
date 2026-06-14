# EasyTaxes API (ET API)

> **⚠️ Closed Beta**
> The API is currently operating in a closed beta and is available only to a limited number of participants. Public access is not yet available.
> To join the waiting list, please visit our contact page: https://easytaxes.am/en/contact

---

## About EasyTaxes

EasyTaxes is an [online accounting service in Armenia](https://easytaxes.am/en/). By automating invoice processing, bank statement analysis, tax reporting, and interactions with the tax authorities, we are able to provide a reliable and affordable service. This allows us to spend more time delivering better support at a lower cost.

## What is the EasyTaxes API?

The ET API provides programmatic access to the EasyTaxes platform. You can retrieve company information, download tax reports and documents, upload invoices and files, and automate workflows without logging into the application.

The API also provides limited access to taxpayer data available through Armenia's tax system at file-online.taxservice.am.

Use the ET API to integrate EasyTaxes with your internal systems, monitoring tools, or LLM-based applications.

Modifying company statuses or other critical data is not available through the API. Such operations can only be performed through the EasyTaxes application.

### Who Can Access the API

The API is available to customers with an active EasyTaxes subscription. To obtain access, you must:

1. Have an active EasyTaxes subscription
2. Apply for beta participation via https://easytaxes.am/en/contact
3. Wait for approval and receive a personal API key

---

## Authentication

All API requests require authentication using a personal API key.

**How to obtain an API key:**

Log in to your EasyTaxes account → **Settings** → **API** tab → click **Create API Key**.

Each customer receives a unique personal key. The key is displayed only once, so make sure to store it securely.

**How to use the API key:**

Include the key in the `Authorization` header of every request:

```

Authorization: Bearer YOUR_API_KEY

````

**Example request:**

```bash
curl -X GET "https://open-api.easytaxes.am/v1/companies" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
````

---

## Base URL

```
https://open-api.easytaxes.am/v1
```

---

## Endpoints

### Companies

| Method | Endpoint                  | Description                                    |
| ------ | ------------------------- | ---------------------------------------------- |
| `GET`  | `/companies`              | Get all companies associated with the customer |
| `GET`  | `/companies/{id}`         | Get detailed information about a company       |
| `GET`  | `/companies/{id}/status`  | Get the current company status                 |
| `GET`  | `/companies/{id}/details` | Get company registration and legal information |

---

### Tax Reports

| Method | Endpoint                                                       | Description                                     |
| ------ | -------------------------------------------------------------- | ----------------------------------------------- |
| `GET`  | `/companies/{id}/tax-reports`                                  | Get a list of company tax reports               |
| `GET`  | `/companies/{id}/tax-reports/{reportId}`                       | Get details of a specific tax report            |
| `GET`  | `/companies/{id}/tax-reports/{reportId}/status`                | Get tax filing status                           |
| `GET`  | `/companies/{id}/tax-reports/{reportId}/document/download`     | Download the tax report document                |
| `GET`  | `/companies/{id}/tax-reports/{reportId}/confirmation/download` | Download the tax authority receipt confirmation |

---

### Invoices

| Method | Endpoint                                        | Description                       |
| ------ | ----------------------------------------------- | --------------------------------- |
| `GET`  | `/companies/{id}/invoices`                      | Get a list of company invoices    |
| `GET`  | `/companies/{id}/invoices/{invoiceId}`          | Get details of a specific invoice |
| `POST` | `/companies/{id}/invoices`                      | Upload a new invoice              |
| `GET`  | `/companies/{id}/invoices/{invoiceId}/download` | Download the invoice file         |

---

### Documents & Files

| Method | Endpoint                                  | Description                           |
| ------ | ----------------------------------------- | ------------------------------------- |
| `GET`  | `/companies/{id}/files`                   | Get a list of all company files       |
| `GET`  | `/companies/{id}/files/{fileId}`          | Get information about a specific file |
| `POST` | `/companies/{id}/files`                   | Upload a new file                     |
| `GET`  | `/companies/{id}/files/{fileId}/download` | Download a file                       |

---

### Analytics

| Method | Endpoint                                     | Description                       |
| ------ | -------------------------------------------- | --------------------------------- |
| `GET`  | `/companies/{id}/analytics/overview`         | Get company overview analytics    |
| `GET`  | `/companies/{id}/analytics/tax-summary`      | Tax payments summary for a period |
| `GET`  | `/companies/{id}/analytics/invoices-summary` | Invoice summary for a period      |
| `GET`  | `/companies/{id}/analytics/expenses`         | Expense analytics                 |
| `GET`  | `/companies/{id}/analytics/revenue`          | Revenue analytics                 |
| `GET`  | `/companies/{id}/analytics/deadlines`        | Upcoming deadlines and reminders  |

---

### Customer Profile

| Method | Endpoint           | Description                                    |
| ------ | ------------------ | ---------------------------------------------- |
| `GET`  | `/me`              | Get information about the current customer     |
| `GET`  | `/me/subscription` | Get information about the current subscription |

---

## Rate Limits

To ensure fair resource distribution, the API uses a **token bucket** rate-limiting algorithm.

| Period   | Limit       | Interval | Burst       |
| -------- | ----------- | -------- | ----------- |
| 1 minute | 60 requests | 1 second | 10 requests |

**Period** — the time window during which the maximum number of requests can be made.

**Limit** — the maximum number of requests allowed during the period. Up to 60 requests can be made per minute. Requests should be distributed evenly over time.

**Interval** — the recommended delay between requests: 60 seconds / 60 requests = **1 second**. Following this interval helps prevent rate-limit errors.

**Burst** — the maximum number of requests that can be sent immediately without waiting between requests. The currently available burst capacity is returned in the `X-Ratelimit-Remaining` response header.

### Response Headers

`X-Ratelimit-Remaining` — the number of requests currently available without delay. The value decreases by 1 after each request. When it reaches `0`, the next immediate request will result in a `429` error. Capacity is replenished automatically over time.

If the rate limit is exceeded, the API returns a `429 Too Many Requests` error. Before retrying, use the following response headers to determine how long to wait:

| Header              | Description                                              |
| ------------------- | -------------------------------------------------------- |
| `X-Ratelimit-Retry` | Number of seconds before the request can be retried      |
| `X-Ratelimit-Limit` | Maximum burst capacity that will be restored             |
| `X-Ratelimit-Reset` | Number of seconds until burst capacity is fully restored |

**Example response when the limit is exceeded:**

```
HTTP/1.1 429 Too Many Requests
X-Ratelimit-Retry: 3
X-Ratelimit-Limit: 10
X-Ratelimit-Reset: 45
```

---

## Data Formats

* All requests and responses use **JSON**
* Supported upload formats: `PDF`, `XLSX`, `DOCX`, `PNG`, `JPG`, `WebP`, `HEIC`
* Character encoding: `UTF-8`
* Date and time format: `ISO 8601` (`YYYY-MM-DDTHH:MM:SSZ`)

---

## Support

If you have any questions or encounter any issues with the API, please contact us via:

[https://easytaxes.am/en/contact](https://easytaxes.am/en/contact)

```
```
