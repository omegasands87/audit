# DOKUMEN SPESIFIKASI MASTER (PRD)
## Platform Pembuat Konten AI — Platform-Agnostic

> Dokumen ini adalah acuan tunggal resmi untuk **kebutuhan produk dan business rules** platform. Detail implementasi teknis berada di dokumen Core Architecture, Security, i18n, White-label, dan Vertical Slice terkait.

---

## 1. Visi Produk

Platform all-in-one otomatisasi konten berbasis AI yang membantu content creator merencanakan jadwal, meriset ide, menganalisis sumber mentah, menyusun skrip, menghasilkan aset media (gambar/video/suara), hingga mengedit dan mengekspor konten siap unggah — **tanpa mengikat member pada satu platform sosial media tertentu**.

**Prinsip Platform-Agnostic**: sistem hanya peduli pada **jenis konten** (Gambar/Carousel atau Video Pendek), bukan platform tujuan. Member bebas mengunggah hasil akhirnya ke Instagram, TikTok, YouTube Shorts, atau kombinasi ketiganya sekaligus — keputusan itu sepenuhnya di tangan member, di luar sistem.

**Roadmap berbasis jenis konten** — lihat §20.

---

## 2. Prinsip UI/UX & Arsitektur Frontend

### A. Fondasi Visual
- **Dual Theme Engine**: Native toggle Dark Mode / Light Mode di semua halaman.
- **Card-Based Architecture**: Seluruh komponen memakai kartu dengan rounded corner 12–16px, border stroke 1px, dan efek shadow/glassmorphism halus untuk hierarki visual. (Detail token warna & tipografi lengkap ada di dokumen `UIUX_Design_Plan`.)
- **Responsive Grid**: Optimal di desktop (workstation utama) dan tablet/mobile.

### B. Prinsip Client-Side Processing
Semua proses yang *bisa* dijalankan di browser wajib dijalankan di client, bukan di server, untuk efisiensi biaya dan kecepatan. Termasuk:
- Cropping, resizing, filter preview, penempatan teks/layer di canvas editor
- Preview background-remover sebelum commit
- Trimming/pemotongan klip di timeline video (preview & non-destruktif)
- Rendering waveform audio, drag-and-drop timeline
- Live text overlay pada slide (sebelum export)
- **Upload media milik member sendiri** (§9) — file yang diunggah manual oleh member untuk dipakai sebagai aset diproses di client-side dan tidak disimpan ke server kecuali member secara eksplisit membawanya masuk ke Editor untuk diedit/diekspor (baru di titik itu file boleh disimpan sementara di server, tunduk pada kebijakan retensi §12).

Yang **wajib di server** hanya proses yang butuh model AI berat: generate gambar/video/voiceover AI, transkripsi, analisis sumber, dan rendering final export.

### C. Prinsip Persistensi Data & Sesi

Ini prinsip UX fundamental yang berlaku di **seluruh modul**, terutama Riset (§5) dan Analyzer (§7):

- **Hasil kerja tidak boleh hilang karena berpindah halaman.** Setiap hasil riset (Competitor Tracker, Trend Explorer, Keyword Research, dst) dan hasil analisa sumber (ringkasan, daftar angle) **disimpan otomatis sebagai draft** yang terikat ke slot konten/workspace terkait — bukan hanya tersimpan sementara di memori browser (in-memory state).
- **Bertahan lintas sesi**: karena tersimpan di sisi server/akun member (bukan cuma local state), hasil tetap ada meski member menutup tab, refresh halaman, atau logout lalu login kembali.
- **Reset bersifat independen per modul**: setiap modul (tiap tab Riset, dan Analyzer) punya **tombol Reset sendiri-sendiri**. Menekan Reset di satu modul **tidak memengaruhi** hasil di modul lain — misal reset hasil Trend Explorer tidak menghapus hasil Competitor Tracker atau hasil Analyzer.
- **Kapan hasil benar-benar diperbarui/hilang**: hanya dalam dua kondisi — (1) member menekan tombol Reset secara eksplisit pada modul terkait, atau (2) member menjalankan analisa/riset **baru** pada slot yang sama (hasil lama otomatis digantikan hasil baru, dengan konfirmasi jika diperlukan).
- **Indikator visual**: tiap kartu hasil menampilkan status kecil "Tersimpan otomatis" agar member merasa aman berpindah-pindah halaman tanpa takut kehilangan progres.

---

## 3. Arsitektur Modul Utama (Micro-Modules)

### A. Arsitektur Level Tertinggi — Three-Engine Architecture

Di atas alur kerja detail (§3-B), seluruh platform berdiri di atas 3 "engine" besar yang berbagi infrastruktur yang sama:

```
                    [ PLATFORM CORE ]
                          │
        ┌─────────────────┼─────────────────┐
        ↓                 ↓                 ↓
  [ RESEARCH        [ CONTENT          [ ANALYTICS
   INTELLIGENCE ]     ENGINE ]          ENGINE ]
        │                 │                 │
        └─────────────────┼─────────────────┘
                           ↓
              [ SHARED PLATFORM INFRASTRUCTURE ]
```

- **Research Intelligence** = Modul 0 (Riset & Insight Center, §5) — bertugas mengubah data mentah jadi keputusan konten.
- **Content Engine** = gabungan Planner (§6) → Analyzer (§7) → Script Review (§8) → Asset Preparation (§9) → Editor (§10/§11) — seluruh pipeline produksi konten.
- **Analytics Engine** = Modul Analytics & Performance Center (§13) — evaluasi hasil nyata di lapangan.
- **Shared Platform Infrastructure** = fondasi yang dipakai ketiga engine di atas: Auth & Session, Role & Permission, Configuration Service, Product & Entitlement, Billing & Payment Abstraction, AI/Research Provider Pools, Storage & Auto-Purge (§12), Audit, Notification, Feature Flags, dan Tenant Foundation. Detail teknisnya dipisahkan dalam dokumen Core Architecture terkait. Perubahan di infrastruktur ini otomatis berdampak ke ketiga engine sekaligus — inilah alasan kenapa provider pool, kuota, dan role dikelola terpusat di Admin, bukan per-modul.

Reframe ini penting untuk fase pembangunan **core/backend**: tim bisa membangun 3 engine sebagai domain service yang relatif independen satu sama lain, selama semuanya konsisten mengakses lapisan infrastruktur bersama di bawahnya.

### B. Alur Kerja Detail (Data Flow)

```
[Modul 0: Riset & Insight] → [Modul 1: Planner] → [Modul 2: Analyzer] → [Modul 3: Script Review]
        ↑                                                                          ↓
        │                                                       [Modul 4: Asset Preparation]
        │                                                                          ↓
        │                                                [Modul 5: Canvas/Timeline Editor]
        │                                                                          ↓
[Modul Analytics & Performance] ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← [Export & Storage]
```

Alur ini membentuk **siklus tertutup (closed loop)**: riset menginformasikan rencana konten, hasil produksi diekspor, performanya dievaluasi di Analytics, lalu insight-nya mengalir kembali ke Riset & Planner untuk periode berikutnya.

**Karakteristik alur**:
1. **Tidak ada pemilihan platform** di Planner maupun Analyzer — sistem hanya bekerja berdasarkan jenis konten (Gambar/Video).
2. **1 sumber di Analyzer bisa menghasilkan lebih dari satu angle/tema**, dan tiap angle boleh cocok untuk Gambar **dan/atau** Video sekaligus (tidak eksklusif).
3. **Generate/siapkan aset adalah tahap tersendiri (Modul 4)** sebelum masuk Editor — Editor difokuskan untuk menyusun aset yang sudah siap.
4. **Member bisa upload media sendiri** sebagai alternatif generate AI, diproses client-side.
5. **Multi-klien/multi-workspace** dan distribusi white-label disiapkan sebagai capability future/core-ready; full white-label bukan bagian rilis awal (lihat §17 dan §20).
6. **`content_slot_id` adalah identitas utama** yang menghubungkan seluruh lifecycle satu konten lintas modul (Planner → Analyzer → Script → Asset → Editor → Export) — lihat detail di §6-H. Ini memastikan member tidak merasa "membuat proyek baru" tiap kali berpindah modul.

Setiap fitur (riset, analisa, penulisan skrip, generate gambar, generate video, voiceover, evaluasi performa) berjalan sebagai **modul spesialis terpisah**, agar masing-masing bisa di-maintain, di-scale, dan diganti provider AI-nya secara independen.

---

## 3.5. Cross-Cutting Core-Ready Requirements

Kapabilitas berikut harus disiapkan pada core sejak awal, tetapi tidak semuanya harus aktif pada rilis pertama:

- **Single Login / Session Control** — satu account memiliki satu active session; login baru memutus session lama.
- **Internationalization (i18n)** — minimum Indonesia + English; bahasa lain dapat ditambahkan; default language mengikuti market.
- **Multi-Currency** — minimum IDR + USD; currency default configurable per market.
- **Content Protection Policy** — protected content dapat menggunakan deterrence seperti right-click/shortcut restriction, PrintScreen/F12 handling, DevTools deterrence, dan Auto-Blur saat focus loss. Default ON untuk protected content; detail teknis berada di dokumen Security & Content Protection.
- **White-label Foundation** — tenant, tenant-aware authorization, API boundary, product sync, tenant pricing, domain/branding foundation. Full white-label bukan rilis awal.
- **Configurable Provider Pools** — termasuk Research & Data Provider Pool sebagai pool tersendiri.
- **Configurable Product Catalog** — Admin dapat membuat product/add-on selama capability tersedia di core.

## 4. Navigasi Global (App Shell)

### A. Sidebar (Kiri)
- Header: Logo, nama aplikasi, versi sistem
- Menu utama: Riset & Insight (Search), Content Planner (Calendar), AI Source Analyzer (Cpu), Script & Visual Prompts (FileText), Asset Preparation (Sparkles), Image Canvas Editor (Layout), Video Timeline Editor (Video), Storage & Auto-Purge (HardDrive, badge "48h"), Analytics & Performance (BarChart), Referral & Milestones (Users), Support Tickets (MessageSquare)
- Bottom bar: Account & Billing (Settings), Admin Godmode (Shield, badge "Admin")

### B. Top Bar
- Judul halaman aktif (dinamis) — satu workspace/rencana konten aktif per akun (default Starter/Growth).
- Widget usage: sisa entitlement gambar & video yang tersedia, status membership, dan shortcut menuju **Account & Billing**
- Theme switcher (☀️/🌙)
- Profile menu: avatar, nama, badge tier (Starter/Growth/Agency), logout

> **Catatan Agency Mode (Coming Soon)**: fitur multi-workspace (tab berpindah antar rencana konten/klien) dibuka khusus untuk role **Agency Mode** dengan harga & mekanisme terpisah — lihat §17-H dan §20. Ini menjaga agar satu langganan standar tidak dipakai mengelola banyak klien tanpa biaya tambahan.

---

## 5. Modul 0 — Riset & Insight Center (Content Intelligence Engine)

**Fungsi**: pusat riset yang menjadi *dasar keputusan* sebelum konten direncanakan — bukan sekadar kumpulan dashboard data, melainkan **decision engine**. Modul ini berdiri sendiri di sidebar dan bisa diakses kapan saja, juga terhubung langsung ke Planner. **Seluruh hasil di tiap tab modul ini tersimpan otomatis dan persisten** — lihat prinsip di §2-C.

### A. Prinsip Inti: 4 Lapisan Data → Insight → Opportunity → Action

Setiap fitur riset di modul ini, apa pun tabnya, harus berujung pada 4 lapisan berikut — member tidak boleh dipaksa menganalisis grafik mentah secara manual untuk menyimpulkan sendiri apa yang harus dibuat:

| Lapisan | Pertanyaan yang Dijawab | Output |
|---|---|---|
| **DATA** | Apa yang terjadi? | Angka, tren, posting, keyword, komentar, pola mentah |
| **INSIGHT** | Apa artinya? | Pola yang terdeteksi + penjelasan AI |
| **OPPORTUNITY** | Di mana celahnya? | Topik/angle/format yang berpotensi dimanfaatkan |
| **ACTION** | Apa yang harus dibuat? | Rekomendasi konten siap dikirim ke Planner/Analyzer |

Data mentah & grafik tetap tersedia untuk audit (transparansi), tapi **Insight dan Action harus selalu terlihat lebih menonjol** daripada tabel angka mentah.

### B. Research Overview — Halaman Ringkasan Keputusan

Area overview di atas semua tab detail — bukan pengganti tab, tapi lapisan keputusan cepat "apa yang harus dilakukan sekarang":

- **Niche Pulse**: 3–5 topik yang sedang naik/turun di niche member.
- **Competitor Moves**: perubahan strategi kompetitor paling signifikan.
- **Audience Signals**: pertanyaan/pain point/kebutuhan yang sedang muncul.
- **Own Winners**: 3 pola dari konten member sendiri yang terbukti bekerja (dari §5-H).
- **Content Gaps**: 3–5 peluang dengan coverage kompetitor rendah (dari §5-C).
- **Recommended Actions**: 3–5 ide prioritas, tiap ide punya tombol **"Buat Konten"** langsung.
- **Research Freshness**: kapan data terakhir diperbarui & sumber/provider yang dipakai (transparansi, lihat §5-J).

### C. Content Gap & Opportunity Engine (Prioritas Tertinggi)

Ini lapisan yang **mengubah** competitor + trend + keyword + audience menjadi keputusan konkret — bagian paling bernilai dari seluruh Modul 0.

**Definisi**: Content Gap = kebutuhan/demand yang terdeteksi tapi belum dilayani baik oleh kompetitor dan/atau belum optimal dilayani member sendiri.

**Sumber sinyal**: pertanyaan/komentar audiens, keyword/search intent, momentum tren, coverage topik kompetitor, performa konten kompetitor, performa konten sendiri, seasonality/event.

**Opportunity Score** — skor komposit dari beberapa komponen (bobot default, **configurable di Admin**, §17-C):

| Komponen | Bobot Default | Makna |
|---|---|---|
| Demand | 25% | Kekuatan sinyal kebutuhan/audience/search |
| Momentum | 15% | Kecepatan pertumbuhan topik |
| Competitor Gap | 20% | Rendah/tinggi coverage kompetitor |
| Expected Engagement | 15% | Potensi saves/shares/comments/views berdasar pola historis |
| Own Fit | 15% | Kesesuaian dengan brand, audience, performa member |
| Freshness | 10% | Apakah opportunity masih awal atau sudah jenuh |

Bobot ini **bukan formula final** sebelum cukup data historis terkumpul, dan skor selalu disertai **alasan + confidence level** (lihat §5-J) — tidak pernah ditampilkan sebagai angka tunggal tanpa penjelasan.

**Kartu Opportunity** wajib berisi: judul, "why now", evidence (sumber sinyal), demand score, competition/coverage score, saturation level, angle/format/hook/CTA yang disarankan, skor + confidence, dan tombol aksi **"Buat Ide"**, **"Kirim ke Planner"**, **"Analisis dengan Analyzer"**.

### D. Competitor Tracker — Competitive Intelligence

Bukan sekadar profil kompetitor — dianalisis sebagai strategi kompetitif:

