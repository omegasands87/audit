# Panduan Operasional Non-Programmer

## Status

**Panduan Operasional untuk Menjalankan, Menguji, dan Memverifikasi Project**

Dokumen ini dibuat khusus untuk operator/project owner yang tidak perlu memahami coding secara mendalam.

Pola kerja:

```text
AI / Developer
→ membuat kode
→ memberi instruksi
→ Anda menjalankan
→ Anda melihat hasil
→ Anda melaporkan hasil
→ AI / Developer memperbaiki
→ Acceptance Gate
→ LOCK
→ lanjut
```

Anda tidak harus memahami TypeScript, Next.js, Hono, Drizzle, PostgreSQL, React, Worker, Queue, atau API architecture untuk mengikuti panduan ini.

---

# 1. Prinsip Utama

Anda berperan sebagai:

```text
Project Owner
+
Operator
+
Validator
```

bukan sebagai programmer atau system administrator.

Aturan utama:

> **Jangan menebak ketika terjadi error.**

Jika perintah menghasilkan error:

```text
STOP
→ jangan mengubah kode secara acak
→ jangan menghapus file
→ jangan menginstal sesuatu sembarangan
→ screenshot / copy error
→ kirim kepada AI / developer
```

---

# 2. Pola Kerja Project

Pembangunan dilakukan dengan:

```text
P0
→ satu slice
→ testing
→ acceptance
→ lock
→ slice berikutnya

P1
→ satu slice
→ testing
→ acceptance
→ lock

P2
→ satu slice
→ testing
→ acceptance
→ lock
```

Titik kerja saat ini:

```text
P0.00
Architecture & Development Skeleton
```

---

# 3. Jangan Menjalankan Semua Perintah Sekaligus

Jika instruksi berisi:

```text
pnpm db:generate
pnpm db:migrate
pnpm dev:api
pnpm dev:worker
pnpm dev:web
```

jangan langsung menjalankan semuanya.

Gunakan:

```text
1 perintah
→ lihat hasil
→ lanjut jika PASS
```

Jika FAIL:

```text
STOP
```

---

# 4. Istilah Dasar

## Terminal
Tempat mengetik perintah untuk menjalankan project.

## Project Folder
Folder utama tempat source code platform berada.

## Command
Perintah yang diketik di Terminal.

Contoh:

```bash
pnpm db:generate
```

## PASS
Hasil sesuai instruksi tanpa error yang relevan.

## FAIL
Ada error atau hasil tidak sesuai.

## LOCKED
Slice telah melewati Acceptance Gate dan boleh dianggap selesai.

---

# 5. Aturan Keamanan Paling Penting

Jangan pernah mengirim ke chat:

```text
Password
API Key
API Secret
Access Key
Private Key
Database Password
JWT Secret
OAuth Client Secret
Webhook Secret
R2 Secret
Production Credential
```

Jika screenshot menampilkan secret:

```text
jangan kirim sebelum secret disamarkan
```

---

# 6. File `.env`

`.env` berisi konfigurasi environment dan kadang secret.

Contoh:

```env
DATABASE_URL=...
SUPABASE_URL=...
SUPABASE_ANON_KEY=...
R2_ACCOUNT_ID=...
R2_ACCESS_KEY=...
R2_SECRET=...
R2_BUCKET_NAME=...
```

Nilai tersebut **tidak boleh ditebak**.

Jika AI memberikan contoh, perlakukan sebagai placeholder sampai Anda memperoleh nilai asli dari dashboard/service yang benar.

Secret asli harus dimasukkan langsung ke environment atau secret manager, bukan ditempel ke chat.

---

# 7. Status Instruksi

```text
🟢 AMAN
Perintah biasa yang boleh dijalankan.

🟡 PERHATIAN
Ikuti instruksi dengan tepat.

🔴 SECRET / SENSITIF
Jangan dibagikan ke chat.
```

---

# 8. Sebelum Mulai Project

Pastikan tersedia:

