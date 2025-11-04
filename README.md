# 🧩 Product Onboarding Wizard (with Infinite Scroll Dropdown)

## 🧠 Overview

Build a **Multi-Step Product Onboarding Wizard** using **React + TanStack Query (React Query)**.  
The form should dynamically load data from JSON endpoints, support infinite scrolling in dropdowns, and post the final submission to a webhook.

---

## 🧾 Steps

### **Step 1: Category Selection**

- Fetch the list of categories from `/categories`.
- Display categories in a dropdown.
- Once a category is selected, move to the next step.
- Cache the category data using React Query to prevent unnecessary refetches.

---

### **Step 2: Product Selection (Infinite Scroll Dropdown)**

- Fetch products from `/products?category={selectedCategory}`.
- Use pagination with:
  ```
  ?_page=1&_limit=10
  ```
- Display products in a dropdown that supports **infinite scroll**:
  - Initially show 10 products.
  - On scroll to the bottom, fetch the next 10.
  - Continue until all category products are loaded.
- Prevent duplicate items and redundant API calls.
- Show a loader during pagination fetches.

---

### **Step 3: Review & Submit**

- Display a review screen showing:
  - Selected **category**
  - Selected **product details**
  - Editable fields: **name** and **price** (allow the user to change them before submission)
- On clicking “Submit”, send the following payload to the provided webhook:

```json
{
  "category": "Electronics",
  "product": {
    "id": 101,
    "name": "NoiseFit Halo Smartwatch",
    "price": 3499,
    "description": "AMOLED display smartwatch with SpO2 and heart-rate monitor"
  },
  "edited": {
    "name": "NoiseFit Halo Smartwatch Pro",
    "price": 4999
  },
  "submittedAt": "2025-11-04T10:15:00Z"
}
```

**Webhook URL:**

```
https://webhook.site/db13dadf-431d-4ec9-9e2b-7110c68aef38
```

The timestamp (`submittedAt`) should be generated dynamically at submission time in ISO format.

---

## ⚙️ Technical Requirements

- Use **TanStack Query (React Query)** for:
  - Fetching + caching categories
  - Paginated fetching of products
  - Infinite scrolling
- Use **React Hook Form** or controlled components for input handling.
- Ensure dropdowns are reusable components supporting async data loading.
- Handle loading, empty, and error states properly.
- Do not use any UI library — minimal styling only.

---

## 📂 Data Source

**Base URL**

```
http://localhost:3000
```

**Endpoints**

```
/categories
/products
```

Each endpoint contains realistic mock data (20 categories × 100 products each).  
Use pagination (`?_page` and `?_limit`) only for `/products`.

---

## 🧭 Expected Flow

1. User opens the wizard.
2. Category dropdown loads → user selects one.
3. Product dropdown appears → infinite scroll fetches products 10 at a time.
4. User selects one product → proceeds to review.
5. User can edit product name and price.
6. On submit → payload (as above) is sent to the webhook.

---

## 📁 Files

### 1. **categories.json**

Contains a list of 20 realistic product categories.

**Example:**

```json
[
  { "id": 1, "name": "Electronics" },
  { "id": 2, "name": "Groceries" },
  { "id": 3, "name": "Furniture" }
]
```

---

### 2. **products.json**

Contains ~2,000 product records (100 per category).

Each record follows this schema:

```json
{
  "id": 101,
  "categoryId": 1,
  "name": "NoiseFit Halo Smartwatch",
  "price": 3499,
  "description": "AMOLED display smartwatch with SpO2 and heart-rate monitor"
}
```

---

## 🧠 API Setup (Local Mock Server)

To simulate backend behavior locally, run:

```bash
npm install -g json-server
npx json-server --watch db.json --port 4000
```

Combine both files into a single `db.json`:

```json
{
  "categories": [ ... ],
  "products": [ ... ],
  "finalize": []
}
```

Your API will expose endpoints:

| Method | Endpoint                    | Description                  |
| ------ | --------------------------- | ---------------------------- |
| GET    | `/categories`               | Fetch all product categories |
| GET    | `/products?categoryId=<id>` | Fetch products by category   |
| GET    | `/products/:id`             | Fetch a single product       |
| PATCH  | `/products/:id`             | Update product details       |
| POST   | `/finalize`                 | Final submission             |

---