- **Overview Card**: followers + pertumbuhan periode, estimated posting frequency, average engagement (+ quality bila tersedia), top format, top pillar, best publishing window, momentum (naik/stabil/turun), confidence/freshness indicator, dan diagnosis AI satu-baris ("Mengapa akun ini kuat sekarang?").
- **Content Strategy Breakdown**: content mix (edukasi/hiburan/storytelling/community/promosi %), format mix, pillar mix + kontribusi performa, cadence (+ perubahannya), konten serial berulang, evergreen vs topical.
- **Performance Quality** (bukan sekadar likes): pisahkan likes/comments/shares/saves/views/reach/watch-time; hitung rate relevan (save rate, share rate, comment rate, completion rate); **gunakan median selain average** untuk mengurangi distorsi outlier viral; bandingkan terhadap baseline kompetitor, bukan angka absolut; tandai confidence jika metrik dari estimasi provider pihak ketiga.
- **Top Content Explorer**: top 10/20 berdasar beberapa objective (reach/saves/shares/engagement tertinggi), tiap konten dengan thumbnail, tanggal, format, pillar, topic, hook, CTA, performa, plus penjelasan AI "kenapa berhasil" dan "apa yang reusable vs yang tidak boleh ditiru". Tombol **"Jadikan Referensi"** menyimpan pola, bukan menyalin konten.
- **Hook Intelligence**: kelompokkan hook berdasar pola (problem, curiosity, number/list, contrarian, before/after, story, warning, question, promise), tampilkan frequency + median performance + uplift terhadap baseline, deteksi hook yang mulai jenuh, hasilkan 3–5 hook pattern layak diuji.
- **Topic Intelligence**: topic frequency, performance per topic, growth/momentum, saturation antar kompetitor, overlap (berapa kompetitor bahas tema sama), **whitespace** (demand ada, coverage rendah).
- **CTA Intelligence**: klasifikasi CTA berdasar tujuan (save/comment/share/follow/click/purchase-intent), hubungkan ke performance metric relevan, rekomendasi CTA sesuai objective.
- **Competitor Comparison**: bandingkan 3–10 akun sekaligus (growth, engagement quality, cadence, format mix, pillar mix, top topics), diagnosis AI "siapa unggul di mana", deteksi strategi yang dipakai semua kompetitor vs strategi unik satu kompetitor yang layak diuji.

### E. Trend Explorer — Trend Intelligence (bukan sekadar daftar tren)

- **Trend Velocity**: pertumbuhan mentions/posts/views dalam periode tertentu.
- **Trend Lifecycle**: Emerging → Rising → Peak → Declining → Saturated.
- **Niche Relevance**: relevansi ke brand/audience member.
- **Saturation**: seberapa banyak kompetitor sudah memakai tren ini.
- **Trend Fit**: apakah tren bisa diterjemahkan ke format Gambar/Carousel atau Video Pendek.
- **Expected Shelf Life**: estimasi apakah tren hanya cocok 24–72 jam atau bisa evergreen — penting untuk penjadwalan di Planner (lihat §6-S Trend-Aware Scheduling).
- **Actionability**: saran angle konkret, bukan cuma nama tren.
- Filter **"Emerging Only"** agar member tidak hanya melihat tren yang sudah telanjur penuh/jenuh.
- Tombol **"Jadikan Ide Konten"** tetap ada — tren terpilih terkirim sebagai Raw Concept Note ke Analyzer.

### F. Keyword & Hashtag Research — Search Intent, bukan Hashtag Sentris

- Keyword cluster/topic cluster.
- Search intent: informational, problem, comparison, commercial, inspiration, navigational (bila relevan).
- Question keywords & long-tail opportunities.
- Keyword trend/momentum, SERP/content competition (jika provider mendukung).
- Mapping keyword → audience pain point → content angle → saran konten.
- **Hashtag bukan output utama** — hashtag jadi metadata pendukung setelah topic, intent, dan angle ditentukan, bukan pusat riset.

### G. Audience Insight — Demand Intelligence (bukan sekadar demografi)

| Area | Output |
|---|---|
| Profile | Demografi, lokasi, interest (bila data tersedia) |
| Pain Points | Masalah yang sering disebut/ditanyakan |
| Questions | Pertanyaan berulang → jadi content queue |
| Desire | Hasil yang ingin dicapai audiens |
| Objection | Keraguan/alasan tidak mencoba |
| Sentiment | Positif/netral/negatif + tema penyebab |
| Language | Istilah/slang yang dipakai audiens |
| Content Demand | Topik yang diminta tapi belum cukup dilayani |
| Active Time | Waktu aktif (jika data akun sendiri tersedia) |

Bisa diisi manual oleh member di awal, dan **otomatis diperkaya seiring waktu** dari data Modul Analytics (§13).

### H. Own Content Intelligence — Wajib untuk Closed Loop

Insight dari konten milik member sendiri **otomatis muncul di Riset**, tidak menunggu member membuka Analytics secara manual:

- Top performing topics, formats, hooks, CTAs.
- Best posting windows, save/share drivers.
- Weak patterns to avoid, content fatigue (topik yang terlalu sering dibuat).
- Historical baseline per jenis konten, A/B learnings (bila tersedia).

Gunakan label **"Proven on Your Account"** agar jelas rekomendasi ini berasal dari data milik member sendiri, bukan dari kompetitor — membedakan kepercayaan sumbernya.

### I. Mood Board / Visual Reference Board
- Kanvas pin-board sederhana: member mengunggah/menyimpan referensi visual (gaya foto, palet warna, layout) sebagai acuan sebelum masuk produksi — **referensi, bukan sumber keputusan konten utama**.
- Terhubung ke **Brand Kit** di Canvas Editor (§10) — referensi yang disimpan di sini bisa langsung dijadikan preset warna/gaya.

### J. Research Evidence & Confidence (Prinsip Lintas-Fitur)

Karena platform memakai kombinasi API resmi dan provider pihak ketiga (§5-L), **setiap insight/skor di seluruh Modul 0 wajib menampilkan**:
- **Confidence**: High / Medium / Low.
- **Data Freshness**: "updated X hours/days ago".
- **Evidence Count**: jumlah post/video/comment/keyword yang dianalisis.
- **Source Type**: official API / third-party provider / member data / AI inference.
- **Estimated vs Observed**: bedakan angka observasi dari estimasi.
- **Jika data tidak cukup** → tampilkan "insufficient data" secara jujur, **AI dilarang mengarang angka** untuk mengisi kekosongan (prinsip anti-halusinasi yang sama seperti di Analyzer, §7-B).

### K. Output: Research Digest — Executive Brief Mingguan

Bukan sekadar ringkasan, tapi brief keputusan berkala:

| Bagian | Isi |
|---|---|
| What Changed | Perubahan terbesar minggu ini di niche/competitor/audience |
| What is Working | Pola konten terbukti dari kompetitor + akun sendiri |
| What is Emerging | Topik/tren yang mulai naik |
| What is Saturated | Topik/format yang sudah terlalu ramai |
| Audience Asks | Pertanyaan/pain point utama |
| Opportunities | 3–5 peluang prioritas |
| Recommended Plan | Rasio/jenis konten disarankan untuk periode berikutnya |
| Actions | Tombol langsung ke Planner/Analyzer |

Sertakan label evidence bila tersedia (misal "berdasarkan 47 posting, 3 kompetitor, dan 2.840 komentar teranalisis") untuk meningkatkan trust terhadap rekomendasi AI. Tombol **"Terapkan ke Planner"** tetap mengisi otomatis Card Content Pillar & mengirim opportunity ke Idea Pool (§6-E) di Modul 1.

### L. Kebutuhan Data & Sumber API

| Fitur | Sumber Data | Catatan |
|---|---|---|
| Competitor Tracker — YouTube | **YouTube Data API v3** (resmi, gratis dengan kuota harian) | Endpoint `channels.list` & `videos.list` bisa ambil data publik channel/video siapa saja tanpa perlu izin pemilik akun. Kuota default ±10.000 unit/hari per project — admin perlu siapkan **beberapa API key & rotasi**. |
| Competitor Tracker — Instagram & TikTok | Provider data pihak ketiga (Social Blade API, Phyllo, dll) | API resmi IG & TikTok hanya mengizinkan akses akun sendiri, tidak bisa untuk mengintip akun kompetitor. Wajib pakai provider eksternal. |
| Trend Explorer — YouTube | **YouTube Data API v3** (`videos.list`, `chart=mostPopular`) | Resmi & gratis untuk video trending per region/kategori. |
| Trend Explorer — Google/umum | Google Trends (unofficial/API pihak ketiga) | Tidak ada API resmi publik dari Google untuk Trends. |
| Trend Explorer — TikTok | Provider data pihak ketiga khusus tren TikTok | Tidak ada API resmi publik untuk trending sound/hashtag TikTok. |
| Keyword & Hashtag Research | Google Ads Keyword Planner API / provider SEO pihak ketiga | Untuk estimasi volume pencarian & kompetisi kata kunci. |
| Audience Insight | **YouTube Analytics API** & **Instagram Graph API (Insights)** — resmi, hanya akun sendiri | Sinkron dengan Modul Analytics (§13). |
| Mood Board | Opsional: Unsplash API / Pexels API (resmi, gratis) | Untuk cari referensi visual stok. |

**Implikasi Arsitektur**: Modul Riset membutuhkan **pool provider tersendiri**, terpisah dari 4 pool AI generatif — lihat **§17-B (Research & Data Provider Pool)**.

### M. Arsitektur Data yang Disarankan

Entitas riset agar semua tab saling terhubung tanpa data terpisah-pisah (referensi untuk tim backend saat membangun core):

`Research Workspace` (terikat akun/niche/brand) → `Competitor` (punya snapshot berkala) → `Content Observation` (post/video yang diamati) → `Topic` (dipakai lintas competitor/trend/keyword/audience/own content) → `Hook Pattern` & `CTA Pattern` (terhubung ke Content Observation) → `Trend Signal` (terhubung ke Topic/Keyword) → `Audience Signal` (question/pain point/desire/objection/sentiment) → `Opportunity` (menggabungkan beberapa evidence) → `Research Insight` (kesimpulan AI + evidence + confidence) → `Research Digest` (snapshot berkala dari insights/opportunities).

**Snapshot & Historical Comparison**: Competitor dan Trend tidak hanya disimpan sebagai kondisi terbaru — simpan snapshot berkala (follower/growth, content mix, top topic, posting cadence, trend score history, opportunity score history, digest history) agar sistem bisa menjawab "apa yang berubah?" — contoh insight yang dihasilkan: *"Dalam 4 minggu, kompetitor X menaikkan porsi carousel dari 22% menjadi 47%, dan median save rate carousel mereka meningkat 31%."*

**Cross-Research Correlation Engine** (lapisan yang menggabungkan tab, bukan tab baru) — beberapa contoh kombinasi sinyal yang bernilai:
- Trend + Competitor → tren naik tapi baru sedikit kompetitor pakai = early opportunity.
- Audience + Keyword → pertanyaan audiens cocok keyword problem = high-intent opportunity.
- Competitor + Own Content → kompetitor kuat di carousel, akun sendiri juga save rate tinggi di carousel = layak diuji.
- Keyword + Competitor → demand tinggi, coverage kompetitor rendah = content gap.
- Audience + Own Content → audience minta topik yang belum pernah dibuat = ide baru.

### N. Format Rekomendasi Konten (Standar Konsisten)

Setiap rekomendasi konten dari Modul 0 (Opportunity Card, Research Digest, dsb) memakai struktur konsisten: **What** (topik/konsep) — **Why Now** (alasan timing) — **For Whom** (audience segment) — **Angle** — **Hook** (pola direkomendasikan) — **Format** (Single/Carousel/Video Pendek) — **CTA** (objective-specific) — **Evidence** — **Opportunity Score + Confidence** — **Action** (Buat di Planner / Kirim ke Analyzer / Simpan).

### O. Integrasi ke Planner & Analyzer

| Aksi | Metadata yang Dikirim |
|---|---|
| Kirim ke Planner | Topik, pillar, format, prioritas, suggested timing, opportunity score |
| Kirim ke Analyzer | Raw concept + evidence + target audience + angle + hook direction |
| Simpan sebagai Insight | Evidence snapshot + timestamp + confidence |
| Buat Ide | Generate 3–5 concept variations tanpa langsung membuat aset AI |

### P. Prioritas Implementasi

| Prioritas | Fitur | Nilai |
|---|---|---|
| P0 | Research Overview + Opportunity Engine | Mengubah riset jadi keputusan |
| P0 | Competitor deep analysis | Memperkuat fondasi yang sudah ada |
| P0 | Own Content Intelligence | Mengaktifkan closed loop dengan Analytics |
| P0 | Content Gap | Diferensiasi utama produk |
| P1 | Trend lifecycle + velocity | Menghindari sekadar daftar tren |
| P1 | Audience questions/pain points | Mengubah audience jadi sumber ide |
| P1 | Hook + CTA Intelligence | Langsung berguna untuk Modul Skrip |
| P1 | Keyword intent + clusters | Menghubungkan demand ke ide konten |
| P2 | Historical snapshots | Competitive intelligence lebih matang |
| P2 | Cross-research correlation | Meningkatkan kualitas Opportunity Score |

### Q. Batasan (Yang Tidak Boleh Dilakukan)
- Jangan membuat tab baru hanya untuk menambah banyak data tanpa insight.
- Jangan menjadikan follower count sebagai indikator utama kualitas kompetitor.
- Jangan hanya pakai average — gunakan median & baseline.
- Jangan menyebut estimasi provider pihak ketiga sebagai data pasti.
- Jangan memberi "viral prediction" tanpa confidence/evidence.
- Jangan menjadikan hashtag sebagai pusat riset.
- Jangan memberi rekomendasi tanpa menjelaskan "why".
- Jangan menyarankan copy competitor secara literal — ekstrak pattern/strategy.
- Jangan menghilangkan tombol reset/persistensi yang sudah ditetapkan (§2-C).
- Jangan membuat rekomendasi yang terisolasi dari Planner, Analyzer, dan Analytics.

### R. Definition of Done

Modul 0 dianggap matang jika member bisa menyelesaikan alur berikut tanpa berpindah ke tool riset eksternal: menentukan niche/brand/audience → menambahkan 3–10 kompetitor → melihat pola strategi kompetitor → melihat tren relevan & lifecycle-nya → menemukan keyword + intent → melihat audience demand → melihat apa yang terbukti bekerja di akun sendiri → menemukan 3–5 content gap → mendapat Opportunity Score + evidence + confidence → memilih opportunity → mengirim langsung ke Planner atau Analyzer → setelah konten berjalan, Analytics memperbarui insight berikutnya.

---

## 6. Modul 1 — Content Planner & Calendar Engine (Content Planning & Scheduling Engine)

**Fungsi**: pusat kendali strategi konten sebelum produksi — bukan sekadar kalender UI + slot generator, melainkan **constraint-based planning engine** yang menjawab bukan cuma "kapan diposting" tapi "konten apa, pillar apa, format apa, kapan waktu paling masuk akal, apakah kalender seimbang, apakah beban produksi wajar, dan apa yang perlu diperbaiki". Bisa diisi manual, atau sebagian otomatis-terisi dari Research Digest (§5-K). **Tidak ada pemilihan platform di sini** — hanya jenis konten yang direncanakan.

### A. Prinsip Desain

| Prinsip | Penjelasan |
|---|---|
| Strategy First | Kalender dibangun dari strategi, bukan sekadar mengisi tanggal kosong |
| Member Control | AI menyarankan; member menyetujui, mengubah, mengunci, atau menolak |
| Constraint-Based | Hard constraint tidak boleh dilanggar; soft constraint dioptimalkan |
| Platform-Agnostic | Planner tidak memilih Instagram/TikTok/YouTube; hanya jenis konten |
| Diversity | Engine menghindari pengulangan pillar, format, topic, dan pola konten yang tidak perlu |
| Evidence-Based | Rekomendasi memanfaatkan Riset (§5) dan Analytics (§13) jika data tersedia |
| Production-Aware | Kalender mempertimbangkan beban produksi, bukan hanya beban posting |
| Explainable | Setiap rekomendasi AI disertai alasan singkat |
| Persisten | Perubahan kalender tidak hilang karena refresh/pindah halaman/logout (§2-C) |
| Reversible | Perubahan massal dapat di-undo atau dikembalikan lewat version history |
| Incremental | Member bisa ubah sebagian kalender tanpa regenerate seluruh periode |

### B. Struktur Modul