```text
[ ] Komputer
[ ] Internet
[ ] Project folder
[ ] Git repository
[ ] Node.js
[ ] pnpm
[ ] Browser
[ ] Supabase account/project
[ ] Vercel account
[ ] Cloudflare/R2 account
[ ] Password/Secret Manager
```

Tidak semua provider AI/research/payment diperlukan untuk memulai P0.00.

---

# 9. Cara Membuka Project

Pastikan Anda berada di root project.

Contoh:

```text
C:\Projects\platform-ai\
```

Biasanya akan terdapat:

```text
apps/
modules/
packages/
package.json
pnpm-workspace.yaml
```

Jika struktur tidak sesuai, jangan membuat folder manual tanpa instruksi.

---

# 10. Cara Membuka Terminal

Di Windows:

```text
Buka folder project
→ klik address bar
→ ketik cmd
```

atau gunakan:

```text
PowerShell
```

Jika menggunakan VS Code:

```text
Terminal
→ New Terminal
```

---

# 11. Cek Node.js

Jalankan:

```bash
node -v
```

Jika versi muncul, misalnya:

```text
v22.x.x
```

maka PASS.

Jika muncul:

```text
node is not recognized
```

atau error serupa:

```text
STOP
→ kirim screenshot
```

---

# 12. Cek pnpm

Jalankan:

```bash
pnpm -v
```

Jika versi muncul:

```text
PASS
```

Jika command tidak ditemukan:

```text
STOP
→ kirim error
```

Jangan langsung menginstal versi sembarangan.

---

# 13. Install Dependency

Jika implementation specification meminta:

```bash
pnpm install
```

jalankan dari root project.

Jika selesai tanpa error fatal:

```text
PASS
```

Jika error:

```text
STOP
→ kirim output
```

---

# 14. Acceptance P0.00 — Gambaran Besar

P0.00 harus membuktikan:

```text
Web runs
API runs
Worker runs
Database connects
Boundary exists
Database migration works
App Shell renders
Light/Dark Mode works
Responsive layout works
Shared UI works
```

Anda cukup memverifikasi hasilnya.

---

# 15. P0.00 — Langkah 1: Database Generate

Jalankan:

```bash
pnpm db:generate
```

Tujuan:

```text
menghasilkan artifact/migration yang diperlukan Drizzle
```

Jika selesai tanpa error dan folder migration yang diminta tersedia, PASS.

Jika gagal:

```text
STOP
→ screenshot error
→ kirim
```

---

# 16. P0.00 — Langkah 2: Database Migrate

Jalankan:

```bash
pnpm db:migrate
```

Tujuan:

```text
menerapkan migration ke PostgreSQL
```

Jika selesai tanpa error:

```text
PASS
```

Contoh masalah:

```text
connection refused
password authentication failed
database does not exist
migration failed
```

Jangan menebak penyebabnya.

```text
STOP
→ screenshot
```

---

# 17. P0.00 — Langkah 3: Menjalankan API

Buka Terminal baru:

```bash
pnpm dev:api
```

Biarkan Terminal tetap terbuka.

Buka:

```text
http://localhost:3000/health
```

atau port yang diberikan implementation.

PASS jika endpoint memberikan respons sehat, misalnya:

```json
{
  "status": "ok"
}
```

Jika tidak bisa dibuka atau ada error:

```text
STOP
→ screenshot
```

---

# 18. P0.00 — Langkah 4: Menjalankan Worker

Buka Terminal lain:

```bash
pnpm dev:worker
```

Biarkan Terminal tetap terbuka.

PASS jika worker berjalan tanpa error fatal dan koneksi sesuai instruksi berhasil.

Jika worker crash:

```text
STOP
→ screenshot
```

---

# 19. P0.00 — Langkah 5: Menjalankan Web

Buka Terminal baru:

```bash
pnpm dev:web
```

Buka URL yang muncul di Terminal, misalnya:

```text
http://localhost:3001
```

---

# 20. P0.00 — Pemeriksaan App Shell

Pastikan:

```text
[ ] halaman tidak blank
[ ] tidak ada error overlay
[ ] sidebar terlihat
[ ] top bar terlihat
[ ] main content terlihat
```

Jika error:

```text
STOP
→ screenshot browser + Terminal Web
```

