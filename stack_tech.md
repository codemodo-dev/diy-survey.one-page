# Tech Stack — DIY Survey & Panel Platform

*v1.0.0 | 27 Februari 2026*

---

## Overview

Platform dibangun sebagai satu sistem monolith terintegrasi — satu Backend API dan satu Frontend — dengan tiga area utama: **Admin Panel** (Super Admin, Admin & Client), **Survey Designer**, dan **Survey Portal**. Arsitektur multi-tenant memastikan setiap Client hanya dapat mengakses data milik tenant-nya sendiri.

---

## 1. Backend

| Tool | Keterangan |
|------|------------|
| NestJS | Main framework |
| Node.js | Runtime |
| TypeScript | Primary language |
| MongoDB Atlas | Database utama — managed, di-host di AWS region |
| Redis (AWS ElastiCache) | Session store & caching |

---

## 2. Frontend

| Tool | Keterangan |
|------|------------|
| Next.js | Framework — satu aplikasi monolith untuk seluruh area platform |
| TypeScript | Primary language |
| Vercel | Hosting & deployment frontend |

Satu aplikasi di-deploy sebagai satu project di Vercel, dengan area yang dibedakan berdasarkan role:

| Area | Deskripsi |
|------|-----------|
| Admin Panel | Interface untuk Super Admin, Admin, dan Client — sidebar dan menu berbeda berdasarkan role |
| Survey Designer | Interface untuk membangun, publish, dan monitor survei — hanya Client |
| Survey Portal | Interface Panelist — registrasi dan pengisian survei |

---

## 3. Design

Biaya pembelian asset (icon, ilustrasi, stock image, dll) jika diperlukan ditanggung oleh client.

| Tool | Keterangan |
|------|------------|
| Figma | UI/UX design & prototyping |

---

## 4. Infrastructure (AWS)

Platform di-deploy di AWS region **ap-southeast-3 (Jakarta)** — dipilih untuk memastikan latency rendah bagi pengguna Indonesia dan memenuhi kebutuhan data residency lokal. Jika ada service yang tidak tersedia di Jakarta, fallback ke ap-southeast-1 (Singapore).

Infrastruktur dibangun di atas container-based architecture menggunakan ECS, sehingga platform dapat diskalakan sesuai pertumbuhan jumlah panelist dan survei aktif tanpa perubahan arsitektur yang signifikan.

| Service | Fungsi |
|---------|--------|
| ECS (Elastic Container Service) | Menjalankan container Backend API |
| ECR (Elastic Container Registry) | Registry Docker image |
| ALB (Application Load Balancer) | Load balancer & routing traffic ke ECS |
| S3 | Object storage — file export (CSV/Excel), asset user, dan asset survei |
| ElastiCache (Redis) | Session store & caching |
| SES (Simple Email Service) | Email transaksional |

### Setup & Biaya Server

Server production disediakan oleh client. Tim pengembang bertanggung jawab atas setup dan konfigurasi kedua environment.

| Environment | Setup & Konfigurasi | Biaya Server |
|-------------|---------------------|--------------|
| Development | Tim pengembang | Tim pengembang |
| Production | Tim pengembang | Client |

---

## 5. CI/CD

| Tool | Fungsi |
|------|--------|
| GitHub | Source control & code repository |
| GitHub Actions | Pipeline otomatis — build, test, dan deploy |

---

## 6. Services & Dependencies

Seluruh service yang digunakan platform — baik infrastruktur maupun third-party. Service dengan status **Required** wajib tersedia. **Optional** dapat ditambahkan sesuai kebutuhan.

Jika ada service yang ingin diganti atau tidak digunakan, mohon dikonfirmasi sebelum pengerjaan dimulai. Perubahan service di tengah pengerjaan dapat mempengaruhi timeline dan scope.

| Service | Fungsi | Status |
|---------|--------|--------|
| MongoDB Atlas | Managed database | Required |
| AWS ECS | Menjalankan container Backend API | Required |
| AWS ECR | Registry Docker image | Required |
| AWS ALB | Load balancer & routing traffic | Required |
| AWS S3 | Object storage — export, asset user & survei | Required |
| AWS ElastiCache (Redis) | Session store & caching | Required |
| AWS SES | Email transaksional | Required |
| Vercel | Hosting & deployment frontend | Required |
| GitHub | Source control & CI/CD pipeline | Required |
| Xendit | Payment gateway — pencairan insentif panelist | Required |
| Google SSO | OAuth 2.0 — login di semua area platform | Required |
| Sentry.io | Error tracking & monitoring (backend & frontend) | Optional |

---

## 7. Estimasi Biaya Server — Production

Semua harga dalam USD, region **ap-southeast-3 (Jakarta)**. Estimasi bersifat kasar — angka aktual tergantung traffic dan usage nyata. Kurs referensi: **1 USD = Rp 16.800** (27 Feb 2026, mid-market rate).

### Skenario

| Parameter | Low | Rekomendasi |
|-----------|-----|-------------|
| Panelist aktif/bulan | ~1.000 | ~5.000 |
| Survei aktif serentak | ~5 | ~20 |

### Estimasi Biaya Bulanan

| Service | Low | Rekomendasi |
|---------|-----|-------------|
| **AWS ECS (EC2 t3.medium)** | ~$30 | ~$60 |
| **AWS ALB** | — | ~$22 |
| **MongoDB Atlas (M10)** | ~$57 | ~$57 |
| **AWS ElastiCache (Redis)** | ~$12 | ~$25 |
| **AWS S3** | ~$3 | ~$10 |
| **AWS SES** | ~$1 | ~$3 |
| **AWS ECR** | ~$5 | ~$5 |
| **Vercel (Pro)** | ~$20 | ~$20 |
| **Sentry.io** | ~$26 | ~$26 |
| **Total/Bulan** | **~$154** | **~$228** |
| **Total/Tahun** | **~$1.848** | **~$2.736** |
| **Total/Bulan (IDR)** | **~Rp 2.587.000** | **~Rp 3.830.000** |
| **Total/Tahun (IDR)** | **~Rp 31.046.000** | **~Rp 45.965.000** |