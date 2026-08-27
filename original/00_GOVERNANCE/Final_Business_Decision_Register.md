# Final Business Decision Register — Platform Core & Product

## Status
**FINAL BUSINESS DECISION**

Dokumen ini menyimpan keputusan bisnis yang sudah dikunci dari rangkaian pembahasan PRD. Menjadi input untuk:

```text
PRD Final
   ↓
Core Contract
   ↓
Architecture
   ↓
Vertical Slice
```

---

# 1. Referral & Downline

## 1.1 Active Downline

`active_downline` adalah downline yang telah melakukan pembayaran subscription dan subscription tersebut valid/aktif.

Tidak dihitung:
- free trial
- akun gratis
- pembayaran gagal
- akun suspended
- subscription expired/tidak aktif

Jika subscription dibatalkan tetapi masa berlangganan berbayar masih berjalan, downline tetap dihitung aktif sampai masa tersebut berakhir.

## 1.2 Attribution

Default attribution window: **90 hari**.

- Admin dapat mengubah attribution window.
- Setelah user teratribusi ke Referrer A, attribution tetap milik A.
- Pendaftaran berikutnya melalui Referrer B tidak memindahkan attribution.
- Member yang mendaftar tanpa upline otomatis menjadi **downline Super Admin**.

---

# 2. Referral Commission

- Commission rate: **10%**.
- Dasar komisi: jumlah subscription yang benar-benar dibayar downline.
- Diskon/promo mengurangi dasar komisi.
- Top Up dan PAYG tidak menghasilkan referral commission pada model billing final.

Contoh:

```text
Harga normal = Rp100.000
Diskon       = Rp20.000
Dibayar      = Rp80.000

Komisi = 10% × Rp80.000
       = Rp8.000
```

---

# 3. Commission Availability

```text
Payment
   ↓
Pending
   ↓
Available
```

- Monthly subscription → Available setelah **1 bulan**.
- Annual subscription → Available setelah **3 bulan**.

---

# 4. Refund & Commission

- Refund normal hanya dapat diminta maksimal **3 hari setelah pembelian**.
- Setelah 3 hari: **No Refund**, kecuali policy khusus di masa depan.
- Jika refund terjadi saat commission masih Pending:
  - commission dibatalkan otomatis;
  - upline diberi informasi.
- Commission yang sudah Available tidak masuk refund normal karena melewati refund window.
- Jika koreksi/clawback diperlukan karena adjustment finansial, pemotongan dilakukan dari **future commission**.

---

# 5. Milestone

Milestone hanya berdasarkan **current active downline count**, bukan lifetime recruited users.

Contoh:

```text
45 active
↓
5 tidak memperpanjang
↓
40 active
```

Progress milestone menjadi 40.

History achievement boleh disimpan untuk audit.

---

# 6. Membership & Purchased Package

## 6.1 Package

Purchased package **tidak hangus**.

Namun package hanya dapat digunakan jika subscription/membership user aktif.

```text
Membership Active
→ Package Usable

Membership Inactive
→ Package Locked
```

Ketika membership aktif kembali, package yang masih dimiliki dapat digunakan kembali.

## 6.2 Downgrade

Purchased package tetap dimiliki saat membership di-downgrade.

---

# 7. Subscription Cancellation

Jika subscription dibatalkan:

```text
Cancelled
→ Active Until End Date
→ Expired
```

Semua membership entitlements tetap dapat digunakan sampai tanggal expiry.

---

# 8. Manual Transfer Payment

Manual transfer harus diverifikasi Admin satu per satu.

Flow:

```text
Order
→ Manual Transfer
→ Member Transfer
→ Bukti transfer melalui Support Ticket
→ Admin Verification
→ Payment Approved
→ Entitlement Granted
```

---

# 9. Payment Gateway

Payment gateway bersifat **configurable per product/market**.

Jika sebuah metode gagal, member dapat mencoba metode pembayaran lain yang tersedia.

Tidak ada kewajiban failover otomatis.

---

# 10. Product & Add-on

Admin dapat membuat product/add-on baru **selama capability yang dibutuhkan sudah tersedia di core**.

Admin dapat menentukan:
- nama
- description
- price
- currency
- entitlement
- duration/policy
- billing type
- market
- visibility
- active/inactive

Product baru tidak perlu hard-coded ke business logic apabila capability-nya sudah tersedia di core.

