# @harjs/service

A lightweight, generic, and fully type-safe HTTP client built on top of the native **Fetch API** to streamline backend integrations.

[![npm version](https://img.shields.io/npm/v/@harjs/service.svg?style=flat-square)](https://www.npmjs.com/package/@harjs/service)
[![npm downloads](https://img.shields.io/npm/dm/@harjs/service.svg?style=flat-square)](https://www.npmjs.com/package/@harjs/service)
[![license](https://img.shields.io/npm/l/@harjs/service.svg?style=flat-square)](https://github.com/kaancetin-f/harjs-design-service/blob/main/LICENSE)
[![bundle size](https://img.shields.io/bundlephobia/minzip/@harjs/service?style=flat-square)](https://bundlephobia.com/package/@harjs/service)

## 🚀 Key Features

- **Zero Dependencies:** No extra runtime overhead; it directly leverages the native Fetch API of browsers and Node.js.
- **Full Type-Safety:** Seamlessly aligns with TypeScript generics and native `RequestInit`/`Response` overrides.
- **Global Interceptors:** Manage pre-request modifications (e.g., attaching Bearer tokens) and post-response logic (e.g., 401 handling) from a single, centralized configuration.
- **Smart Content-Type Management:** Automatically strips clashing header information during `FormData` submissions, allowing the browser to correctly set its own multi-part `boundary` values.

---

## 📦 Installation

To add the package to your project, run the command corresponding to your preferred package manager:

```bash
npm install @harjs/service
# or
yarn add @harjs/service
# or
pnpm add @harjs/service
```

---

## 🛠️ Global Configuration & Interceptors

You can globally customize headers or hook into the request/response lifecycle before creating API endpoints.

Create an initialization file in your project (e.g. `api.config.ts`):

```ts
import { setApiConfig } from "@harjs/service";

setApiConfig({
  headers: {
    Accept: "application/json",
    "Content-Type": "application/json",
    "X-Custom-Header": "MyApp-Client",
  },

  // Request Interceptor:
  // Triggered immediately before the fetch operation takes place.
  requestInterceptor: async (input, init) => {
    const token = localStorage.getItem("token");

    const headers = {
      ...init.headers,
      ...(token ? { Authorization: `Bearer ${token}` } : {}),
    } as Record<string, string>;

    return [input, { ...init, headers }];
  },

  // Response Interceptor:
  // Triggered right before the response is returned to the application.
  responseInterceptor: async (response) => {
    if (response.status === 401) {
      // Global unauthorized handling
      // (e.g., redirecting the user to the login page)
      window.location.href = "/login";
    }

    return response;
  },
});
```

### Default Headers

With this configuration, the following headers are sent with every request:

- `Accept: application/json`
- `Content-Type: application/json`
- `X-Custom-Header: MyApp-Client`

### Request Interceptor

The `requestInterceptor` runs right before the HTTP request is sent.

Common use cases:

- Reading tokens from `localStorage`
- Adding `Authorization` headers
- Dynamically modifying request configuration

### Response Interceptor

The `responseInterceptor` runs right before the response is returned to the application.

Common use cases:

- Forcing logout on `401 Unauthorized`
- Global error handling
- Transforming or logging responses

---

## 🧑‍💻 Basic Usage

To manage your service layer in a modular way, you can create a new instance from the `Api` class.

### 1. Creating a Service Instance

Define a base configuration for a specific microservice or API cluster:

```ts
import Api from "@harjs/service";

// Base configuration for a specific microservice or API cluster
const mainService = new Api({
  host: "https://api.yourdomain.com",
  core: "v1", // Safely appends /v1/ to the URL
});

export default mainService;
```

With this configuration, all requests will be sent to:

```txt
https://api.yourdomain.com/v1/
```

---

### 2. Performing HTTP Requests

The library supports common HTTP methods such as `GET`, `POST`, `PUT`, and `DELETE`.

---

### GET Request

```ts
import mainService from "./mainService";

interface User {
  id: number;
  name: string;
}

const getUserProfile = async (userId: number) => {
  const { response } = await mainService.Get({
    input: `users/${userId}`,
  });

  if (response.ok) {
    const data: User = await response.json();
    return data;
  }
};
```

**Request URL:**

```txt
GET https://api.yourdomain.com/v1/users/1
```

---

### POST Request (JSON Data)

The `data` field is automatically serialized using `JSON.stringify()`.

```ts
const createNewUser = async (userData: any) => {
  const { response } = await mainService.Post({
    input: "users",
    data: userData,
  });

  return response.ok;
};
```

**Request URL:**

```txt
POST https://api.yourdomain.com/v1/users
```

---

### POST Request (File Upload - FormData)

You can use `FormData` for file uploads.

```ts
const uploadAvatar = async (file: File) => {
  const formData = new FormData();
  formData.append("avatar", file);

  const response = await mainService.PostWithFormData({
    input: "users/upload-avatar",
    data: formData,
  });

  return response.json();
};
```

> **Note:** When using `PostWithFormData()`, the `Content-Type` header is automatically removed internally. This allows the browser to generate the correct `multipart/form-data boundary`, preventing file corruption issues.

---

## 🎛️ API Reference

## Constructor Parameters

A new `Api` instance is created as follows:

```ts
new Api(values: {
  host?: string;
  core?: string;
  init?: RequestInit;
});
```

### Properties

| Property | Type          | Description                                                 | Default                                            |
| -------- | ------------- | ----------------------------------------------------------- | -------------------------------------------------- |
| `host`   | `string`      | The base domain for requests.                               | `window.location.origin` (in browser environments) |
| `core`   | `string`      | Optional path segment appended to the URL (e.g., `api/v2`). | `""`                                               |
| `init`   | `RequestInit` | Global initial settings for the native `fetch` instance.    | `undefined`                                        |

---

## Class Methods

### `Get(values)`

Sends a `GET` request to the specified endpoint.

- Returns a `p_response` object containing the async promise structure.
- Provides access to the resolved `response`.

```ts
const { response } = await service.Get({
  input: "users/1",
});
```

---

### `Post(values)`

Sends a `POST` request to the specified endpoint.

- Automatically serializes `data` using `JSON.stringify()`.
- Sends data in `application/json` format by default.

```ts
await service.Post({
  input: "users",
  data: {
    name: "John Doe",
  },
});
```

---

### `PostWithFormData(values)`

Designed for file uploads and `multipart/form-data` requests.

- Sends `FormData` directly.
- Automatically removes the `Content-Type` header to prevent boundary conflicts.

```ts
const formData = new FormData();
formData.append("avatar", file);

await service.PostWithFormData({
  input: "users/avatar",
  data: formData,
});
```

---

### `Put(values)`

Sends a `PUT` request to the specified endpoint.

- Automatically converts payload to JSON.
- Used for updating resources.

```ts
await service.Put({
  input: "users/1",
  data: {
    name: "Jane Doe",
  },
});
```

---

### `Delete(values)`

Sends a standard `DELETE` request to the specified endpoint.

```ts
await service.Delete({
  input: "users/1",
});
```

---

## 📝 License

This project is distributed under the **MIT License**.

See the `LICENSE` file for more details.