| Area | Isi |
|---|---|
| Planning Configuration | Period, Timezone, Active Days, Posting Frequency, Content Types |
| Strategy Configuration | Content Pillar & Content Type Allocation, Brand Profile, Audience, Diversity Preference |
| Idea & Opportunity Layer | Idea Pool, Research Opportunities, Analytics Recommendations |
| Calendar Engine | Auto Generate, Rebalance, Regenerate, Conflict Detection, Health Score |
| Interactive Calendar | Monthly/Weekly/Grid, Drag & Drop, Bulk Actions, Lock/Unlock |

### C. Planning Configuration

- **Period**: input Start Date & End Date, **Duration dihitung otomatis** oleh sistem (bukan input manual terpisah). Jika periode diperpanjang/dipersingkat setelah slot terbentuk, sistem **wajib konfirmasi** dulu sebelum mengubah data (misal menawarkan generate slot baru untuk hari tambahan, atau memilih pindah/arsipkan slot yang jatuh di luar periode baru) — **tidak pernah menghapus data tanpa persetujuan**.
- **Timezone**: eksplisit dipilih member, dipakai konsisten di seluruh perhitungan jadwal.
- **Active Days**: toggle hari aktif (Senin–Minggu). Frekuensi dihitung dari **active days**, bukan jumlah hari kalender — contoh: 7 hari kalender dengan hanya 5 hari aktif dan frekuensi 2x/hari menghasilkan `5 × 2 = 10 slot`, bukan 14.
- **Posting Frequency**: pilihan 1x/2x/3x per hari (arsitektur disiapkan untuk custom frequency di fase berikutnya). Total slot dihitung `Active Days × Daily Frequency`, dapat berkurang oleh locked/existing constraint, excluded dates, atau campaign constraint — sistem selalu menampilkan **"Planned Slots: N"** sebelum member menekan Generate.

### D. Strategy Configuration

**Content Type Allocation**: saat member memilih lebih dari satu jenis konten, sistem meminta/merekomendasikan distribusi persentase (misal Single Post 20% / Carousel 30% / Video Pendek 50%). Dua mode: **Recommended Mix** (AI mengusulkan berdasar Research Insight, Own Content Intelligence §5-H, content objective, audience, histori performa) atau **Manual Mix** (member tentukan sendiri, total wajib 100%).

**Content Pillar Allocation Engine**: slider pillar yang sudah ada (§6 versi awal) dinaikkan jadi **actual allocation engine** — bukan sekadar visual. Pillar allocation adalah **target**, bukan alasan membuat kalender terlihat buruk secara editorial (lihat aturan anti-repetisi, §6-J).

**Aturan Alokasi Integer** (berlaku untuk Content Type maupun Content Pillar Allocation): gunakan distribusi integer dengan pendekatan **largest remainder** (atau algoritma ekuivalen) — total slot harus selalu bulat dan konsisten dengan jumlah slot sebenarnya. Contoh: 7 slot dengan distribusi 40/30/30 menghasilkan 3/2/2 slot, **bukan** 2.8/2.1/2.1.

**Brand Profile & Audience**: tone of voice (Casual, Profesional, Gen-Z, B2B, dll), deskripsi target audiens — tetap seperti spesifikasi awal.

**Diversity Preference**: pengaturan preferensi tingkat variasi pillar/format/topik yang diinginkan member, dipakai sebagai input ke Anti-Repetition Engine (§6-J).

### E. Idea & Opportunity Layer (Idea Pool)

Area terpisah yang memisahkan **"ide yang tersedia"** dari **"ide yang sudah dijadwalkan"**. Idea Pool bisa berisi: Research Opportunity, Trend, Keyword opportunity, Audience question, Saved idea, Manual idea, atau Analytics recommendation. Tiap idea minimal punya: Title, Description, Pillar, Suggested Content Type, Priority, Source, Status (**Available / Planned / Used / Archived**).

**Integrasi Riset → Planner**: Opportunity dari Modul 0 (§5-C, §5-O) membawa metadata lengkap — topic, recommended pillar, recommended format, priority, suggested timing, opportunity score, confidence, recommended angle, evidence reference. Opportunity masuk ke Idea Pool, atau langsung ke slot kalender jika member memilih **"Kirim ke Planner & Schedule"**.

**Integrasi Analytics → Planner**: memakai prinsip **"Proven on Your Account"** (§5-H) — jika Analytics menemukan pola nyata (misal video punya completion rate tinggi, carousel save rate tinggi, konten promosi sering underperform), Planner memberi rekomendasi eksplisit (misal "Berdasarkan performa akun Anda, kami menyarankan Video naik dari 40% menjadi 55%"). Member memilih **Apply Recommendation**, **Review Changes**, atau **Keep Current Strategy** — **strategi tidak pernah diubah diam-diam**.

### F. Auto Calendar Generation & Planning Pipeline

Tombol utama **"Generate Content Plan"**. Sebelum eksekusi, sistem menampilkan ringkasan pra-generate: periode, active days, frekuensi, total slot, distribusi pillar & jenis konten, jumlah idea tersedia, jumlah research opportunity tersedia — sehingga member tahu persis apa yang akan dihasilkan sebelum menekan Generate.

**Planning Engine Pipeline** (urutan pemrosesan): Planning Configuration → Strategy Configuration → Research Opportunities → Analytics Insights → Idea Pool → Candidate Slot Generation → Hard Constraints → Pillar Allocation → Content Type Allocation → Time Scheduling → Diversity Optimization → Production Workload Check → Calendar Scoring → Calendar Health Check → Generated Calendar → Member Review → Lock/Edit/Approve.

### G. Hard Constraints vs Soft Constraints

**Hard Constraints** (tidak boleh dilanggar): slot harus dalam planning period; slot hanya di active day; daily frequency tidak boleh terlampaui; content type & pillar harus dari pilihan/aktif member; tidak boleh dua slot di timestamp sama; **locked slot tidak boleh dipindahkan oleh auto-planner**.

**Soft Constraints** (dioptimalkan, boleh trade-off): pillar diversity, format diversity, topic diversity, time optimization, research relevance, analytics fit, production workload, promotional spacing, trend freshness.

### H. Scheduling Engine & Content Slot

Setiap slot kalender direpresentasikan sebagai **Content Slot** dengan `content_slot_id` sebagai **identitas utama** yang menghubungkan seluruh lifecycle konten lintas modul:

```
Content Slot
   ├── Source (Analyzer)
   ├── Angle
   ├── Script (Script Review)
   ├── Assets (Asset Preparation)
   ├── Editor Project
   └── Export
```

Field minimal per slot: `content_slot_id`, `content_plan_id`, `date`, `scheduled_time`, `content_type`, `content_pillar`, `topic`, `title`, `priority`, `status`, `source_id`, `angle_id`, `script_id`, `asset_project_id`, `editor_project_id`, `is_auto_generated`, `is_locked`. Tujuannya agar member tidak merasa "membuat proyek baru" tiap kali berpindah dari Planner ke Analyzer ke Editor — semua tetap merujuk ke satu `content_slot_id` yang sama.

### I. Smart Time Recommendation & Time Slot Intelligence

Tiga mode penentuan jam tayang:
- **AI Recommended**: jika data Analytics tersedia, pakai best performing window milik member (disesuaikan content type/pillar bila data cukup); jika belum tersedia, pakai benchmark umum dan **ditandai sebagai rekomendasi, bukan fakta pasti**.
- **Fixed Schedule**: member menentukan jam tetap (misal 09:00/13:00/19:00).
- **Custom**: member menetapkan waktu berbeda per hari.

**Time Slot Intelligence**: jika 2–3 slot/hari dipakai, engine menentukan urutan berdasar content type, pillar, audience behavior, histori performa, dan kesiapan produksi — bukan sekadar urutan database.

### J. Anti-Repetition & Diversity Engine

Kalender yang valid secara matematis (memenuhi persentase pillar) belum tentu bagus secara editorial. Aturan diversity mencegah: pillar sama berturut-turut (`Edukasi → Edukasi → Edukasi`), content type sama berturut-turut (`Video → Video → Video → Video`, kecuali memang strategi yang dipilih), topik sama terlalu berdekatan, promotional clustering (`Promosi → Promosi → Promosi` tanpa alasan campaign), serta pola hook/angle yang identik terlalu sering (jika metadata tersedia).

Secara internal, engine memberi **repetition penalty** ke kandidat slot untuk pola-pola di atas — mekanisme ini **tidak perlu ditampilkan sebagai formula teknis** ke member; cukup pesan sederhana seperti *"Calendar adjusted to improve content variety."*

### K. Calendar Scoring & Calendar Health Score

Tiap kalender hasil generate punya skor internal per dimensi (Pillar Balance, Format Diversity, Topic Diversity, Time Optimization, Research Fit, Analytics Fit, Production Load) plus **Overall Score**. Skor ini bersifat **decision support**, **bukan klaim prediksi viral** — sejalan dengan prinsip Riset (§5-Q).

**Calendar Health Score** ditampilkan sebagai kartu (misal "Calendar Health — 88/100") dengan breakdown yang sama, disertai warning konkret bila relevan (misal "⚠️ 3 video edukasi dijadwalkan berturut-turut") dan tombol **"Fix Automatically"**.

### L. Production Workload Awareness

Planner tidak boleh hanya optimal dari sisi jadwal posting — beban produksi nyata juga dihitung. Level: Low / Medium / High / Critical, dihitung dari content type & status produksi tiap slot. Contoh peringatan: *"8 konten video dijadwalkan dalam 3 hari dan sebagian besar belum mulai produksi"* dengan rekomendasi *"sebar konten berat produksi ke hari tambahan"*.

### M. Interactive Calendar

Tiga view (fokus berbeda tiap view): **Monthly** (density keseluruhan, distribusi pillar, campaign, workload, status), **Weekly** (eksekusi, waktu, urutan harian, drag & drop), **Grid** (bulk management, perbandingan, filtering, sorting).

**Calendar Card** minimal menampilkan: Date, Time, Content Type, Title/Topic, Pillar, Priority, Content Status, Production Indicator, Lock Indicator.

**Menu Aksi Card**: Open Content, Add Source, Change Date/Time/Pillar/Content Type, Duplicate, Lock/Unlock Slot, Regenerate, Move, Delete — aksi menyesuaikan status konten saat itu.

### N. Drag & Drop, Conflict Detection, Locked Slot & Manual Override Priority

**Drag & Drop Rescheduling**: saat slot dipindah, sistem memvalidasi berurutan — active day, daily frequency, timestamp conflict, locked slot — lalu menghitung ulang distribusi pillar, diversity, dan workload. Jika konflik ditemukan, tampilkan pilihan **Move Anyway / Rebalance Calendar / Cancel**.

**Jenis Conflict yang Diperiksa**: Schedule Conflict (dua slot waktu sama), Frequency Conflict (melebihi daily frequency), Active Day Conflict (jatuh di hari nonaktif), Locked Conflict (auto-planner coba pindahkan locked slot), Pillar Conflict (distribusi terlalu jauh dari target), Format Conflict (terlalu repetitif), Production Conflict (beban produksi terlalu tinggi), Trend Freshness Conflict (opportunity/tren sudah lewat shelf life yang direkomendasikan).

**Locked Slot**: field `is_locked`. Slot terkunci adalah keputusan manual member dan **tidak boleh dipindahkan oleh auto-planner** — saat regenerate, locked slot tetap, engine hanya mengoptimalkan slot lain.

**Manual Override Priority** (urutan otoritas keputusan, dari tertinggi): Locked Manual Decision → Manual Unlocked Decision → Hard Constraint → Approved Strategy → AI Recommendation → Default Heuristic. AI tidak pernah mengalahkan keputusan eksplisit member.

### O. Regenerate Selected / Regenerate From Date / Rebalance Calendar

- **Regenerate Selected**: member mencentang slot tertentu (bukan cuma "regenerate semua"), hanya slot terpilih yang diganti.
- **Regenerate From Date**: member pilih titik mulai (misal "regenerate dari 1 September"), semua slot sebelum tanggal itu — termasuk locked slot di dalam rentang regenerasi — tetap dipertahankan.
- **Rebalance Calendar**: dipicu saat perubahan manual membuat alokasi tidak seimbang (misal target Edukasi 40% tapi kondisi saat ini 55%). Sistem menawarkan **"Rebalance Automatically"**, hanya mengubah slot yang **tidak locked**.

### P. Bulk Actions, Duplicate Content, Partial Update & Change Preview

**Bulk Actions**: Select Multiple/Select All Visible, lalu Change Pillar/Content Type/Date/Time, Lock/Unlock, Regenerate, Duplicate, Delete — penting untuk kalender periode panjang.

**Duplicate Content**: menghasilkan **content plan baru**, bukan copy identik (misal duplikat dari Carousel bisa dialihkan jadi Video Pendek dengan judul sama) — source/angle boleh direferensikan kembali, tapi script & production flow tetap entitas tersendiri. Ini konsisten dengan prinsip §7 bahwa satu angle bisa diproses untuk Gambar dan Video sekaligus.

**Partial Calendar Update**: perubahan kecil (misal ubah target pillar dari 40%→50%) **tidak memaksa regenerate seluruh kalender** — sistem menawarkan preview jumlah slot unlocked yang perlu berubah, dengan pilihan Review Changes / Apply / Cancel.

**Calendar Change Preview**: sebelum perubahan massal diterapkan, tampilkan diff eksplisit (slot mana berubah dari apa ke apa) dengan tombol Apply Changes / Cancel — mencegah kesan AI mengambil keputusan yang tidak terkontrol.

### Q. Explainable Planning & Undo / Version History

**Explainable Planning**: setiap rekomendasi signifikan wajib disertai alasan singkat — misal *"Video performs better on your account during evening windows, and this slot helps balance the weekly format mix"* atau *"Recommended because this topic has high demand and low competitor coverage"*. Tidak ada output tanpa alasan.

**Undo & Version History**: perubahan penting dicatat sebagai log (misal "Calendar Generated" → "3 slots moved" → "Pillar allocation changed" → "Calendar Rebalanced"), minimal tersedia **Undo Last Change**, idealnya juga **View Version History**. Tiap generate besar dapat menghasilkan version (Plan v1, v2, v3) yang bisa dibandingkan, di-restore, atau di-rename — version lama tidak dihapus otomatis selama masih relevan untuk audit/undo.

### R. Status Integration

PRD sudah menetapkan state machine resmi (§18): `Draft → Source Needed → Script Ready → Asset Ready → Editing → Exported`. Planner **menampilkan** state ini di Calendar Card, **tidak menggantinya**. Jika diperlukan metadata tambahan khusus level perencanaan (misal `calendar_plan_status`: Draft Plan / Ready), ini terpisah dari `content_status` yang tetap mengikuti state machine resmi §18.

### S. Trend-Aware Scheduling & Priority System

**Trend-Aware Scheduling**: jika opportunity berasal dari Trend Explorer (§5-E) dengan metadata lifecycle/velocity/expected shelf life, Planner memprioritaskan penempatan tanggal sesuai jendela relevansinya — tren dengan shelf life 72 jam tidak dijadwalkan 3 minggu kemudian.

**Priority System**: tiap planned content bisa berlevel High/Medium/Low. Saat slot terbatas dibanding jumlah opportunity, opportunity prioritas tinggi dijadwalkan lebih dulu — priority bukan berarti otomatis locked.

### T. Filter, Search, Summary & Planning Recommendations

**Filter**: Date, Content Type, Pillar, Status, Priority, Locked/Unlocked, Production Load, Research Source, Opportunity Score.

**Search Global**: pencarian topic/title/source/pillar/content type.

**Calendar Summary** (tampil di atas kalender, update real-time): total hari & slot, breakdown per pillar, breakdown per content type, breakdown per status konten (§18).

**Panel AI Planning Recommendations**: daftar saran aktif (misal "Naikkan Video 10% berdasarkan performa sendiri", "Renggangkan 2 post promosi", "Pakai tren emerging dalam 72 jam", "Kurangi beban produksi tanggal 3 September"), tiap saran disertai reason, affected slots, expected benefit, dan action button.

### U. Data Model yang Disarankan