---

# 21. P0.00 — Dark / Light Mode

Cari Theme Toggle.

Coba:

```text
Light
→ Dark
→ Light
```

Periksa:

```text
[ ] tema berubah
[ ] teks tetap terbaca
[ ] card tetap terlihat
[ ] tombol tetap terlihat
[ ] layout tidak rusak
```

Jika rusak:

```text
STOP
→ screenshot
```

---

# 22. P0.00 — Responsive

Persempit ukuran browser.

Periksa apakah:

```text
Sidebar
```

berubah menjadi:

```text
Drawer / menu mobile
```

dan layout tetap dapat digunakan.

Jika rusak:

```text
STOP
→ screenshot
```

---

# 23. P0.00 — Shared UI

Coba komponen yang tersedia:

```text
Button
Card
Input
Badge
Modal
```

Periksa:

```text
[ ] tampil
[ ] klik bekerja
[ ] styling konsisten
[ ] theme bekerja
```

---

# 24. Cara Membaca Error

Anda tidak perlu memahami seluruh error.

Cari bagian seperti:

```text
Error:
Failed to connect
Module not found
Type error
Build failed
Permission denied
Authentication failed
```

Kemudian kirim:

```text
perintah yang dijalankan
+
error
```

---

# 25. Jangan Mengubah Banyak Hal Sekaligus

Jika ada error, jangan langsung:

```text
install banyak package
ganti versi Node
hapus node_modules
hapus lockfile
ubah .env
ubah kode
```

kecuali memang ada instruksi yang jelas.

---

# 26. Jika Terminal Terlihat "Diam"

Command seperti:

```bash
pnpm dev:web
```

memang biasanya tidak kembali ke prompt karena server sedang berjalan.

Jika ragu:

```text
screenshot → kirim
```

---

# 27. Jangan Tutup Terminal Server

Jika dijalankan bersamaan:

```text
Terminal 1 → API
Terminal 2 → Worker
Terminal 3 → Web
```

biarkan tetap terbuka selama pengujian.

---

# 28. Cara Menghentikan Server

Jika diminta:

```text
Ctrl + C
```

Biasanya menghentikan proses pada Terminal aktif.

Pastikan Terminal yang aktif memang server yang ingin dihentikan.

---

# 29. Format Laporan Acceptance

Contoh:

```text
P0.00 Verification

Web: PASS
API: PASS
Worker: PASS
DB: PASS
Migration: PASS
Boundary: PASS
App Shell: PASS
Dark/Light: PASS
Responsive: PASS
Shared UI: PASS
```

Jika ada gagal:

```text
API: FAIL
```

sertakan error untuk bagian tersebut.

---

# 30. Jangan Menulis LOCKED Jika Belum PASS

Gunakan:

```text
P0.00 LOCKED
```

hanya jika seluruh acceptance gate relevan PASS.

Jika belum:

```text
P0.00 masih ada error
```

---

# 31. Setelah P0.00 LOCKED

Baru lanjut:

```text
P0.01
Identity, Session & Single Login
```

Pola selalu:

```text
Specification
→ Build
→ Run
→ Test
→ UI Review
→ Fix
→ Acceptance
→ LOCK
```

---

# 32. Aturan Screenshot

Aman untuk dikirim:

```text
error message
terminal output
website UI
browser console tanpa secret
```

Jangan kirim:

```text
API key
password
secret
token
database password
private credential
```

Jika muncul:

```text
blur / sensor
```

---

# 33. Format Copy-Paste Error

Gunakan:

```text
Slice:
P0.00

Step:
Database Migration

Command:
pnpm db:migrate

Environment:
Development

Result:
FAIL

Error:
[paste error]
```

Format ini paling mudah dianalisis.

---

# 34. Jika Tidak Mengerti Instruksi AI

Langsung katakan:

```text
"Saya tidak mengerti langkah ini. Jelaskan langkah demi langkah."
```

Tidak perlu menebak.

---

# 35. Jika AI Meminta Secret

Jangan menempelkan secret.

Tanyakan:

```text
"Di mana saya harus memasukkan secret ini?"
```

