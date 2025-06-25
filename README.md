#  Decentralized Cloud Storage on Stacks Blockchain

This smart contract implements a decentralized cloud storage system where users can **upload**, **update**, **delete**, **transfer ownership**, and **manage access permissions** for their files — all transparently and immutably on the **Stacks blockchain**.

---

## 🧩 Features

* 📁 Upload and store metadata for files
* ✏️ Update file name and size
* 🗑️ Delete files
* 🔄 Transfer ownership of files
* ✅ Grant/revoke permissions for others to access files
* 🔍 View file details and system stats

---

## 📜 Contract Constants

```clojure
(define-constant contract-owner tx-sender)
(define-constant err-owner-only (err u100))
(define-constant err-not-found (err u101))
(define-constant err-already-exists (err u102))
(define-constant err-invalid-name (err u103))
(define-constant err-invalid-size (err u104))
(define-constant err-unauthorized (err u105))
(define-constant err-invalid-recipient (err u106))
```

---

## 🧱 Data Structures

### 🔐 `files` (Map)

Stores metadata for each uploaded file:

* `file-id: uint` – Unique identifier
* `owner: principal` – File owner
* `name: (string-ascii 64)` – File name
* `size: uint` – File size (bytes)
* `created-at: uint` – Block height when file was added

### 🧾 `file-permissions` (Map)

Stores access control info for file sharing:

* Key: `{ file-id, user }`
* Value: `{ permission: bool }`

### 📊 `total-files` (Data var)

A counter that keeps track of how many files have been uploaded.

---

## 🔓 Public Functions

### `upload-file (name, size)`

Uploads a new file to the system.

* ✅ Name must be between 1 and 64 characters
* ✅ Size must be > 0 and ≤ 1 GB (`1,000,000,000` bytes)

```clojure
(upload-file "photo.jpg" u2048)
;; Returns: (ok u1)
```

---

### `update-file (file-id, new-name, new-size)`

Updates metadata of a file. Only the file owner can call this.

```clojure
(update-file u1 "photo_v2.jpg" u4096)
;; Returns: (ok true)
```

---

### `delete-file (file-id)`

Deletes a file. Only the file owner can call this.

```clojure
(delete-file u1)
;; Returns: (ok true)
```

---

### `transfer-file-ownership (file-id, new-owner)`

Transfers ownership to another principal.

```clojure
(transfer-file-ownership u1 'SP...XYZ)
;; Returns: (ok true)
```

---

### `grant-permission (file-id, permission, recipient)`

Grants or revokes permission for another user to access a file.

* Can’t grant permission to yourself
* Only file owner can perform this

```clojure
(grant-permission u1 true 'SP...ABC)
;; Returns: (ok true)
```

---

### `revoke-permission (file-id, user)`

Revokes access permission from a specific user.

```clojure
(revoke-permission u1 'SP...ABC)
;; Returns: (ok true)
```

---

## 🔍 Read-Only Functions

### `get-total-files`

Returns the total number of files uploaded.

```clojure
(get-total-files)
;; Returns: (ok u5)
```

---

### `get-file-info (file-id)`

Retrieves metadata for a given file.

```clojure
(get-file-info u1)
;; Returns: (ok { owner: 'SP...', name: "photo.jpg", size: u2048, created-at: u12345 })
```

---

## 🔒 Internal Utility Functions (Private)

* `file-exists` – Checks if a file exists
* `get-owner-file` – Validates ownership
* `get-file-size-by-owner` – Retrieves a file's size

---

## 🚫 Error Handling

| Error Code | Meaning                          |
| ---------- | -------------------------------- |
| `err u100` | Owner-only function restriction  |
| `err u101` | File not found                   |
| `err u102` | File already exists              |
| `err u103` | Invalid file name                |
| `err u104` | Invalid file size                |
| `err u105` | Unauthorized access              |
| `err u106` | Invalid recipient for permission |

---

## 📌 Deployment Notes

* This contract is written in **Clarity**, the smart contract language for the **Stacks blockchain**.
* Be sure to deploy it using a compatible Clarity tool like [Clarinet](https://docs.hiro.so/clarinet) or the Stacks CLI.

---