Entitas minimal untuk tim backend (pelengkap `content_slot_id` di §6-H): `content_plans` (id, workspace_id, name, start_date, end_date, timezone, frequency, status, timestamps), `content_slots` (field lengkap di §6-H), `content_ideas` (id, content_plan_id, title, description, pillar, suggested_type, priority, source, opportunity_score, confidence, status), `content_pillar_allocations`, `content_type_allocations`, `schedule_preferences`, `calendar_constraints`, `calendar_versions` (id, content_plan_id, version_number, snapshot, created_by, created_at), `calendar_change_logs`.

### V. Persistence & Autosave

Mengikuti prinsip persistensi platform (§2-C) — autosave minimal terjadi pada: perubahan konfigurasi, perpindahan slot, edit slot, locking, perubahan pillar/content type/jadwal. Saat browser refresh, kalender **tetap berada di kondisi terakhir tersimpan** — tidak boleh hanya mengandalkan local state browser.

### W. Batasan (Yang Tidak Boleh Dilakukan)
- Jangan memilih platform sosial media.
- Jangan mengubah locked slot.
- Jangan menghapus konten tanpa konfirmasi.
- Jangan menganggap rekomendasi AI sebagai fakta.
- Jangan menjanjikan performa/viral.
- Jangan mengisi topic hanya demi kalender terlihat penuh.
- Jangan memaksakan pillar ratio jika membuat kualitas kalender buruk tanpa memberi warning.
- Jangan mengubah seluruh kalender hanya karena satu slot diedit.
- Jangan menghilangkan hasil manual member saat regenerate.
- Jangan menyembunyikan alasan perubahan.
- Jangan menyamakan frequency dengan jumlah hari kalender jika active days dipakai.

### X. Prioritas Implementasi

| Prioritas | Fitur |
|---|---|
| P0 (wajib) | Planning period, active days, frequency, content type selection, pillar allocation, integer allocation, auto calendar generation, Monthly/Weekly/Grid, drag & drop, manual edit, lock/unlock, content status, content slot ID, conflict detection, regenerate selected, rebalance, autosave/persistence, basic anti-repetition |
| P1 (sangat disarankan) | Idea Pool, Research Opportunity integration, Analytics recommendation, smart posting time, Calendar Health Score, production workload, bulk actions, duplicate, calendar change preview, undo, basic version history, trend-aware scheduling |
| P2 (advanced) | Advanced scoring engine, predictive scheduling, scenario planning, multiple calendar variants, advanced performance-based optimization, automatic strategy optimization, long-term content fatigue modeling |

### Y. Acceptance Criteria & Definition of Done

**Modul dianggap matang** jika member bisa menyelesaikan alur: menentukan periode → active days → frequency → jenis konten → content pillar → melihat jumlah slot yang akan dibuat → mengimpor opportunity dari Riset → melihat rekomendasi Analytics (bila tersedia) → generate kalender otomatis → mendapat distribusi pillar & format sesuai target → mendapat jadwal yang tidak repetitif → melihat warning/conflict → melihat Calendar Health → mengubah slot manual (termasuk drag & drop) → mengunci slot tertentu → regenerate slot tertentu tanpa mengubah yang lain → rebalance tanpa mengubah locked slot → melihat status produksi tiap konten → memakai Idea Pool → membuka Analyzer dari slot terkait → mempertahankan kalender setelah refresh/logout → menggunakan undo/restore.

**Alur Definition of Done**: Menentukan strategi → Mendapat ide/opportunity → Generate calendar → Review strategy → Melihat health & warning → Mengubah sebagian slot → Lock keputusan penting → Mengirim slot ke Analyzer → Memantau status produksi → Rebalance bila perlu → Memakai Analytics untuk planning berikutnya. Target akhir: member membuka Content Planner dan dalam beberapa menit mendapat kalender yang **strategis, seimbang, realistis untuk diproduksi, dapat dijelaskan AI, mudah diedit, dan tetap sepenuhnya di bawah kontrol member**.
## 7. Modul 2 — AI Multi-Source Analyzer

**Fungsi**: mengubah sumber mentah menjadi **evidence, claim, insight, opportunity, dan angle konten tervalidasi** yang siap diproduksi — bukan sekadar summarizer. Analyzer diposisikan sebagai **Content Intelligence Layer**: lapisan yang memastikan sumber dibaca dengan benar, evidence-nya jelas, dan angle yang dihasilkan bisa dipertanggungjawabkan sebelum masuk ke Script Review. **Hasil analisa tersimpan otomatis dan persisten** — lihat prinsip di §2-C. Modul ini punya tombol **Reset Analyzer** sendiri untuk mengosongkan hasil dan memulai analisa baru.

**Prinsip monetisasi modul ini**: mengikuti §14-A — **akses dasar Analyzer tetap unlimited & gratis di semua tier** (tidak memotong kuota gambar/video). Yang membedakan tier/berbayar bukan "bisa dipakai atau tidak", melainkan **kedalaman analisa**. Seluruh fitur di bawah ini dibagi tegas menjadi dua kelas:

- **Default (wajib, termasuk semua tier)** — lapisan minimum yang membuat Analyzer layak disebut "intelligence engine"; tanpa ini hasil analisa berisiko dangkal atau berhalusinasi.
- **Add-on (opsional, tersedia melalui Product Package/Entitlement, atau termasuk dalam membership/role sesuai pengaturan admin §17)** — lapisan pendalaman yang memperkuat analisa tapi tidak wajib untuk alur dasar tetap berjalan.

Pembagian ini mengikuti pola yang sudah ada pada Add-on Multi-AI (§7-H), sehingga member cukup mengenal **satu mekanisme upgrade** untuk seluruh modul: pilih/purchased add-on package → entitlement diberikan → hasil bertambah dalam.

### Input (Tabbed Card, 3 opsi)
1. **URL Extractor** — input link artikel, blog, atau video rujukan. Mendukung multi-URL (2+ sumber sekaligus) sebagai bagian dari Add-on Cross-Source Analysis (§7-I).
2. **Media Uploader** — drag-and-drop file: Video (MP4/MOV), Audio (MP3/WAV), Dokumen (PDF/DOCX), Gambar (PNG/JPG/WEBP).
3. **Raw Concept Note** — textarea bebas untuk ide mentah / poin acak.

### A. Pipeline Analisis (Overview)

```
[Default] Ingest → Identify & Classify Source → Quality Gate Dasar → Structured Extraction
     ↓
[Default] Fact/Opinion Split → Evidence Reference Ringan → Angle Generation → Virality Potential Score
     ↓
[Add-on: Deep Source Intelligence] Source Quality Score penuh → Claim Registry & Risk → Content Richness →
     Novelty/Tension → Emotional Intelligence detail → Content Value & Transformation Score →
     Angle Qualification Gate → Content Readiness Gate
     ↓
[Add-on: Media Intelligence] Visual/Audio/Quote Intelligence (khusus sumber video/audio/gambar)
     ↓
[Add-on: Cross-Source Analysis] Perbandingan antar 2+ sumber sekaligus
     ↓
[Add-on: Multi-AI] Cross-validation 2–3 model AI di sepanjang pipeline (bukan hanya di angle akhir)
     ↓
SCRIPT REVIEW (Modul 3)
```

Alur ini menjawab bukan cuma "apa isi sumber", tapi apa yang bisa dipercaya, apa yang bernilai buat audiens, apa yang baru, dan apakah sumbernya cukup kuat untuk langsung diproduksi.

### B. Default — Source Identity, Klasifikasi & Quality Gate Dasar
Berlaku otomatis untuk setiap sumber, tanpa biaya tambahan:

- **Source Identity & Provenance**: tipe sumber, URL/file reference, domain, penulis/creator (jika tersedia), publisher, tanggal publikasi, tanggal update, judul, bahasa, source platform, canonical URL bila ada, timestamp ekstraksi. "Siapa yang mengatakan" ikut menentukan bobot kepercayaan sebuah klaim, sama pentingnya dengan "apa yang dikatakan".
- **Klasifikasi Otomatis** ke salah satu tipe: News, Article, Blog, Research/Paper, Report, Document, Video, Podcast/Audio, Interview, Social Post, Thread, Forum, Product Page, Advertisement, User-Generated, Image, Raw Concept, Other. Tipe ini menentukan **profil ekstraksi** yang dipakai (mis. Research Paper memprioritaskan metodologi/sample size/limitations; Video memprioritaskan hook/retention moment/emotional peak; Raw Concept memprioritaskan intent & pertanyaan yang perlu dijawab sebelum produksi).
- **Pre-Analysis Validation (Quality Gate Dasar)** — dijalankan **sebelum** AI berat dipanggil, untuk menghindari biaya sia-sia: URL/file bisa diakses, format didukung, kualitas transkrip/OCR/ekstraksi dokumen memadai, bahasa terdeteksi, durasi wajar, konten tidak terlalu pendek/kosong. Jika gagal → pesan **"Sumber tidak dapat dianalisis dengan andal"**, bukan memaksa AI tetap menghasilkan output.
- **Duplicate & Reuse Detection**: jika URL/file sudah pernah dianalisis sebelumnya, tampilkan ringkasan analisa lama (tanggal, Source Quality, jumlah angle) dengan pilihan **"Gunakan Analisa Sebelumnya"** atau **"Analisa Ulang"**; jika konten sumber berubah sejak terakhir dianalisis, beri catatan **"Konten sumber berubah sejak analisa terakhir"**. Ini menghemat biaya API dan mencegah member membayar dua kali untuk sumber yang sama.

### C. Default — Structured Extraction & Evidence Ringan
Menggantikan pendekatan "ringkasan generik" dengan representasi sumber yang terstruktur:

1. **Ringkasan Analisis Umum** — key takeaways menyeluruh dari sumber (bukan cuma 1–2 kalimat generik).
2. **Core Facts** — siapa/apa/kapan/di mana/mengapa/bagaimana, angka, tanggal, persentase, urutan kejadian.
3. **Key Points / Talking Points** — daftar poin konkret yang bisa langsung dipakai sebagai bahan slide/shot.
4. **Klasifikasi Fact vs Opinion vs Interpretation vs Prediction vs Anecdote/Testimonial** — setiap poin penting ditandai tipenya, supaya opini sumber tidak pernah ditampilkan sebagai fakta di konten akhir.
5. **Tone & Sentiment Sumber Asli** — nada asli sumber (formal/santai/emosional/dst), dicocokkan ke Brand Profile member dari Planner (§6).
6. **Data/Statistik Pendukung** — angka atau fakta kuat yang diekstrak dari sumber, dengan catatan ringan apakah relatif/absolut dan apakah butuh verifikasi.
7. **Evidence Reference Ringan** — tiap fakta/hook penting menyertakan penunjuk lokasi sumber (paragraf/timestamp/halaman), ditampilkan sebagai badge **"Lihat Sumber"** di UI. Ini level dasar dari evidence mapping; detail claim registry penuh ada di Add-on (§7-F).

### D. Default — Angle Generation & Virality Potential Score

Tiap kartu Angle/Tema (bisa lebih dari 1 per sumber) dikelompokkan ke salah satu **Angle Family** yang paling relevan (Educational, How-to, Problem/Solution, Contrarian, Myth vs Reality, Story, Case Study, Data/Stats, List, Comparison, Warning, Prediction, Opinion/Commentary, Before/After, Q&A, Behind the Scenes, Authority/Expert Insight — sistem hanya memakai family yang benar-benar didukung sumber, tidak wajib menghasilkan semua family):

5. Nama & deskripsi angle, serta **alasan diferensiasi** — kenapa angle ini berbeda dari angle lain hasil sumber yang sama.
6. Badge kecocokan non-eksklusif: ✅ Gambar/Carousel dan/atau ✅ Video Pendek.
7. **Target Audience Fit** — disandingkan dengan Audience Insight dari Riset (§5-G).
8. **Opsi Hook/Judul** — jumlah opsi mengikuti kekayaan sumber; **AI dilarang mengarang atau memaksakan variasi tambahan hanya demi menampilkan "banyak pilihan"** — kejujuran hasil analisa lebih penting daripada kuantitas opsi. Setiap hook melewati **Hook Accuracy Check** ringan: apakah melebih-lebihkan, mengubah makna, menyiratkan sebab-akibat yang tidak didukung evidence, atau menghilangkan konteks penting — hook yang gagal cek ini otomatis direvisi AI ke versi yang lebih aman sebelum ditampilkan ke member.
9. **Saran Content Pillar** — mempercepat pengisian kalender Planner.

**Skor Potensi Viral** direname menjadi **Virality Potential Score (0–100)** — tetap elemen paling menonjol di kartu, tetap wajib melalui cross-validation multi-AI bila Add-on Multi-AI aktif (§7-H), tetap diurutkan tertinggi ke terendah secara default. Faktor & bobot default (configurable admin, sama seperti mekanisme Opportunity Score §5-C):

| Faktor | Bobot Default |
|---|---:|
| Kekuatan Hook | 15% |
| Curiosity / Information Gap | 10% |
| Trigger Emosional | 10% |
| Relatability | 10% |
| Share Motivation | 10% |
| Novelty / Freshness | 10% |
| Utility / Value | 10% |
| Tension / Contrarian Potential | 8% |
| Kesesuaian Tren Saat Ini | 7% |
| Kesesuaian Format-Algoritma | 5% |
| Audience Fit | 5% |

Bobot ini **starting heuristic**, bukan ground truth — dikalibrasi ulang setelah data historis Analytics cukup terkumpul (lihat §7-K), sama seperti prinsip bobot Opportunity Score di §5-C.

> **Catatan penting**: Virality Potential Score **tidak pernah berdiri sendiri** sebagai dasar keputusan produksi. Skor ini menjelaskan seberapa besar karakteristik yang mendukung distribusi/engagement — bukan jaminan hasil viral, dan bukan pengganti keputusan editorial member.

### E. Default — Anti-Halusinasi & Batasan AI (Non-Negotiable)

Bagian ini **tidak bisa dimatikan atau di-downgrade** oleh tier atau add-on manapun — berlaku di seluruh level pipeline, termasuk saat Add-on Deep Source Intelligence/Media Intelligence tidak aktif:

**AI boleh**: merangkum, mengklasifikasikan, menghubungkan evidence, memberi interpretasi yang jelas diberi label ("interpretasi AI", bukan fakta sumber), membuat hook kreatif yang tidak mengubah fakta.

**AI tidak boleh**: menambah/mengubah angka yang tidak ada di sumber, membuat sumber/kutipan baru, mengatribusikan quote ke orang yang salah, mengubah opini menjadi fakta, menghapus konteks yang mengubah makna klaim, menyimpulkan sebab-akibat tanpa evidence, membuat "konsensus palsu" antar-AI, membuat headline lebih sensasional daripada evidence tanpa label.

### F. Add-on — Deep Source Intelligence (Berbayar per Analisa)

Paket add-on baru, independen dari Add-on Multi-AI (§7-H), menggunakan entitlement dari paket/add-on yang dibeli atau termasuk dalam membership saat diaktifkan per sumber, atau termasuk default untuk role tertentu bila diatur admin (§17-A). Berisi:

- **Source Quality Score penuh** (0–100), pecahan 6 dimensi: Extraction Quality, Source Completeness, Evidence Strength, Freshness, Source Reliability, Content Richness. Contoh output: *"Source Quality: 82/100 — Cukup kuat untuk dikembangkan jadi konten"* atau *"43/100 — Disarankan verifikasi tambahan"*.
- **Content Richness Score** + estimasi jumlah angle potensial (mis. "Content Richness 88/100 → Potential Angles: 5–7"); jika rendah, sistem menyarankan menambah sumber (§7-I) alih-alih memaksa banyak angle generik.
- **Claim & Evidence Registry penuh** — tiap klaim penting menjadi objek tersendiri (`claim_id`, tipe klaim, lokasi sumber, teks evidence, atribusi, confidence, status verifikasi, konteks waktu), bisa dibuka satu per satu lewat tombol **"Lihat Evidence"** di tiap insight/angle (bukan cuma badge ringan seperti di §7-C).
- **Claim Risk & Verification Layer** — klaim kesehatan/finansial/hukum/ilmiah/"terbukti"/sebab-akibat/superlatif otomatis ditandai `Risk: Low/Medium/High` dan `Verification: Not Needed/Recommended/Required`.
- **Temporal Analysis per klaim** — tiap klaim ditandai `Evergreen / Current / Time-sensitive / Historical / Expired / Unknown`, melengkapi Freshness sumber yang sudah ada di level dasar.
- **Novelty & Saturation Analysis** — menyilangkan insight sumber dengan coverage kompetitor & saturasi tren dari Riset (§5-C, §5-E) untuk menilai seberapa baru/segar sudut pandang ini.
- **Contrarian & Tension Detection** — mendeteksi pola "common belief vs temuan sumber", "ekspektasi vs realita", dsb sebagai bahan hook yang lebih actionable daripada sekadar curiosity gap.
- **Emotional Intelligence Detail** — bukan cuma "Trigger Emosional: Tinggi", tapi emosi spesifik (curiosity, surprise, fear, urgency, joy, inspiration, nostalgia, dst) + pemicu + evidence pendukung + prediksi reaksi audiens.
- **Audience Relevance Mendalam** — pain point, desire, objection, level pengetahuan, dan **Share Motive** (Helpful/Relatable/Identity Signaling/Warning/Entertainment/Status/Debate/Inspiration/Community).
- **2 skor tambahan** melengkapi Virality Potential: **Content Value Score** (seberapa berguna isi sumber) dan **Content Transformation Potential Score** (seberapa mudah sumber diubah jadi konten berkualitas — hookability, visualizability, storytelling potential, dll).
- **Angle Qualification Gate** — tiap angle divalidasi (evidence support, audience fit, novelty, hook strength, claim risk, production feasibility, dst) dan diberi status `Rejected / Weak / Needs Verification / Qualified / Strong Candidate` sebelum ditampilkan ke member.
- **Content Readiness Gate** — status akhir per angle: `Ready / Ready with Verification / Needs More Context / Weak Source`, dihitung dari evidence quality, content value, audience fit, transformation potential, brand fit, dikurangi claim risk & missing context (formula internal, tidak perlu ditampilkan sebagai rumus ke member).

**Kartu Angle dengan Add-on ini aktif** menampilkan keempat skor sekaligus (Source Quality, Content Value, Transformation, Virality Potential) + Confidence + Content Readiness status + alasan ("Why This Angle") + jumlah evidence + warning verifikasi jika ada.

### G. Add-on — Media Intelligence (Video/Audio/Gambar)

Relevan khusus untuk sumber non-teks, menggunakan entitlement analisa yang tersedia:

- **Visual Intelligence**: untuk gambar (objek, orang, komposisi, chart, before/after, elemen visual tak biasa) dan video (scene change, shot type, ekspresi wajah, visual hook, momen visual menonjol) → menghasilkan daftar **Visual Hook Opportunities** yang langsung dipakai di Modul 3 (Script & Visual Prompt).
- **Audio & Speech Intelligence**: speaker identification, perubahan speaker, tempo bicara, penekanan, jeda, frasa yang diulang, pertanyaan retoris, kandidat kutipan, timestamp. Transkrip diperlakukan sebagai **raw evidence**, bukan hasil analisa akhir.
- **Narrative Structure Mapping** (untuk video/podcast/interview/artikel bernarasi): Hook → Context → Problem → Escalation → Discovery → Turning Point → Resolution → Lesson → CTA Opportunity, plus deteksi **Peak Moment** (mis. "Peak Moment #2 — 04:17: pembicara mengungkap fakta yang bertentangan dengan asumsi umum").
- **Quote Intelligence**: tiap kandidat kutipan diberi speaker, timestamp/lokasi, konteks, emosi, kekuatan berdiri sendiri, risiko salah tafsir, dan kategori penggunaan (hook/authority/controversial/emotional/educational/punchline/conclusion).

### H. Add-on Multi-AI — Diperluas ke Seluruh Pipeline, Bukan Hanya Angle Akhir

Melanjutkan mekanisme yang sudah ada — **Proses Analisis Default vs Add-on Multi-AI (Cross-Validation)**:

**Perilaku default (termasuk dalam semua tier, tanpa biaya tambahan)**:
- Sumber dianalisis oleh **1 model AI** (dari Text & Analysis Pool, §17-B).
- Hasil ditampilkan sebagai **1 hasil** — natural, karena hanya ada 1 sumber analisa, tidak ada perbandingan.

**Add-on 1 — Tambah Jumlah AI (Multi-AI Cross-Validation)**:
- Member bisa memilih menganalisis sumber yang sama dengan **2 atau 3 model AI sekaligus** secara paralel (misal GPT-4o + Claude + Gemini), untuk validasi silang yang lebih dalam.
- Fitur **berbayar terpisah**, melalui product/add-on package; setiap penggunaan mengonsumsi entitlement analisa yang tersedia.
- Ekstraksi transkrip (untuk audio/video) tetap dilakukan sekali di awal, dipakai bersama oleh semua model AI yang diaktifkan (tidak diulang per model, menghindari biaya ganda yang tidak perlu).

**Add-on 2 — Tampilkan Semua Hasil (Full Comparison View)**:
- Independen dari Add-on 1. Meski member sudah mengaktifkan 2–3 AI, **tampilan default tetap 1 hasil** (versi terbaik/konsolidasi yang dipilih sistem).
- Untuk melihat **seluruh varian hasil dari tiap AI berdampingan** (bukan cuma 1 konsolidasi), member perlu mengaktifkan add-on ini secara terpisah — juga menggunakan entitlement analisa yang tersedia.
- Add-on ini hanya relevan/aktif jika Add-on 1 (jumlah AI > 1) juga diaktifkan.

**Engine Perbandingan (Comparison Layer)** — berjalan otomatis setiap kali lebih dari 1 AI dipakai, kini **berjenjang** (bukan hanya membandingkan hasil akhir):

1. **Extraction Agreement** — apakah semua AI membaca sumber dengan sama.
2. **Claim Agreement** — apakah AI sepakat soal fakta/klaim.
3. **Interpretation Agreement** — apakah insight yang ditarik sama.
4. **Angle Agreement** — apakah angle yang disarankan sama.
5. **Score Agreement** — apakah penilaian virality sama.

Disagreement di Level 1–2 (soal fakta) diperlakukan **lebih serius** daripada disagreement di Level 4–5 (soal opini/skor) — sistem menurunkan confidence, bukan sekadar voting mayoritas, dan memberi flag verifikasi (mis. *"2/3 AI menganggap klaim valid, ada ambiguitas evidence"*).

- Jika hasil antar-AI **konvergen/serupa** di seluruh level → hasil konsolidasi (versi terbaik/gabungan) ditampilkan sebagai hasil default, badge **"Konsensus [N] AI"**.
- Jika hasil antar-AI **berbeda signifikan** → tetap disimpan semua variannya di backend, tapi **hanya terlihat oleh member jika Add-on 2 aktif** — badge **"Perbedaan Terdeteksi, [N] Varian Tersedia"** ditampilkan sebagai indikator meski varian belum dibuka, agar member tahu ada opsi untuk melihat lebih dalam (dengan tombol upgrade/beli add-on di tempat).

**Pengaturan Default per Role (Admin)**: admin menentukan di §17-A **default jumlah AI** dan **default mode tampilan hasil** untuk tiap role — misal Role "Member Standar" default 1 AI/1 hasil (gratis), Role "Growth" default 2 AI/1 hasil termasuk dalam paket (tanpa biaya tambahan), dst. Admin bebas menentukan kombinasi mana yang gratis-termasuk-tier vs yang harus bayar add-on, per role.

### I. Add-on — Cross-Source Analysis (Multi-Sumber Sekaligus)

Kapabilitas baru: member bisa memasukkan **2 atau lebih sumber sekaligus** ke satu analisa (berbeda dari Add-on Multi-AI di §7-H yang menganalisis 1 sumber dengan banyak model). Add-on ini menggunakan entitlement analisa dari Product/Package yang tersedia:

- **Source Set Table**: tiap sumber dibandingkan (Quality, Freshness, jumlah Evidence, Reliability).
- **Cross-Source Comparison**: agreement, kontradiksi, evidence yang saling melengkapi, informasi duplikat, hierarki kekuatan sumber, peluang sintesis (mis. *"Source A menyatakan X, Source B menunjukkan Y, Source C memberi data Z → peluang gabungkan jadi explainer yang lebih kuat"*).
- **Source Expansion Recommendation**: jika sumber terlalu tipis (Content Richness rendah), sistem menyarankan jenis sumber tambahan (riset primer, statistik resmi, wawancara ahli, contoh kompetitor, komentar audiens) lewat tombol **"Cari Sumber Pendukung"**.

### J. Pemilihan & Lanjutan

- **Pemilihan Multi**: member mencentang satu atau lebih kombinasi (angle × jenis konten) yang ingin diproses lebih lanjut — misal angle "Tips Cepat" dicentang untuk Gambar **dan** Video sekaligus, akan menghasilkan 2 alur skrip terpisah.
- Tombol **"Lanjutkan ke Skrip"** — tiap kombinasi terpilih menjadi satu antrian terpisah menuju Modul 3, membawa **traceability** penuh (Script → Angle → Evidence → Source) agar tim produksi bisa menelusuri balik dasar tiap klaim yang dipakai di skrip.

### K. Closed-Loop Learning (Roadmap Lanjutan, Terhubung Analytics)

Bagian ini **bukan bagian rilis awal**, melainkan kapabilitas lanjutan setelah data performa cukup terkumpul di Analytics (§13):

- **Source Pattern Performance** — tipe sumber mana (news/research/podcast/competitor video/user concept) yang paling sering menghasilkan konten berkinerja tinggi.
- **Angle Pattern Performance** — angle family mana (contrarian/list/story/myth vs reality/data/how-to) yang paling efektif di akun member.
- **Kalibrasi otomatis** bobot Virality Potential Score berdasarkan histori performa akun (bukan bobot generik statis).
- **Source-to-Content Attribution** penuh (Content → Angle → Opportunity → Source → Evidence) untuk audit & pembelajaran platform.

Ini konsisten dengan prinsip §13-E (loop Analytics → Riset/Planner); Analyzer ikut menjadi bagian dari loop tersebut, bukan hanya penerima sekali pakai.

### L. Data Model Tambahan yang Disarankan

Melengkapi entitas Analyzer di backend: `source` (metadata, source_content, extraction_result, source_quality, freshness, content_richness), `source_claim` (claim_text, claim_type, evidence_location, evidence_text, attribution, confidence, verification_status, risk_level), `source_evidence`, `source_quote`, `source_theme`, `content_opportunity`, `content_angle` (menyimpan keempat skor: source_quality_score, content_value_score, transformation_score, virality_score, confidence, claim_risk, readiness_status, angle_family).

### M. Prioritas Fitur: Default vs Add-on

| Kategori | Fitur | Status |
|---|---|---|
| **Default (semua tier)** | Source identity/provenance, klasifikasi tipe sumber, quality gate dasar (pre-analysis validation), duplicate detection, structured extraction (core facts, fact/opinion split), evidence reference ringan, angle generation + angle family, Virality Potential Score, hook accuracy check ringan, aturan anti-halusinasi | Wajib rilis awal, tanpa biaya tambahan |
| **Add-on: Deep Source Intelligence** (§7-F) | Source Quality Score penuh, Content Richness Score, Claim & Evidence Registry penuh, Claim Risk & Verification, Temporal analysis per klaim, Novelty/Saturation, Contrarian/Tension Detection, Emotional Intelligence detail, Content Value Score, Transformation Potential Score, Angle Qualification Gate, Content Readiness Gate | Berbayar melalui Product/Package atau dapat termasuk dalam membership/role via Admin |
| **Add-on: Media Intelligence** (§7-G) | Visual Intelligence, Audio/Speech Intelligence, Narrative Structure & Peak Moment, Quote Intelligence | Melalui Product/Package; relevan sumber video/audio/gambar |
| **Add-on: Multi-AI** (§7-H, sudah ada, diperluas) | Jumlah AI 2–3, Full Comparison View, cross-validation berjenjang (extraction→claim→interpretation→angle→score) | Melalui Product/Package atau termasuk dalam membership/role via Admin |
| **Add-on: Cross-Source Analysis** (§7-I) | Input multi-sumber, Source Set comparison, cross-source synthesis, Source Expansion Recommendation | Melalui Product/Package |
| **Roadmap Lanjutan** (§7-K, bukan rilis awal) | Source/angle pattern performance learning, kalibrasi otomatis bobot dari Analytics, knowledge graph antar-sumber, predictive source quality | Setelah data historis cukup terkumpul |

Pembagian ini memastikan member gratis/Starter tetap mendapat Analyzer yang **jujur secara evidence dan aman dari halusinasi** (bagian yang tidak boleh dikompromikan, §7-E), sementara kedalaman analisa (skor tambahan, claim registry penuh, media intelligence, cross-source) menjadi jalur monetisasi baru yang selaras dengan mekanisme add-on yang sudah ada di §14-E.

### N. Definition of Done — Diperbarui

**Alur dasar (default, semua tier)**: member bisa memasukkan sumber (URL/video/audio/dokumen/gambar/catatan) → melihat tipe & metadata sumber → mengetahui apakah sumber berhasil diekstraksi dengan baik → melihat ringkasan, key points, dan fact/opinion split → melihat Virality Potential Score per angle beserta alasannya → melihat evidence ringan tiap insight → memilih angle × jenis konten → mengirim ke Script Review dengan traceability ke sumber asal.

**Jika Add-on Deep Source Intelligence aktif**, tambahan: melihat Source Quality/Content Value/Transformation Score, claim risk & verification warning, novelty/tension, Content Readiness status, dan alasan qualifikasi tiap angle.

**Jika Add-on Media Intelligence aktif**: melihat visual/audio hook opportunities & narrative peak moments.

**Jika Add-on Cross-Source aktif**: melihat perbandingan antar sumber & rekomendasi sumber pendukung.

**Jika Add-on Multi-AI aktif**: melihat tingkat kesepakatan antar-AI per level (bukan hanya hasil akhir) dan confidence yang disesuaikan otomatis saat terjadi disagreement.


---

## 8. Modul 3 — Review Skrip & Visual Prompt (Content Production Blueprint)

**Reposisi penting (sinkronisasi dengan Analyzer §7)**: Modul 3 **bukan lagi tempat AI mencari angle atau menganalisis konten dari awal** — itu sudah selesai di Analyzer, sampai tahap Content Readiness Gate (§7-F) dan pemilihan angle oleh member (§7-J). Modul 3 adalah **production layer**: mengubah opportunity + angle + evidence yang sudah divalidasi Analyzer menjadi **Content Production Blueprint** — script, visual blueprint, asset plan, dan editor mapping yang siap diteruskan ke Asset Preparation (§9) dan Editor (§10/§11). Nama internal *Content Production Blueprint*; UI tetap memakai label **"Review Skrip & Visual"** agar familiar bagi member.

Halaman transisi wajib sebelum masuk ke Asset Preparation. Jika member memilih beberapa kombinasi di Analyzer, tiap kombinasi punya halaman blueprint sendiri (dinavigasi lewat tab kecil "Angle 1 - Gambar", "Angle 1 - Video", dst dalam 1 sumber yang sama).

### A. Batas Tanggung Jawab (Boundary Analyzer vs Modul 3)

**Analyzer (§7) bertanggung jawab atas**: memahami sumber, mengekstrak fakta/klaim/kutipan/data, memetakan evidence, menilai reliability/risk/freshness, menganalisis audience relevance & novelty, mendeteksi emosi/tension/story, menemukan content opportunity, menghasilkan angle family, memvalidasi angle & hook terhadap evidence, melakukan scoring, cross-validation, dan menentukan Content Readiness.