atau:

```text
"Berikan langkah untuk memasukkannya secara lokal."
```

---

# 36. Perintah Berbahaya

Berhenti jika melihat perintah yang dapat menghapus data, misalnya:

```bash
rm -rf ...
DROP DATABASE ...
TRUNCATE ...
DELETE ...
format ...
```

Jangan jalankan tanpa memahami tujuan dan dampaknya.

---

# 37. Database Safety

Jangan menjalankan command destructive di Production.

Contoh:

```text
DROP
TRUNCATE
DELETE ALL
RESET DATABASE
```

Untuk Development pun pastikan memahami dampaknya.

---

# 38. Environment Safety

Sebelum menjalankan command, pastikan environment:

```text
Local
Development
Staging
Production
```

Perintah yang aman di Development belum tentu aman di Production.

---

# 39. Production Rule

Untuk Production gunakan:

```text
backup
→ migration plan
→ deployment
→ smoke test
→ monitoring
```

Jangan menjalankan perubahan Production hanya berdasarkan instruksi singkat tanpa konfirmasi.

---

# 40. Vercel

Pada tahap awal Anda mungkin hanya perlu:

```text
Login Vercel
→ buka project
→ lihat deployment
→ lihat build status
```

Anda tidak perlu memahami konfigurasi internal setiap deployment.

---

# 41. Supabase

Tahap awal biasanya membutuhkan:

```text
Project
→ Database
→ Auth
→ API settings
```

Jangan mengubah schema database manual jika project sudah menggunakan migration.

---

# 42. Cloudflare R2

Yang perlu diketahui operator:

```text
Bucket
Access Credentials
Endpoint
Storage Object
```

Jangan menghapus object Production hanya karena terlihat sebagai file biasa.

Lifecycle mengikuti Storage Contract.

---

# 43. API Key Inventory

Untuk setiap provider aktif, catat:

```text
Nama Provider
Environment
Tujuan
Status
Pemilik
Tanggal Dibuat
Tanggal Rotasi
Lokasi Secret
```

Nilai secret aktual tetap berada di Secret Manager.

---

# 44. Provider Pool

Platform menggunakan pool:

```text
Text & Analysis
Image
Video
Voice
Research & Data
```

Admin nantinya dapat mengelola provider.

Operator tidak perlu mengubah konfigurasi provider secara manual di source code.

---

# 45. Mock Provider

Mock Provider adalah provider simulasi untuk testing.

Tujuannya:

```text
tidak mengeluarkan biaya
tidak membutuhkan API key nyata
dapat menguji workflow
```

Mock result bukan representasi performa provider nyata.

---

# 46. Development vs Production Data

Development:

```text
boleh test data
```

Production:

```text
real user
real payment
real content
real provider cost
```

Jangan mencampurkannya.

---

# 47. Jika Project Tidak Bisa Start

Prosedur:

```text
1. Jangan panik.
2. Jangan ubah banyak hal.
3. Catat perintah terakhir.
4. Screenshot error.
5. Kirim screenshot/error.
6. Tunggu instruksi perbaikan.
```

---

# 48. Jika Website Blank

Periksa:

```text
1. Terminal Web
2. Apakah ada error?
3. URL benar?
4. Browser refresh
```

Jika tetap blank:

```text
STOP
→ screenshot browser
→ screenshot Terminal Web
```

---

# 49. Jika Database Error

Catat:

```text
Perintah:
pnpm db:migrate
```

Jangan mengganti `DATABASE_URL` sembarangan.

Kirim:

```text
error
+
environment yang digunakan
```

tanpa password database.

---

# 50. Jika API Error

Periksa:

```text
Terminal API
```

kemudian endpoint health yang diberikan.

Kirim:

```text
perintah
+
hasil Terminal
+
hasil browser
```

---

# 51. Jika Worker Error

Periksa Terminal Worker.

Catat jika tersedia:

```text
error
job
provider
database
```

Jangan menghapus queue atau data worker tanpa instruksi.

---

# 52. Jika UI Tidak Sesuai

Gunakan `UIUX_Design_Plan`.