---

# 11. Currency

Minimum core-ready:

```text
IDR
USD
```

Harga dapat ditentukan **secara independen per currency**.

Harga USD tidak harus berasal dari konversi otomatis IDR.

---

# 12. Support SLA

## First Response

| Priority | Target |
|---|---|
| Urgent | ≤ 4 jam kerja |
| High | ≤ 8 jam kerja |
| Normal | ≤ 1 hari kerja |
| Low | ≤ 2 hari kerja |

## Resolution Target

| Priority | Target |
|---|---|
| Urgent | ≤ 1 hari kerja |
| High | ≤ 2 hari kerja |
| Normal | ≤ 5 hari kerja |
| Low | ≤ 10 hari kerja |

SLA adalah target operasional, bukan jaminan absolut.

---

# 13. Support Auto-Close

Jika ticket berstatus `Resolved` dan member tidak membalas selama **7 hari**, ticket dapat otomatis menjadi `Closed`.

Durasi dihitung dari response/resolution terakhir Admin.

---

# 14. Closed Ticket Reopen

- Member tidak dapat reopen ticket Closed.
- Hanya Admin yang dapat reopen.
- Member dapat membuat ticket baru untuk kasus lanjutan.
- Ticket baru dapat dikaitkan dengan ticket lama.

---

# 15. Support Attachment

- Maksimum: **2 MB per file**
- Format: **PDF, PNG, JPG**
- Retention: **90 hari setelah ticket Closed**

Retention Support Attachment berbeda dari content export retention 48 jam.

---

# 16. Security — Single Login

Policy:

> **1 account = 1 active session**

Login pada device/session baru:

```text
Device B Login
→ Device A Session Revoked
→ Device B Active
```

---

# 17. Content Protection

Default:

> **ON untuk protected content**

## Auto-Blur

Saat browser/tab/window kehilangan focus:

> hanya protected area yang di-blur.

## Protection Indicator

Tidak perlu menampilkan indikator khusus kepada member.

## Limitation

Content protection adalah deterrence/protection, bukan jaminan absolut terhadap screenshot atau screen recording tingkat OS.

---

# 18. Internationalization

Bahasa mengikuti **market**.

Minimum core-ready:

```text
Indonesia
English
```

Bahasa lain harus dapat ditambahkan tanpa redesign core.

---

# 19. Currency per Market

Currency default bersifat **configurable per market**.

Contoh:

```text
Indonesia → IDR
Market global tertentu → USD
```

---

# 20. White-label Pricing

Agency/white-label dapat menggunakan:

- percentage markup;
- fixed override price.

Keduanya didukung.

---

# 21. White-label Price Synchronization

Jika Webmaster mengubah harga master product:

> behavior harga Agency **configurable**.

Agency dapat mengikuti atau mempertahankan pricing sesuai policy tenant.

---

# 22. White-label Wholesale Deposit Model

White-label menggunakan model **agency wholesale settlement**, berbeda dari billing member biasa.

Contoh:

```text
Agency Deposit
= Rp1.000.000

Webmaster Base Cost
= Rp1.000 / generation

Agency Retail Price
= Rp2.000 / generation
```

Saat customer Agency membayar Rp2.000:

```text
Agency settlement cost
→ Rp1.000 dipotong dari Agency Deposit
```

Sehingga:

```text
Customer Billing
= Customer → Agency

Wholesale Settlement
= Agency → Webmaster
```

---

# 23. White-label Core Status

White-label **core-ready tetapi tidak dibangun penuh sekarang**.

Core hanya menyiapkan fondasi:

- tenant
- tenant-aware authorization
- API boundary
- product synchronization contract
- tenant pricing
- domain mapping
- branding foundation

Full White-label UI dan operational system dibuat pada project/fase tersendiri.

---

# 24. Final Billing Model

Untuk member biasa:

```text
Membership
    +
Feature Packages
    +
Add-ons
    ↓
Entitlements
    ↓
Feature Usage
```

Tidak menggunakan:

```text
Top Up
PAYG Wallet
Deposit Balance
```

---

# 25. Admin Flexibility

Admin dapat secara fleksibel:

- membuat role;
- menentukan permission;
- membuat product;
- membuat add-on;
- menentukan price;
- menentukan currency;
- menentukan entitlement;
- menentukan duration/policy;
- menentukan market;
- mengaktifkan/nonaktifkan product;
- menambah provider;
- mengubah payment configuration.