**Modul 3 bertanggung jawab atas**: menerima opportunity & angle terpilih beserta evidence-nya, menyusun narrative & script profesional, memilih/menghaluskan hook yang sudah tervalidasi, membuat visual blueprint & structured prompt, menentukan asset strategy, menyusun editor mapping, menjalankan production QA, dan menghasilkan blueprint siap produksi.

**Modul 3 tidak boleh mengulang**: analisa sumber, penemuan opportunity/audience/angle, analisa novelty/freshness/evidence dari nol. Jika informasi itu sudah tersedia dari Analyzer, Modul 3 memakainya langsung — mencegah member "membuat ulang" keputusan yang sudah diambil AI di tahap sebelumnya, dan mencegah dua modul punya sumber kebenaran berbeda untuk fakta yang sama.

### B. Default — Analyzer → Script Input Contract

Analyzer wajib mengirim input terstruktur ke Modul 3 (bukan cuma teks bebas), field minimal: `content_slot_id`; `source` (source_ids, source_type, source_quality, freshness); `content_opportunity` (opportunity_id, title, problem, why_now, utility, novelty, transformation_potential, visual_opportunity); `selected_angle` (angle_id, angle_family, title, description, differentiation, audience_fit, format_fit, pillar, confidence, claim_risk); `audience` (awareness_level, pain_points, desires, objections, intent, share_motive); `hooks` (candidates, selected_hook, hook_validation); `evidence` (evidence_items, claims, statistics, quotes, contradictions, missing_context); `risk` (claim_risk, verification_required, risk_flags); `signals` (emotional_signals, visual_signals, narrative, themes); `scores` (source_quality, content_value, transformation_potential, virality_potential); `readiness` (status, blockers). Nama field disesuaikan dengan data model final Analyzer (§7-L) — **Modul 3 tidak boleh punya sumber kebenaran kedua** untuk informasi yang sudah dianalisis Analyzer.

### C. Default — Selected Angle Lock

Setelah member memilih angle di Analyzer, Modul 3 **tidak boleh mengubah angle secara diam-diam**. Script wajib mempertahankan: angle family, core thesis, audience, objective, evidence basis, dan alasan diferensiasi yang sudah ditetapkan. Jika AI menilai angle sulit diwujudkan jadi script yang baik, sistem memberi warning eksplisit — *"Angle ini mungkin butuh konteks tambahan"* — bukan otomatis mengganti angle tanpa sepengetahuan member.

### D. Bagian 01 — Content Context (Default)

Menampilkan ringkasan Analyzer yang **relevan saja**, bukan seluruh hasil Analyzer: Objective, Audience, Pillar, Selected Angle, Confidence, jumlah Evidence, jumlah Claim yang perlu verifikasi, Visual Opportunity level. Tombol **"Lihat Insight Analyzer"** membuka detail lengkap tanpa keluar dari Modul 3. Ditambah ringkasan **"Kenapa Script Ini?"** — daftar singkat dasar pembuatan script (angle terpilih, jumlah evidence, pain point audiens, visual opportunity, objective konten) agar keputusan AI tetap explainable, bukan kotak hitam.

### E. Bagian 02 — Script (Default)

**Struktur Umum**: Hook → Context → Core Value → Evidence/Example → Takeaway → CTA — struktur ini **boleh berubah** menyesuaikan angle & format, AI tidak boleh memaksakan template yang sama untuk semua konten.

**Jika Jenis Konten Gambar (Carousel/Single Post)**:
- Carousel diperlakukan sebagai **narrative sequence**, bukan kumpulan gambar lepas — default urutan: Hook → Context/Problem → Key Point(s) → Evidence/Example → Practical Implication → Summary → CTA, jumlah slide dinamis mengikuti kekayaan konten.
- Tiap slide punya **role** eksplisit (Hook/Context/Problem/Point/Evidence/Example/Comparison/Summary/CTA) yang menentukan layout, tipe visual, densitas copy, dan penekanan visual.
- **Aturan Copywriting**: headline 8–12 kata, supporting copy 20–30 kata, satu slide = satu pesan inti, maksimal 2–3 blok teks, maksimal 3 tingkat hierarki teks. Jika terlampaui, sistem memberi peringatan **"Densitas teks tinggi"** dengan saran: persingkat copy / pecah ke slide lain / ganti jadi penjelasan visual.
- **Evidence Mapping per Slide**: tiap klaim utama pada slide menyertakan referensi evidence & sumber (lihat §8-G) beserta tombol **"Lihat Evidence"** tanpa keluar dari Modul 3.
- Judul & headline utama, Caption & Hashtag Generator (teks caption umum + saran tagar, member sesuaikan sendiri saat upload) — tetap seperti spesifikasi awal.
- Tombol: **"Lanjutkan ke Persiapan Aset"**.

**Jika Jenis Konten Video (Pendek/Panjang)**:
- Video membutuhkan **Voiceover Script + Subtitle Script + Shot List + Visual Direction + Editing Direction** sekaligus, bukan cuma voiceover. Struktur default: Hook → Context → Development → Evidence/Example → Payoff → CTA, durasi mengikuti isi (bukan durasi tetap dipaksakan).
- **Voiceover ≠ Subtitle** — subtitle tidak otomatis disalin dari transkrip voiceover, melainkan diracik ulang sebagai lapisan komunikasi visual tersendiri (lebih pendek, ada penekanan kata kunci), sementara voiceover tetap naratif penuh.
- **Tabel Skenario Matriks (Timeline Breakdown)** per shot, kolom: Timestamp/Durasi, Voiceover/Audio Script, On-Screen Subtitle (+ kata yang ditekankan), Visual Directives (arahan kamera/pergerakan kamera/b-roll/animasi), Asset Strategy (lihat di bawah), AI Video/Image Prompt per Shot, Evidence reference (jika shot membawa klaim tertentu).
- **Asset Strategy per Shot** — tidak semua shot wajib AI-generate; sistem menyarankan salah satu: `ai_video`, `ai_image`, `uploaded_video` (mis. talking head milik member), `screenshot`, `graphic`, `screen_recording`, `text_only` — mengurangi biaya generate AI & beban editing yang tidak perlu.
- Caption & Hashtag Generator.
- Tombol: **"Lanjutkan ke Persiapan Aset"**.

### F. Default — Fact, Interpretation & Creative Copy Terpisah

Untuk mencegah AI mengubah makna sumber, setiap klaim penting dijaga terpisah secara internal antara: **Source Fact** (mis. "X meningkat 20%") → **Interpretation** (mis. "Peningkatan ini menunjukkan perubahan signifikan") → **Creative Copy** (mis. "Angkanya naik 20%. Tapi apa yang berubah?"). Creative copy boleh lebih menarik secara gaya bahasa, tapi **tidak boleh mengubah fakta atau tingkat kepastian klaim**.

**Claim Risk & Verification diwariskan dari Analyzer** — jika Analyzer menandai `claim_risk: medium` / `verification_required: true` pada sebuah klaim, Modul 3 wajib mempertahankan status itu dan menampilkan badge **"⚠ Klaim Perlu Verifikasi"** di baris script terkait, beserta tombol **"Lihat Evidence"** dan **"Edit Klaim"**. Script tidak boleh terlihat 100% otoritatif jika Analyzer belum memberi confidence tinggi.

### G. Default — Evidence-Bound Script & Visual Signal

- **Evidence Binding**: tiap klaim penting di script terhubung ke `Evidence ID → Source ID → Source Location` (paragraf/timestamp/halaman) — ditampilkan sebagai badge ringkas *"Evidence-backed, Confidence: [level]"* dengan tombol **"Lihat Evidence"**.
- **Visual Signals → Visual Blueprint**: jika Analyzer menghasilkan visual signal dari sumber (mis. "creator frustrasi", "grafik menurun", "editing cepat"), Modul 3 memakainya sebagai bahan shot/slide — visual tidak hanya diturunkan dari teks script, tapi juga dari peluang visual yang sudah ditemukan Analyzer (§7-G).
- **Narrative Inheritance**: jika Analyzer menghasilkan pola narasi (mis. Problem → Contradiction → Evidence → Implication → Solution), Modul 3 menerjemahkannya ke struktur script (§8-E) alih-alih menemukan ulang pola narasi dari nol, kecuali member secara eksplisit meminta perubahan.

### H. Default — Hook Pipeline

Menggunakan hook candidates yang sudah tervalidasi Analyzer (§7-D, §7-H): **Analyzer Hook Candidates → Selected/Recommended Hook → Script Refinement → Hook Validation → Final Hook**. UI menampilkan hook terpilih beserta checklist ringkas (cocok dengan angle terpilih / relevan audiens / selaras evidence / tidak ada klaim tak terdukung) dan tombol **[Edit] [Buat Alternatif]**. Jika member mengubah hook secara manual, sistem menjalankan ulang **Hook Accuracy Check** (§7-D) sebelum hook final disimpan.

### I. Default — Structured Prompt (Backend)

Prompt gambar/video **tidak disimpan hanya sebagai satu string bebas** — disimpan terstruktur per elemen: Subject, Action, Environment, Composition, Camera (+ Camera Movement untuk video), Lighting, Style, Mood, Palette, Aspect Ratio, Continuity Group, Negative Prompt. Sistem merangkai elemen ini menjadi `final_prompt` yang dikirim ke provider AI. Manfaat: regenerate lebih mudah (cukup ganti 1 elemen), style bisa diganti tanpa menulis ulang semua, continuity lebih terjaga, dan tidak terikat 1 provider AI tertentu. Prompt yang sudah dirangkai tetap **editable oleh member** sebelum masuk ke Modul 4, sesuai spesifikasi awal.

### J. Default — Editor Mapping

Modul 3 menghasilkan mapping yang bisa langsung dipakai Editor (§10/§11), mengikuti alur `Slide/Shot → Template/Timeline → Layer/Track → Konten → Aset`. Untuk Gambar: tiap layer (headline/body/image) dipetakan ke zona template yang dipilih member. Untuk Video: tiap shot dipetakan ke rentang waktu di timeline beserta track (video, voiceover, subtitle, graphic). Tujuannya agar saat member membuka Editor, **timeline/canvas sudah tersusun** — member melakukan fine-tuning, bukan menyusun dari nol (konsisten dengan prinsip Auto-Populate di §10 dan §11).

### K. Default — Production QA & Editability Matrix

Sebelum tombol **"Setujui & Siapkan Aset"**, sistem menjalankan QA otomatis di 5 dimensi: **Content QA** (konsistensi angle/audience/objective/pillar), **Evidence QA** (klaim terlacak, evidence tersedia, tidak ada klaim tak terdukung, flag verifikasi tetap ada), **Hook QA** (cocok angle & evidence, tidak menyiratkan makna menyesatkan), **Copy QA** (densitas teks, kejelasan, alur narasi, CTA), **Visual QA** (relevansi visual, komposisi, continuity, kelengkapan prompt), **Production QA** (aset terdefinisi, layout terisi, timeline valid, editor mapping valid).

**Editability Matrix** — menentukan elemen mana yang bisa diedit/regenerate bebas, dan mana yang memicu revalidasi evidence saat diubah:

| Elemen | Edit | Regenerate | Perlu Revalidasi |
|---|---:|---:|---:|
| Hook | ✓ | ✓ | ✓ |
| Headline | ✓ | ✓ | Jika klaim berubah |
| Supporting Copy | ✓ | ✓ | Jika klaim berubah |
| CTA | ✓ | ✓ | Tidak |
| Visual Concept / Prompt | ✓ | ✓ | Tidak |
| Asset Type | ✓ | ✓ | Tidak |
| Layout | ✓ | ✓ | Tidak |
| Voiceover | ✓ | ✓ | Jika klaim berubah |
| Subtitle / Timestamp / Camera | ✓ | ✓ | Tidak |

**Granular Regeneration** — member bisa meregenerasi bagian tertentu saja (mis. hanya Hook, atau hanya Slide 3 Copy/Visual/Prompt, atau hanya Shot 4 Voiceover/Subtitle/Visual) tanpa mengulang seluruh script/blueprint; perubahan manual di bagian lain **tidak boleh ikut terhapus** saat regenerate parsial.

### L. Default — Versioning & Undo

Mengikuti prinsip persistensi platform (§2-C): setiap perubahan signifikan tercatat sebagai versi (Script v1 — AI Generated, v2 — Hook diedit member, v3 — Visual diregenerasi, dst), minimal tersedia **Undo**, idealnya juga **Restore**, **Compare**, dan **Duplicate** — sejalan dengan pola Undo & Version History yang sudah ada di Planner (§6-Q).

### M. Add-on — Visual Continuity Engine (Berbayar per Proyek)

Untuk carousel/video yang memakai karakter, produk, atau environment yang sama berulang kali, member bisa mengaktifkan **continuity lock**: sistem menyimpan `continuity_group_id`, tipe entitas, deskripsi, dan atribut yang dikunci (mis. usia, wardrobe, gaya rambut, gaya visual), lalu memastikan tiap prompt baru dalam grup yang sama mewarisi atribut terkunci tersebut. UI menampilkan indikator **"Konsistensi Visual — Karakter Terkunci / Environment Terkunci"**. Add-on ini menggunakan entitlement yang tersedia karena butuh pemrosesan tambahan tiap kali prompt baru dibuat dalam satu grup kontinuitas.

### N. Add-on — Advanced Prompt Studio & Auto-Fix (Berbayar per Proyek)

- **Advanced Prompt Editor**: akses langsung ke `final_prompt` mentah per elemen (§8-I) untuk member yang ingin mengatur prompt secara presisi (termasuk optimasi khusus per provider AI) — level default cukup dengan structured prompt yang sudah dirangkai otomatis, add-on ini untuk member/agency yang butuh kontrol lebih dalam.
- **One-Click Fix**: saat Production QA (§8-K) menemukan masalah (mis. densitas teks tinggi di satu slide), AI otomatis meringkas copy, menyesuaikan layout, dan memperbarui visual prompt/asset requirement terkait — tetap wajib mempertahankan angle terpilih, evidence, makna klaim, audience, dan objective. Karena melibatkan pemanggilan AI tambahan untuk perbaikan otomatis, fitur ini berbayar per penggunaan.

### O. Roadmap Lanjutan (Bukan Rilis Awal, Terhubung Analytics)

Setelah data performa cukup terkumpul di Analytics (§13): **Analytics-driven re-edit** (saran revisi script/visual berdasarkan performa nyata), **automatic visual-performance learning** (pola visual mana yang berkorelasi dengan performa tinggi), **template A/B testing**, **visual fatigue detection** (mendeteksi pola visual yang mulai jenuh dipakai berulang), **provider-specific prompt optimization** otomatis. Ini melengkapi Closed-Loop Learning yang sudah dirancang di Analyzer (§7-K) — Modul 3 ikut menjadi bagian dari loop tersebut, bukan hanya penerima sekali pakai.

### P. Output Contract — Content Production Blueprint (Default)

Modul 3 menghasilkan objek terstruktur berikut sebagai output resmi ke Asset Preparation (§9) dan Editor (§10/§11): `content_slot_id`, `script_id`, `inherited_context` (opportunity_id, angle_id, audience, objective, pillar, confidence), `narrative`, `hook`, `script`, `cta`, `evidence_bindings`, `claim_bindings`, `carousel.slides[]` atau `video.shots[]` (sesuai jenis konten), `visual_blueprints[]`, `asset_requirements[]`, `prompts[]`, `editor_mappings[]`, `qa` (hasil Content/Evidence/Hook/Copy/Visual/Production QA), `readiness` (status, blockers).

### Q. Prioritas Fitur: Default vs Add-on