Contoh laporan:

```text
warna salah
spacing salah
dark mode rusak
sidebar salah
mobile layout rusak
button tidak berfungsi
```

sertakan screenshot.

---

# 53. Visual Review

Setiap slice dengan UI dinilai dari:

```text
Visual
Functional
Responsive
Persistence
Permission
Error State
Loading State
```

Tampilan bagus saja belum cukup.

---

# 54. Functional Review

Untuk setiap halaman, lakukan:

```text
klik tombol
isi form
submit
refresh
pindah halaman
kembali
logout
login kembali
```

Pastikan data tidak hilang secara tidak semestinya.

---

# 55. Persistence Review

PRD menetapkan bahwa hasil kerja penting bertahan lintas halaman, refresh, dan sesi. fileciteturn33file2L70-L78

Untuk modul seperti:

```text
Research
Analyzer
Planner
Blueprint
```

uji:

```text
Save
→ pindah halaman
→ kembali
→ refresh
→ logout/login
```

---

# 56. Permission Review

Untuk feature yang memiliki permission:

```text
authorized user
→ boleh

unauthorized user
→ ditolak
```

Jangan hanya memeriksa apakah tombol terlihat.

---

# 57. Acceptance vs Feedback

Feedback seperti:

```text
"Ini terasa terlalu rumit."
"Button kurang jelas."
"Informasi terlalu padat."
"Layout mobile kurang nyaman."
```

bukan otomatis berarti slice gagal.

Bedakan:

```text
Bug
UX issue
Design adjustment
Business rule issue
Feature request
```

---

# 58. Jangan Mengubah Scope Sendiri

Ide baru sebaiknya dicatat sebagai:

```text
Future Improvement
```

jangan langsung disisipkan ke slice berjalan jika mengubah scope.

---

# 59. Open Business Decision

Jika muncul pertanyaan bisnis:

```text
"Apakah user boleh melakukan X?"
"Apakah paket berlaku Y?"
"Apakah admin boleh Z?"
```

jangan menebak.

Masukkan sebagai:

```text
Business Decision
```

---

# 60. Kapan Harus Berhenti

Berhenti dan minta bantuan jika:

```text
API error
Database error
Migration error
Build error
Authentication error
Secret error
Provider error
Data hilang
Website blank
Unexpected data change
```

---

# 61. Kapan Boleh Lanjut

Lanjut jika:

```text
hasil sesuai instruksi
tidak ada error
data sesuai
UI dapat digunakan
```

Jika AI mengatakan PASS tetapi Anda melihat website rusak:

> **Jangan anggap PASS.**

Verifikasi nyata di environment Anda lebih penting.

---

# 62. P0.00 LOCK

P0.00 dapat dikatakan LOCKED jika:

```text
[PASS] Web runs
[PASS] API runs
[PASS] Worker runs
[PASS] DB connects
[PASS] Boundary exists
[PASS] Database migrations work
[PASS] App Shell renders
[PASS] Light/Dark Mode works
[PASS] Responsive layout works
[PASS] Shared UI components work
```

dan tidak ada blocker yang tersisa.

---

# 63. Dokumen Referensi Operator

Dokumen yang menjadi referensi utama:

```text
PRD Final
Business Decision Register
Core Architecture V1
Implementation Roadmap
Implementation Specification per Slice
UIUX Design Plan
Environment & Deployment Strategy
Engineering Constitution
Panduan Operasional Non-Programmer
```

Untuk pekerjaan sehari-hari, terutama gunakan:

```text
Implementation Specification
+
Panduan Operasional
```

---

# 64. Lima Aturan Jika Lupa Semuanya

```text
1. Satu langkah sekali.
2. Jangan menebak.
3. Jangan kirim secret.
4. Error → screenshot/kirim.
5. PASS → baru lanjut.
```

---

# 65. Next Operational Step

Mulai dari pemeriksaan paling sederhana:

```bash
node -v
```

kemudian:

```bash
pnpm -v
```

Setelah keduanya PASS, lanjutkan ke database dan Acceptance Gate P0.00 sesuai instruksi implementation.