Business configuration yang memang dirancang configurable tidak boleh hard-coded.

---

# 26. Role & Membership Separation

```text
Membership
→ entitlement / package / product benefit

Role
→ permission / access control
```

Keduanya terpisah.

Contoh:

```text
User
├── Membership: Growth
└── Role: Content Manager
```

---

# 27. Final Status

Seluruh aturan dalam dokumen ini berstatus **FINAL BUSINESS DECISION** dan menjadi input untuk:

```text
PRD Final
→ Core Contract
→ Architecture
→ Vertical Slice Implementation
```

Dokumen ini tidak menggantikan PRD atau architecture document. Ia menjadi referensi keputusan bisnis yang sudah dikunci.


---

# 28. Manual Transfer — Payment Confirmation

Manual transfer memiliki workflow final:

```text
Order
   ↓
Manual Transfer
   ↓
Member Transfer
   ↓
Bukti Transfer dikirim melalui Support Ticket
   ↓
Admin Approve Ticket
   ↓
Payment = Paid
   ↓
Entitlement Granted
```

Keputusan:

> **Approval Admin pada ticket manual transfer menjadi trigger resmi untuk mengubah payment menjadi `Paid` dan memberikan entitlement yang dibeli.**

Tidak diperlukan approval pembayaran terpisah setelah ticket disetujui.

---

# 29. Agency / White-label Deposit Availability

Agency deposit menggunakan model **wholesale settlement balance**.

Deposit Agency belum dianggap available ketika baru dibuat/request.

Deposit menjadi:

```text
Available
```

hanya setelah:

```text
Agency Deposit
   ↓
Webmaster Approval
   ↓
Balance Credited to White-label Account
   ↓
Deposit Available
```

Saldo yang sudah tersedia dapat digunakan untuk settlement biaya Webmaster.

---

# 30. Agency / White-label Deposit Refund Policy

Agency deposit:

> **tidak dapat di-refund.**

Deposit merupakan saldo settlement untuk kebutuhan wholesale antara Agency/White-label dan Webmaster.

Jadi:

```text
Agency Deposit
→ Approved
→ Credited
→ Available
→ Used for Webmaster Settlement
```

Tidak tersedia withdrawal/refund normal atas saldo deposit tersebut.

---

# 31. Subscription Inactive & New Package Purchase

Aturan final:

Jika subscription/membership user **tidak aktif**:

```text
Existing Purchased Package
→ tetap dimiliki
→ Locked / tidak dapat digunakan
```

Dan:

```text
New Package Purchase
→ TIDAK diperbolehkan
```

User harus mengaktifkan subscription terlebih dahulu.

Flow:

```text
Subscription Inactive
   ↓
Package Purchase Attempt
   ↓
Blocked
   ↓
Prompt:
"Activate your subscription first."
```

Setelah subscription kembali aktif:

```text
Subscription Active
   ↓
Existing Package
→ Usable

New Package Purchase
→ Allowed
```

---

# 32. Final Billing & Entitlement Rule

Dengan keputusan terbaru, hubungan subscription dan package menjadi:

```text
Subscription Active
    │
    ├── Membership Entitlements
    │       → Usable
    │
    └── Purchased Packages
            → Usable
            → New package purchase allowed
```

Sedangkan:

```text
Subscription Inactive
    │
    ├── Membership Entitlements
    │       → Locked
    │
    ├── Existing Purchased Packages
    │       → retained
    │       → Locked
    │
    └── New Package Purchase
            → Not Allowed
```

Package tidak hangus, tetapi package selalu berada di bawah syarat:

> **Subscription harus aktif untuk menggunakan atau membeli package.**

---

# 33. Final Business Decision Status

Dengan penambahan keputusan pada bagian 28–32:

> Seluruh business rules yang sebelumnya terbuka pada questionnaire referral, support, billing, payment, package, security, internationalization, dan white-label yang telah dibahas sekarang telah memiliki keputusan final.

Dokumen ini menjadi acuan utama untuk:

```text
PRD Final
   ↓
Core Contract
   ↓
Core Architecture
   ↓
Vertical Slice Implementation
```

Detail implementasi teknis tetap ditentukan pada dokumen architecture/core masing-masing tanpa mengubah business rules yang sudah dikunci di sini.