| Kategori | Fitur | Status |
|---|---|---|
| **Default (semua tier)** | Analyzer→Script Input Contract, Selected Angle Lock, Content Context, struktur script (carousel & video) + aturan copywriting, fact/interpretation/creative-copy split, claim risk inheritance, evidence binding & visual signal inheritance, hook pipeline & validasi ulang, structured prompt, editor mapping, Production QA 5 dimensi, Editability Matrix, granular regeneration, versioning & undo, output contract blueprint | Wajib rilis awal, tanpa biaya tambahan |
| **Add-on: Visual Continuity Engine** (§8-M) | Continuity group lock lintas slide/shot untuk karakter, produk, environment yang sama | Berbayar per proyek |
| **Add-on: Advanced Prompt Studio & Auto-Fix** (§8-N) | Akses raw prompt per elemen, optimasi per provider, perbaikan otomatis 1-klik saat QA gagal | Berbayar per penggunaan |
| **Roadmap Lanjutan** (§8-O, bukan rilis awal) | Analytics-driven re-edit, visual-performance learning, template A/B testing, visual fatigue detection, prompt optimization otomatis | Setelah data historis cukup terkumpul |

### R. Definition of Done — Diperbarui

Modul dianggap matang jika member bisa: melihat selected opportunity & angle beserta audience/objective → melihat evidence yang dipakai & claim risk → melihat hook yang sudah tervalidasi → melihat & mengedit narrative/script → melihat carousel/video blueprint beserta visual strategy & asset strategy → melihat prompt siap produksi → melihat evidence binding tiap klaim → melihat editor mapping → menjalankan regenerasi granular → menjalankan Production QA dan memperbaiki issue yang ditemukan → menyetujui script → mengirim blueprint ke Asset Preparation dengan traceability penuh (Script → Angle → Evidence → Source) tetap terjaga.

**Acceptance Criteria** — tiap kombinasi angle × jenis konten yang selesai diproses menghasilkan tepat: 1 `content_slot_id`, 1 `script_id`, 1 `opportunity_id`, 1 `angle_id`, N slide/shot (sesuai jenis konten), N visual blueprint, N evidence binding, N asset requirement, N prompt gambar/video, N editor mapping, 1 hasil Production QA — untuk Video ditambah 1 voiceover script & N subtitle segment.

---

## 9. Modul 4 — Asset Preparation Center

**Fungsi**: tahap tersendiri untuk menyiapkan seluruh aset (gambar/video/voiceover) sebelum masuk ke Editor. Editor difokuskan untuk menyusun & merapikan, bukan tempat pertama kali membuat aset.

### A. Daftar Kebutuhan Aset
- Sistem menampilkan checklist otomatis berdasarkan `asset_requirements` pada Content Production Blueprint dari Modul 3 (§8-P) — misal untuk Carousel 5 slide: 5 slot gambar; untuk Video 8 shot: 8 slot video/gambar b-roll + 1 slot voiceover. Tiap slot sudah membawa prompt siap pakai, asset type yang disarankan (§8-E), dan editor target (§8-J), sehingga member tidak menyusun ulang dari nol.

### B. Dua Cara Mengisi Tiap Slot Aset
1. **Generate AI** — tombol `✨ Generate Preview` (gratis, watermark/resolusi rendah, lihat §14-B) menggunakan prompt dari skrip (editable), lalu `✓ Gunakan Versi Ini` untuk finalisasi (memotong kuota).
2. **Upload Media Sendiri** — member mengunggah foto/video/audio milik sendiri untuk slot tersebut. File diproses client-side dan tidak dikirim/disimpan ke server kecuali member melanjutkannya ke Editor untuk diedit lebih lanjut (baru di titik itu file ikut tunduk pada kebijakan retensi 48 jam, §12).
- Member bebas mencampur: sebagian slot pakai AI generate, sebagian pakai upload sendiri, dalam satu konten yang sama.

### C. Preview Grid
- Tampilan grid semua slot aset (thumbnail) dengan status per slot: Kosong / Preview / Final / Upload Sendiri.
- Klik slot untuk generate ulang, ganti sumber, atau hapus.

### D. Lanjut ke Editor
- Tombol aktif setelah semua slot terisi (Final atau Upload) — **"Lanjutkan ke Editor Gambar"** atau **"Lanjutkan ke Editor Video"** sesuai jenis konten.

---

## 10. Modul 5A — Smart Canvas Image Editor

**Konsep**: slide-deck canvas sederhana (mirip Canva/Figma ringan), fokus menyusun & merapikan aset yang sudah disiapkan di Modul 4.

- **Auto-Populate**: teks dari Modul 3 dan aset dari Modul 4 otomatis terpasang ke **template** yang dipilih member.
- **Pilihan Template**: daftar template yang bisa ditambah/dikelola admin (lihat §17-E Global Template Manager) — member memilih salah satu sebagai titik awal, lalu bebas mengedit sesuai kemauan.
- **Multi-Slide Layout Bar**: strip thumbnail slide di bawah untuk navigasi cepat antar slide.
- **Sidebar**: akses cepat regenerate aset (kembali sebentar ke Modul 4 tanpa kehilangan progres editing) jika member ingin ganti gambar tertentu.
- **Panel Editing**: font, warna brand kit, upload logo, background remover 1-klik (preview client-side, proses akhir server-side), reposisi layer (drag, semua di client-side).
- **Export Engine**: ekspor batch seluruh slide → **PNG, JPG, WEBP** (per gambar) atau **PDF** (gabungan carousel).

---

## 11. Modul 5B — Smart Timeline Video Editor

**Konsep**: multi-track timeline sederhana, drag-and-drop, fokus menyusun aset yang sudah disiapkan di Modul 4.

- **Preview Canvas Vertikal (9:16)** untuk Video Pendek / horizontal (16:9) untuk Video Panjang (saat sudah aktif): simulasi real-time.
- **Multi-Track Timeline**:
  - Track 1 — Video/Image B-Roll (dari Modul 4: hasil AI atau upload sendiri)
  - Track 2 — Voiceover & Musik Latar
  - Track 3 — Subtitle/Text Overlay
- **Pilihan Template**: template struktur timeline yang bisa ditambah/dikelola admin, member bebas modifikasi.
- **Auto-Subtitling**: caption otomatis dengan gaya animasi ala konten viral.
- **Trimming/pemotongan klip**: dilakukan client-side (non-destruktif) sebelum render final.
- **Export Engine**: render final → **MP4** atau **WEBM**, siap unggah ke platform manapun.

---

## 12. Storage & Auto-Purge Policy

### A. Storage Boundary
- Media member yang diunggah di Asset Preparation diproses client-side dan tidak disimpan di server sampai member membawanya ke Editor.
- File yang sudah masuk ke server berada dalam Storage Retention Scope sesuai policy berikut.
- Storage retention untuk content export berbeda dari Support Ticket attachment retention.

### B. Rilis Awal — Retention 48 Jam
- Seluruh file hasil ekspor gambar/video dan media milik member yang sudah masuk tahap Editor **dihapus permanen otomatis setelah 48 jam**.
- Countdown dimulai ketika file final berhasil tersedia untuk digunakan/download oleh member.
- Download atau membuka Storage Manager tidak mereset countdown.
- File Asset Preparation yang belum dibawa ke Editor tetap client-side dan tidak termasuk retention scope server.

### C. Project Data vs Binary File
Auto-Purge hanya menghapus binary/media object yang berada dalam retention scope. Auto-Purge **tidak menghapus**:
- `content_slot_id`;
- Content Plan/Slot;
- Research/Insight;
- Analyzer result;
- Script/Blueprint;
- Editor metadata;
- Export metadata/history;
- project/lifecycle data yang ditetapkan persistent.

Dengan demikian, binary file dapat expired tanpa menghapus project history.

### D. Storage Manager
**Export & Storage Manager** menampilkan:
- thumbnail;
- file name;
- type;
- size;
- related content / `content_slot_id`;
- available time / retention deadline;
- countdown;
- status;
- download.

### E. Storage Lifecycle
Storage object secara internal dapat menggunakan:

```text
AVAILABLE → EXPIRING → PURGE_PENDING → PURGED
```

Jika purge gagal, sistem melakukan retry dan mencatat event/audit tanpa memengaruhi project data.

### F. Future Storage Monetization — Core-ready
Rilis awal menggunakan retention 48 jam sesuai policy gratis platform. Core harus memungkinkan retention policy dan storage entitlement dikonfigurasi per membership/product di masa depan tanpa mengubah lifecycle produksi.

---

## 13. Modul Analytics & Performance Center

**Fungsi**: menutup siklus kerja (*closing the loop*). Hasil konten yang sudah diposting member dievaluasi di sini, lalu insight dan learning dialirkan kembali ke Research Intelligence, Planner, dan secara bertahap ke Analyzer/Production layer.

### A. Koneksi Data Performa
- **Opsi 1 — Integrasi API Resmi**: hubungkan akun/platform yang didukung melalui OAuth resmi agar metrik ditarik otomatis.
- **Opsi 2 — Input Manual**: member memasukkan angka performa dasar secara manual.

Setiap data dapat membawa source, freshness, sample size, dan observed/estimated status.

### B. Performance Overview & Dashboard
- Ringkasan performa per platform dan rentang waktu.
- Reach, engagement rate, watch time/completion rate, saves/shares, serta metrik yang tersedia dari sumber data.
- Period comparison dan own-account baseline.
- Median, average, dan sample size ketika data mencukupi.

### C. Content Performance Ranking
- Peringkat konten terbaik dan terlemah.
- Breakdown per pillar, content type, topic, dan dimensi lain yang tersedia.

### D. Performance Pattern Intelligence
Ketika sample cukup, sistem dapat mempelajari:
- format performance;
- pillar performance;
- topic performance;
- hook pattern;
- angle family;
- CTA;
- posting window;
- Content DNA;
- content fatigue.

### E. A/B & Experiment Insight
- Membandingkan variasi hook, waktu posting, content type, pillar, topic, angle, CTA, atau variabel lain.
- Sistem membedakan observed difference dengan interpretation dan tidak memaksakan kesimpulan jika data tidak cukup.

### F. Confidence & Data Quality
Insight dapat menampilkan:
- source;
- freshness;
- sample size;
- observed vs estimated;
- confidence.

Jika data tidak cukup:

> **Insufficient data**

AI dilarang mengarang data untuk mengisi kekosongan.

### G. Recommendation Engine
AI dapat menghasilkan recommendation terstruktur yang menjelaskan:
- apa yang direkomendasikan;
- evidence/reason;
- affected area;
- confidence;
- tindakan yang tersedia.

Member dapat:

```text
Review Changes
Apply Recommendation
Keep Current Strategy
```

Strategi Planner tidak pernah diubah diam-diam oleh Analytics.

### H. Recommendation Outcome & Learning History
Recommendation yang diterapkan dapat dicatat hasilnya agar sistem belajar:

```text
Recommendation
→ Applied
→ Measurement Window
→ Outcome
→ Learning Signal
```

### I. Closed Loop
- **Analytics → Research**: Own Content Intelligence, Content Gap/Opportunity, audience/content learning.
- **Analytics → Planner**: rekomendasi distribusi pillar, format, timing, dan workload.
- **Analytics → Analyzer/Production**: learning hook/angle/topic secara bertahap ketika data historis cukup.

### J. Attribution
Performance harus dapat ditelusuri ke `content_slot_id`, dan bila tersedia ke:

```text
Content → Angle → Opportunity → Source → Evidence
```

Tujuannya agar platform dapat belajar dari hasil nyata tanpa kehilangan traceability.

---

## 14. Membership, Product Packages, Entitlement & Billing

### A. Prinsip Billing Final
- **Tidak ada Top Up untuk member biasa.**
- **Tidak ada PAYG Wallet / Saldo Deposit untuk member biasa.**
- Member membeli **Membership**, **Feature Package**, dan/atau **Add-on Package** sesuai kebutuhan.
- Setiap pembelian menghasilkan **Entitlement** yang dapat dikonsumsi oleh capability yang tersedia di core.
- Core harus memisahkan `Product`, `Order`, `Payment`, `Entitlement`, dan `Usage`.

```text
Membership / Product Package
        ↓
      Order
        ↓
     Payment
        ↓
   Entitlement
        ↓
    Feature Usage
```

### B. Fitur Inti vs Entitlement Berat
- **Fitur inti** seperti Riset, Planner, Analyzer dasar, dan Script dasar tetap mengikuti prinsip unlimited sesuai membership/role.
- **Generate berat** seperti gambar AI dan video AI menggunakan entitlement yang diberikan oleh membership dan/atau package yang dibeli.
- Voiceover mengikuti entitlement video atau product entitlement yang ditentukan Admin.
- Upload media sendiri tetap tidak memotong entitlement generate AI.

### C. Preview vs Final
- Generate AI di Modul 4 pertama kali menghasilkan preview gratis sesuai policy preview.
- Entitlement dikonsumsi ketika member melakukan finalisasi sesuai flow Modul 4.

### D. Membership Tier
Harga dan isi membership sepenuhnya configurable oleh Admin.

Contoh default awal:

| Tier | Harga | Riset/Planner/Analyzer/Skrip | Gambar AI | Video AI | Multi-Workspace |
|---|---|---|---:|---:|---|
| Starter | Ditetapkan Admin | Unlimited | Ditetapkan Admin | Ditetapkan Admin | ✗ |
| Growth | Ditetapkan Admin | Unlimited | Ditetapkan Admin | Ditetapkan Admin | ✗ |
| Agency / White-label | Future / terpisah | Configurable | Configurable | Configurable | ✓ |

- Free Trial dapat disediakan dengan entitlement gratis yang sangat terbatas sesuai konfigurasi Admin.
- Membership annual dapat menggunakan billing cycle tahunan dengan benefit mengikuti policy membership.

### E. Feature Package & Add-on Package
Admin dapat membuat product/package baru selama capability yang diperlukan sudah tersedia di core.

Contoh:

```text
Image Package 25
Video Package 10
Deep Source Intelligence 10
Multi-AI 10
Visual Continuity 5 Projects
Advanced Prompt Studio 10 Uses
```

Admin bebas menentukan:
- nama;
- harga;
- currency;
- entitlement amount;
- duration/policy;
- billing type;
- market;
- visibility;
- active/inactive.

### F. Purchased Package
- Purchased package **tidak hangus**.
- Package hanya dapat **digunakan** jika membership/subscription user aktif.
- Jika membership tidak aktif, package tetap dimiliki tetapi berstatus locked.
- Jika membership aktif kembali, package dapat digunakan kembali sesuai entitlement yang tersisa.
- Jika member downgrade, purchased package tetap dimiliki.
- Saat membership tidak aktif, member **tidak dapat membeli package baru**.

```text
Subscription Active
→ Existing Package Usable
→ New Package Purchase Allowed

Subscription Inactive
→ Existing Package Locked
→ New Package Purchase Not Allowed
```

### G. Account & Billing
Halaman Account & Billing menampilkan:
- current membership;
- subscription status;
- renewal/expiry;
- current entitlements;
- active purchased packages;
- package entitlement remaining;
- billing history;
- invoice/receipt;
- payment methods;
- upgrade/downgrade;
- cancel subscription.

Tidak ada saldo wallet, PAYG balance, atau tombol Top Up untuk member biasa.

### H. Multi-Currency
Core harus global-ready dengan minimum:

```text
IDR
USD
```

- Harga IDR dan USD dapat ditentukan secara independen oleh Admin.
- Currency default configurable per market.
- Bahasa default mengikuti market.

### I. Payment Gateway
Payment layer harus provider-agnostic dan dapat dikonfigurasi Admin.

Provider/method yang disiapkan:

```text
Xendit
Duitku
NOWPayments
Manual Transfer
Provider lain / Custom Provider
```

Admin dapat mengatur credential, currency, market, active/inactive, priority, webhook, dan konfigurasi provider.

### J. Manual Transfer
Manual transfer wajib melalui verification Admin satu per satu.

```text
Order
→ Manual Transfer
→ Member Transfer
→ Bukti Transfer via Support Ticket
→ Admin Approve Ticket
→ Payment = Paid
→ Entitlement Granted
```

### K. Order & Entitlement Integrity
- Historical order menyimpan snapshot product/price/currency saat transaksi dibuat.
- Perubahan harga product tidak mengubah historical transaction.
- Entitlement hanya diberikan setelah payment memenuhi status sukses/approved sesuai metode pembayaran.

