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
