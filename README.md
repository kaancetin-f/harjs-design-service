# @harjs/service

A lightweight, generic, and fully type-safe HTTP client built on top of
the native Fetch API to streamline backend integrations.

[![npm
version](https://img.shields.io/npm/v/@harjs/service.svg?style=flat-square)](https://www.npmjs.com/package/@harjs/service)
[![npm
downloads](https://img.shields.io/npm/dm/@harjs/service.svg?style=flat-square)](https://www.npmjs.com/package/@harjs/service)
[![license](https://img.shields.io/npm/l/@harjs/service.svg?style=flat-square)](https://github.com/kaancetin-f/harjs-design-service/blob/main/LICENSE)
[![bundle
size](https://img.shields.io/bundlephobia/minzip/@harjs/service?style=flat-square)](https://bundlephobia.com/package/@harjs/service)

---

## Features

- Zero Dependencies: No extra runtime overhead; it directly leverages
  the native Fetch API of browsers and Node.js.
- Full Type-Safety: Seamlessly aligns with TypeScript generics and
  native RequestInit/Response overrides.
- Global Interceptors: Manage pre-request modifications (e.g.,
  attaching Bearer tokens) and post-response logic (e.g., 401
  handling) from a single, centralized configuration.
- Smart Content-Type Management: Automatically strips clashing header
  information during FormData submissions, allowing the browser to
  correctly set its own multi-part boundary values.

---

## 1. Installation

```bash
npm install @harjs/service
# or
yarn add @harjs/service
# or
pnpm add @harjs/service
```

---

## 2. Global Configuration & Interceptors

```ts
import { setApiConfig } from "@harjs/service";

setApiConfig({
  headers: {
    Accept: "application/json",
    "Content-Type": "application/json",
  },

  requestInterceptor: async (input, init) => {
    const token = localStorage.getItem("token");

    const headers = {
      ...init.headers,
      ...(token ? { Authorization: `Bearer ${token}` } : {}),
    };

    return [input, { ...init, headers }];
  },

  responseInterceptor: async (response) => {
    if (response.status === 401) {
      window.location.href = "/login";
    }
    return response;
  },
});
```

---

## 3. Basic Usage

### 3.1 Creating a Service Instance

```ts
import Api from "@harjs/service";

const mainService = new Api({
  host: "https://api.yourdomain.com",
  core: "v1",
});
```

OR

```ts
import Service from "@harjs/service";

class MainService extends Service {}

export default new MainService({
  host: "https://api.yourdomain.com",
  core: "v1",
});
```

---

## 4. HTTP Requests

### GET

```ts
const { response } = await mainService.Get({
  input: "users/1",
});
```

### POST (JSON)

```ts
const { response } = await mainService.Post({
  input: "users",
  data: { name: "John Doe" },
});
```

### POST (FormData)

```ts
const formData = new FormData();
formData.append("avatar", file);

const response = await mainService.PostWithFormData({
  input: "users/avatar",
  data: formData,
});
```

### PUT

```ts
await mainService.Put({
  input: "users/1",
  data: { name: "Jane Doe" },
});
```

### DELETE

```ts
await mainService.Delete({
  input: "users/1",
});
```

---

## API Reference

### Constructor

```ts
new Api({
  host?: string;
  core?: string;
  url?: string;
  init?: RequestInit;
});
```

---

## License

MIT License