### L. Promo Code
Promo code diterapkan pada order/product sebelum pembayaran.
Nilai yang dibayar setelah diskon menjadi nilai transaksi aktual.

### M. Subscription Lifecycle
```text
Trial / Active
→ Cancelled
→ Expired
```

- Cancelled tetapi masih dalam masa aktif tetap dapat menggunakan membership entitlement sampai expiry.
- Setelah expiry, membership entitlement terkunci.
- Purchased package tetap dimiliki tetapi terkunci sampai membership aktif kembali.

---

## 15. Program Referral & Checkpoint Milestone

### A. Referral Attribution
- Setiap member memiliki referral link/code unik.
- Default attribution window: **90 hari**.
- Attribution window dapat diubah Admin.
- Setelah downline teratribusi ke Referrer A, attribution tetap milik A.
- Member yang mendaftar tanpa upline otomatis menjadi downline **Super Admin**.

### B. Active Downline
`active_downline` adalah downline yang telah melakukan pembayaran subscription dan subscription tersebut valid/aktif.

Tidak dihitung:
- free trial;
- akun gratis;
- pembayaran gagal;
- suspended;
- subscription expired/tidak aktif.

Jika subscription dibatalkan tetapi masih aktif sampai end date, downline tetap dihitung active sampai expiry.

### C. Recurring Commission
- Commission = **10% dari jumlah subscription yang benar-benar dibayarkan downline**.
- Promo/discount menurunkan dasar komisi.
- Top Up/PAYG bukan sumber commission pada model billing final.

### D. Commission Availability
```text
Monthly subscription → Available setelah 1 bulan
Annual subscription  → Available setelah 3 bulan
```

### E. Refund & Commission
- Refund normal hanya dapat diminta maksimal **3 hari setelah pembelian**.
- Jika refund terjadi saat commission masih Pending, commission dibatalkan otomatis dan upline diberi informasi.
- Setelah refund window berakhir, transaksi masuk status no-refund sesuai policy.
- Jika koreksi finansial/clawback diperlukan, pemotongan dapat dilakukan dari future commission.

### F. Checkpoint Milestone
Milestone dihitung berdasarkan **current active downline count**, bukan lifetime recruited users.

| Milestone | Syarat | Bonus | Pengaturan |
|---|---|---|---|
| Level 1 | 50 downline aktif | Rp X | Ditetapkan Admin |
| Level 2 | 100 downline aktif | Rp Y (>X) | Ditetapkan Admin |
| Custom | Configurable | Dynamic | Admin dapat menambah |

Current progress mengikuti active downline saat ini.

### G. Dashboard Referral
- referral link;
- registered referrals;
- active downlines;
- total commission earned;
- pending commission;
- available commission;
- paid commission;
- milestone progress;
- withdrawal history.

### H. Withdrawal
- Minimum withdrawal: **Rp20.000**.
- Indonesia: Bank Transfer, DANA, OVO, GoPay.
- Metode payout dapat ditambah Admin.
- Future international payout dapat menggunakan PayPal/provider lain.

---

## 16. Support Ticket System

### A. Interface & Category
- Gaya email inbox berbasis kartu pesan.
- Member dapat membuat ticket dengan kategori: **Billing, Technical Bug, Feature Request**.

### B. Ticket Lifecycle
```text
Open
→ In Progress
→ Resolved
→ Closed
```

- Member reply pada Resolved dapat mengembalikan ticket ke In Progress sesuai flow support.
- Closed ticket hanya dapat di-reopen oleh Admin.
- Member yang memiliki masalah lanjutan dapat membuat ticket baru dan Admin dapat menghubungkannya dengan ticket lama.

### C. Priority
```text
Low
Normal
High
Urgent
```

### D. SLA — First Response
| Priority | Target |
|---|---|
| Urgent | ≤ 4 jam kerja |
| High | ≤ 8 jam kerja |
| Normal | ≤ 1 hari kerja |
| Low | ≤ 2 hari kerja |

### E. SLA — Resolution Target
| Priority | Target |
|---|---|
| Urgent | ≤ 1 hari kerja |
| High | ≤ 2 hari kerja |
| Normal | ≤ 5 hari kerja |
| Low | ≤ 10 hari kerja |

SLA adalah target operasional, bukan jaminan absolut.

### F. Auto-Close
- Ticket `Resolved` yang tidak mendapat balasan member selama **7 hari** dapat otomatis menjadi `Closed`.
- Countdown dihitung dari response/resolution terakhir Admin.

### G. Attachment
- Maksimum **2 MB per file**.
- Format: **PDF, PNG, JPG**.
- Retention: **90 hari setelah ticket Closed**.
- Retention Support Attachment terpisah dari Storage & Auto-Purge content export.

### H. Manual Transfer Integration
Bukti transfer manual dikirim melalui Support Ticket. Setelah Admin menyetujui ticket tersebut, payment manual menjadi `Paid` dan entitlement order diberikan.

---

## 17. Dashboard Admin (Godmode)

### A. Prinsip Admin Godmode
Godmode adalah **control plane** platform. Admin dapat mengatur business configuration yang memang dirancang configurable tanpa mengubah kode untuk setiap perubahan bisnis normal.

### B. Role & Permission Builder
- Admin dapat membuat role baru tanpa batas praktis.
- Starter, Growth, Agency/White-label, Staff Internal hanya preset awal.
- Permission granular: View, Create, Edit, Delete, Execute, Approve, Export, Manage, Configure.
- Permission dapat memiliki scope: Own Data / Team Data / Workspace Data / All Data.
- Membership dan Role adalah konsep terpisah: membership menentukan entitlement, role menentukan permission.
- Admin dapat assign, remove, duplicate, archive, activate/deactivate role.

### C. User Management
Admin dapat:
- search user;
- melihat membership;
- melihat role;
- melihat entitlement;
- assign/change role;
- block/unblock;
- suspend/reactivate;
- melihat activity;
- melihat security/session events.

### D. AI Provider Pool Manager
Admin dapat mengelola:
- Text & Analysis Pool;
- Image Generator Pool;
- Video Generator Pool;
- Voice Generator Pool;
- Research & Data Provider Pool.

Custom Provider Registry memungkinkan provider baru ditambahkan tanpa mengubah struktur pool inti.

### E. Research Provider Administration
Research Provider memiliki **pool sendiri** dan Admin dapat:
- menambah provider;
- mengubah credential;
- menentukan pool;
- menentukan priority/fallback;
- enable/disable;
- melihat health/quota;
- menentukan market/region jika diperlukan.

### F. Product & Add-on Catalog
Admin dapat membuat dan mengubah:
- membership;
- feature package;
- add-on package;
- harga;
- IDR/USD price;
- entitlement;
- duration/policy;
- market;
- visibility;
- active/inactive;
- bundle.

Product baru tidak perlu hard-code jika capability sudah tersedia di core.

### G. Feature Flags
Admin dapat mengatur feature availability berdasarkan:
- global;
- role;
- membership;
- user;
- market.

### H. Currency & Market Configuration
- Currency minimum: IDR, USD.
- Currency default configurable per market.
- Language default mengikuti market.
- Bahasa minimum: Indonesia, English.

### I. Payment Gateway Configuration
Admin dapat mengelola:
- Xendit;
- Duitku;
- NOWPayments;
- Manual Transfer;
- provider lain/custom.

Credential disimpan secara aman dan konfigurasi provider dapat diaktifkan/nonaktifkan sesuai market/product.

### J. Finance Center
- Revenue Dashboard: Membership, Feature Packages, Add-ons, Agency/White-label, Storage future.
- Cost Tracking per provider/pool.
- Profit Margin Monitor.
- Transaction Ledger.
- Payment Gateway.
- Refund & Dispute Management.
- Referral Payout Center.

### K. Support Administration
Admin dapat mengelola inbox, assignment, priority, SLA, auto-close, attachment policy, dan audit.

### L. Storage Administration
Admin dapat mengatur retention policy, storage limits, export retention, support attachment retention, serta future storage product.

### M. Analytics & Scoring Configuration
Admin dapat mengatur scoring weights, thresholds, confidence policy, recommendation behavior, dan feature flags.

### N. Security Administration
- single-login policy;
- active sessions;
- revoke session;
- login history;
- security events;
- 2FA policy;
- content protection policy.

### O. White-label Control Surface — Future/Core-ready
Godmode menyediakan control surface untuk future white-label:
- tenant/agency;
- product visibility;
- pricing markup/override;
- API access;
- domain status;
- branding;
- activation.

Full white-label UI dan operational product **bukan rilis awal**.

### P. System Operations
Admin dapat melihat status:
- AI jobs;
- export jobs;
- purge jobs;
- research sync;
- analytics jobs;
- payment webhooks;
- notification jobs.

### Q. Audit Trail
Perubahan sensitif wajib mencatat:
```text
Who
What
When
Target
Before
After
Reason
Session
```

Audit history tidak boleh dihapus melalui UI normal.

### R. Soft Delete / Archive
Untuk product, role, provider, dan konfigurasi yang memiliki histori, gunakan `Active / Inactive / Archived` daripada hard delete bila diperlukan untuk menjaga histori.

---

## 18. Status Konten (State Machine Resmi)

```
Draft → Source Needed → Script Ready → Asset Ready → Editing → Exported
```

| Status | Arti |
|---|---|
| Draft | Slot kalender terbuat, belum ada sumber |
| Source Needed | Menunggu member mengisi sumber di Analyzer |
| Script Ready | Skrip sudah digenerate & disetujui di Modul 3 |
| Asset Ready | Seluruh aset di Modul 4 sudah final/terisi (AI atau upload) |
| Editing | Sedang dikerjakan di Canvas/Timeline Editor |
| Exported | File final sudah diekspor dari sistem |

---

## 19. Standar Format File

| Jenis | Format Didukung |
|---|---|
| Gambar (Carousel/Feed) | PNG, JPG, WEBP, PDF (gabungan) |
| Video (Pendek/Panjang) | MP4, WEBM |
| Audio (Voiceover) | MP3 |

---

## 20. Roadmap Ekspansi Platform

1. **Fase 1 (Rilis Awal)**: Gambar (Single Post & Carousel) + Video Pendek (9:16) — seluruh modul (Riset s/d Editor) aktif penuh untuk kedua jenis konten ini.
2. **Fase 2**: **Video Panjang (16:9, long-form)** diaktifkan — struktur skrip & timeline sudah disiapkan sejak awal.
3. **Fase 3**: **Agency Mode** dapat diaktifkan jika model multi-workspace siap.
4. **White-label**: full product distribution layer disiapkan pada core tetapi dibangun pada project/fase terpisah setelah platform utama stabil.
5. **Global Expansion**: bahasa, currency, payment provider, dan market tambahan diaktifkan sesuai prioritas ekspansi.

---

## Lampiran — Ringkasan Keputusan Desain Kunci

| Area | Keputusan |
|---|---|
| Alur inti | Platform-agnostic penuh: Planner & Analyzer tidak memilih platform, hanya jenis konten (Gambar/Video) |
| Analyzer | Content Intelligence Layer: sumber → evidence/claim → opportunity → angle tervalidasi. 1 sumber bisa hasilkan beberapa angle (dikelompokkan per Angle Family); tiap angle bisa cocok Gambar dan/atau Video (non-eksklusif), member pilih multi |
| **Analyzer — Default vs Add-on** | **Default (semua tier, gratis)**: identitas & klasifikasi sumber, quality gate dasar, duplicate detection, structured extraction (fact/opinion split), evidence ringan, angle generation, Virality Potential Score, aturan anti-halusinasi. **4 Add-on independen** (menggunakan entitlement yang tersedia, atau default per role via admin): Multi-AI (jumlah AI + full comparison), Deep Source Intelligence (Source Quality/Content Value/Transformation Score, claim registry & risk, novelty/tension, angle qualification & content readiness gate), Media Intelligence (visual/audio/quote intelligence), Cross-Source Analysis (2+ sumber sekaligus) |
| **Output analisa (default)** | Ringkasan, core facts, key points, tone/sentiment, data pendukung, target audience fit, opsi hook/judul (jumlah menyesuaikan kekayaan sumber, AI dilarang mengarang demi kuantitas), saran content pillar, alasan diferensiasi antar-angle, evidence reference ringan (paragraf/timestamp/halaman) |
| **Virality Potential Score** | (dulu "Skor Potensi Viral") Elemen paling menonjol di tiap kartu Angle — dipecah per faktor (hook, curiosity gap, trigger emosional, relatability, share motivation, novelty, utility, tension, kesesuaian tren, kesesuaian format-algoritma, audience fit), kartu diurutkan otomatis dari skor tertinggi; selalu diiringi 3 skor lain (Source Quality, Content Value, Transformation Potential) bila Add-on Deep Source Intelligence aktif |
| **Modul 3 (Review Skrip & Visual)** | Direposisi jadi *production layer* (Content Production Blueprint), bukan tempat analisa ulang — mewarisi opportunity/angle/evidence/hook dari Analyzer via Input Contract (§8-B), angle terkunci (§8-C), evidence & claim risk diwariskan ke script (§8-F–§8-G), structured prompt & editor mapping default (§8-I–§8-J), Production QA 5 dimensi sebelum lanjut ke Asset Preparation (§8-K). 2 Add-on opsional: Visual Continuity Engine, Advanced Prompt Studio & Auto-Fix (§8-M–§8-N) |
| Generate aset | Modul 4 (Asset Preparation) tersendiri, sebelum Editor; checklist otomatis dari `asset_requirements` Blueprint Modul 3 |
| Upload media sendiri | Tersedia sebagai alternatif generate AI di Modul 4, diproses client-side, tidak memotong kuota, tidak masuk retensi server kecuali dibawa ke Editor |
| Template | Dikelola admin (Global Template Manager), member pilih & bebas edit di Editor |
| Persistensi data | Hasil Riset & Analyzer tersimpan otomatis, tidak hilang saat pindah halaman/refresh/logout; hanya hilang lewat tombol Reset per modul atau analisa/riset baru |
| Multi-klien/multi-workspace | Foundation disiapkan di core; Agency/White-label full product berada di fase/project terpisah |
| Entitlement vs Wallet | Fitur inti mengikuti membership/role; generate berat memakai entitlement. Tidak ada Top Up/PAYG Wallet untuk member biasa. |
| Preview vs final | Preview generate gratis; entitlement hanya dikonsumsi saat finalisasi sesuai policy Asset Preparation |
| Kuota/Entitlement habis | Member membeli Product Package/Add-on Package yang tersedia; tidak ada Top Up/PAYG Wallet. |
| Jumlah API Pool | 5 pool (Text/Analysis, Image, Video, Voice, Research & Data); Image & Video Pool dipecah jalur Included vs Add-on vs Preview |
| Sumber data riset | YouTube Data API v3 resmi untuk data YouTube; IG & TikTok wajib provider pihak ketiga |
| Manajemen keuangan admin | Finance Center terpisah: revenue dashboard, cost tracking per jalur, profit margin monitor, ledger, payment gateway, refund, referral payout |
| Modul Riset | Modul 0 (§5) — Competitor Tracker, Trend Explorer, Keyword/Hashtag Research, Audience Insight, Mood Board |
| Modul Analytics | Modul penutup siklus (§13) — evaluasi performa, insight mengalir balik ke Riset & Planner |
| Status konten | Draft → Source Needed → Script Ready → Asset Ready → Editing → Exported |
| Frekuensi posting | 1x / 2x / 3x per hari |
| Format ekspor | PNG, JPG, WEBP, PDF (gambar); MP4, WEBM (video); MP3 (audio) |
| Prinsip pemrosesan | Client-side untuk editing ringan & upload mentah; server-side hanya untuk generate AI & render final |
| Core-ready cross-cutting | Single Login, i18n ID/EN + future languages, IDR/USD, configurable provider pools, content protection, tenant/white-label foundation; detail teknis dipisahkan ke dokumen core |
| Roadmap | Berbasis jenis konten (Gambar+Video Pendek → Video Panjang → Agency/White-label → Global Expansion) |
