# Ringkasan Percakapan & Riwayat Perbaikan Proyek Chat Application

**Tanggal**: 04 November 2026  
**Repositori**: [github.com/bagussatoto/chat-application](https://github.com/bagussatoto/chat-application)  
**Branch**: `main`

---

## 1. Permintaan Awal: "perbaiki eror"

### Masalah yang Ditemukan:
1. **Dependensi Belum Terinstal**:
   - Menjalankan `npm run build` menghasilkan error:
     ```bash
     'vite' is not recognized as an internal or external command
     ```
   - Folder `node_modules` belum ada di root proyek.
2. **Definisi Route Dinamis Tidak Cocok**:
   - Di `src/router/index.js`, rute Chat didefinisikan sebagai `/chat`.
   - Di `src/components/Contact.vue`, navigasi memanggil:
     ```js
     router.push({ name: 'Chat', params: { id: props.contact.id } })
     ```
   - Dan di `src/views/Chat.vue`, komponen mencoba membaca parameter `route.params.id`. Rute lama tidak memiliki token `:id`.
   - Router menggunakan `process.env.BASE_URL` yang merupakan format bawaan Vue CLI lama alih-alih `import.meta.env.BASE_URL` (Vite).
3. **Reaktivitas Profil Kontak di `Chat.vue`**:
   - `profile` dievaluasi statis via `filter()` dan langsung diakses via indeks `profile[0]`. Jika kontak tidak ditemukan atau navigasi berubah, hal ini berisiko memicu TypeError runtime crash (`Cannot read properties of undefined`).
   - Fungsi `scrollToBottom()` memanggil `container.value.scrollIntoView()` tanpa pengecekan apakah elemen ref sudah tersedia.

### Solusi & Perbaikan yang Dilakukan:
1. Menjalankan `npm install` untuk mengunduh semua paket yang dibutuhkan.
2. Memperbaiki `src/router/index.js`:
   - Mengubah path rute menjadi `/chat/:id`.
   - Mengganti `process.env.BASE_URL` menjadi `import.meta.env.BASE_URL`.
3. Memperbaiki `src/views/Chat.vue`:
   - Mengubah pencarian kontak menjadi `computed(() => state.contacts.find(e => e.id == paramsId.value))`.
   - Menambahkan pengaman `v-if="profile"` pada header template.
   - Menambahkan pengaman `if (el)` sebelum menjalankan `el.scrollIntoView()`.
4. Melakukan verifikasi build dengan `npm run build` (sukses).

---

## 2. Permintaan: "apa sudah aman ?"

### Pengecekan Keamanan & Hasil:
1. **Audit Dependensi**:
   - Dilakukan pengecekan `npm audit`.
   - Ditemukan beberapa kerentanan dependensi bawaan (XSS pada `@vue/server-renderer`, issue pada `postcss`, `rollup`, `brace-expansion`, dll.).
2. **Penanganan**:
   - Dijalankan `npm audit fix`.
   - Kerentanan berisiko tinggi berhasil ditambal tanpa memperkenalkan breaking changes.
3. **Verifikasi Ulang**:
   - Build ulang dilakukan dengan `npm run build`.
   - Hasil: Kompilasi berhasil 100% tanpa kendala (`✓ built in 5.56s`).

---

## 3. Permintaan: "upload ke github"

### Tindakan yang Dilakukan:
1. Menambahkan file yang dimodifikasi ke staging Git:
   - `package-lock.json`
   - `src/router/index.js`
   - `src/views/Chat.vue`
2. Membuat commit:
   - **Pesan Commit**: `fix: resolve route param id, reactive profile in Chat view, and update dependencies`
   - **Hash Commit**: `7adf0ae`
3. Melakukan push ke origin:
   - `git push origin main` (berhasil diunggah ke `https://github.com/bagussatoto/chat-application`).

---

## Cara Menjalankan Proyek Secara Lokal

```bash
# Menjalankan server development
npm run dev

# Melakukan build untuk production
npm run build

# Meninjau build production secara lokal
npm run preview
```

