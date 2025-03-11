## BAGIAN 1: Kolaborasi di Proyek Python Dengan Git & GitHub

### 1. Setup SSH Sebelum Mulai ⚙️ (Bisa diskip kalau udah ada ssh)

#### 1.1 Setup SSH di Windows

1. **Buka Git Bash**, buat kunci SSH:
   ```
   ssh-keygen -t ed25519 -C "emailanda@example.com"
   ```
   - Lokasi default: `C:\Users\<nama_user>\.ssh\id_ed25519`
   - Bisa memberi **passphrase** (disarankan demi keamanan).
2. **Daftarkan kunci ke GitHub**:
   - Buka [GitHub Settings → SSH and GPG Keys](https://github.com/settings/keys).
   - Klik **New SSH key**, tempel isi file `id_ed25519.pub`.
3. **Tes koneksi**:
   ```
   ssh -T git@github.com
   ```
   Jika berhasil, akan muncul pesan “You've successfully authenticated...”.

#### 1.2 Setup SSH di Ubuntu

1. **Buka Terminal**, buat kunci SSH:
   ```bash
   ssh-keygen -t ed25519 -C "emailanda@example.com"
   ```
   - Lokasi default: `~/.ssh/id_ed25519` (privat) & `~/.ssh/id_ed25519.pub` (publik).
   - **Passphrase** opsional, tapi disarankan demi keamanan.
2. **Daftarkan kunci ke GitHub**:
   - Salin isi kunci publik:
     ```bash
     cat ~/.ssh/id_ed25519.pub
     ```
   - Buka [GitHub Settings → SSH and GPG Keys](https://github.com/settings/keys), klik **New SSH key**, tempel kunci publik.
3. **Tes koneksi**:
   ```bash
   ssh -T git@github.com
   ```
   Muncul pesan sambutan menandakan sukses.

#### 1.3 Agar Tidak Perlu Memasukkan Passphrase Terus (Ubuntu)

1. **Aktifkan ssh-agent**:
   ```bash
   eval $(ssh-agent -s)
   ```
2. **Tambahkan kunci privat**:
   ```bash
   ssh-add ~/.ssh/id_ed25519
   ```
   Passphrase hanya diminta sekali. Selama sesi terminal/komputer tetap aktif, Anda tidak perlu memasukkan passphrase berulang.
3. **Otomatis saat login** (opsional):
   - Tambahkan di `~/.bashrc`:
     ```bash
     eval $(ssh-agent -s)
     ssh-add ~/.ssh/id_ed25519
     ```
     Sehingga setiap terminal baru akan menjalankan ssh-agent dan menambahkan kunci privat otomatis.

**HTTPS vs SSH?**

- **SSH**: Praktis setelah setup (apalagi dengan ssh-agent). Lebih aman karena menggunakan kunci kriptografi untuk autentikasi, mengurangi risiko pencurian kredensial dibandingkan dengan username/password.
- **HTTPS**: Harus memasukkan username/password atau token, kecuali memakai credential manager. Rentan terhadap serangan phishing dan pencurian kredensial jika tidak menggunakan token atau credential manager.

---

### 2. Alur Singkat Git & GitHub 🌀

#### 2.1 Buat Repository di GitHub 📦

- Klik **New**, beri nama (mis. `python-collab`).
- Pilih **Public** / **Private**.
- Salin URL SSH, misal `git@github.com:username/python-collab.git`.

#### 2.2 Clone ke Lokal 👨‍💻

```
git clone git@github.com:username/python-collab.git
cd python-collab
```

#### 2.3. Penjelasan Singkat Tentang Workflow Git (Sesuai Gambar)

![Workflow-Git](./workflow-git.png)

**Working Directory** → Tempat Anda mengedit file.  
**Staging Area** → Mengatur file mana saja yang siap di-_commit_ (`git add`).  
**Local Repo** → Semua commit disimpan di repository lokal (`git commit`).  
**Remote Repo** → GitHub/Server tempat commit dikumpulkan ketika di-_push_ (`git push`).

- _Pull_ (`git pull`) untuk menarik perubahan terbaru dari remote.
- _Checkout_ (atau `switch`) untuk berpindah branch atau membatalkan perubahan di file tertentu.

Secara ringkas:

1. Anda mengedit di **Working Directory**
2. `git add` → Masuk **Staging Area**
3. `git commit` → Masuk ke **Local Repository**
4. `git push` → Kirim ke **Remote Repository**
5. `git pull` → Ambil update dari **Remote**

#### 2.4 Tambah File Python & Commit 🐍

1. Buat `app.py`:
   ```python
   print("Hello from main branch!")
   ```
2. Commit awal:
   ```bash
   git add app.py
   git commit -m "Add simple app.py"
   git push origin main
   ```

#### 2.5 Menulis Pesan Commit dengan Baik 📑

Agar riwayat commit lebih rapi dan mudah dipahami, Anda bisa menggunakan format **Conventional Commits**. Beberapa tipe umum:

- **feat**: Penambahan fitur baru
- **fix**: Perbaikan bug
- **docs**: Perubahan dokumentasi
- **style**: Perubahan tampilan/format (tanpa mengubah kode)
- **refactor**: Perubahan kode yang tidak memperbaiki bug/menambah fitur
- **test**: Penambahan/perubahan terkait testing
- **chore**: Perubahan minor (misal perapian file, update dependency)

Contoh:

```bash
git commit -m "feat: add user input to app.py"
```

atau:

```bash
git commit -m "fix: resolve issue with input encoding"
```

---

#### 2.6 Branch dengan `git switch` 🌿

1. Buat branch `feature-1`:
   ```bash
   git switch -c feature-1
   ```
2. Ubah `app.py` (mis. tambah input):
   ```python
   name = input("Masukkan nama Anda: ")
   print(f"Hello, {name}!")
   ```
3. Commit & push:
   ```bash
   git add app.py
   git commit -m "feat: add user input"
   git push origin feature-1
   ```

#### 2.7 Pull Request & Merge 🤝

- Buka repo di GitHub, klik **Compare & pull request** → **Create PR** → **Merge**.
- (Opsional) Hapus branch `feature-1` setelah merge.

#### 2.8 Conflict Resolution ⚠️

- Jika ada perubahan di baris yang sama, merge akan menimbulkan conflict.
- Edit file, cari tanda `<<<<<<<`, `=======`, `>>>>>>>`, gabungkan manual.
- `git add`, `git commit -m "fix: resolve merge conflict"`, push, lalu merge.

#### 2.9 `git reset` & `git revert` ⏪

1. **`git reset`**
   - Memindahkan pointer _HEAD_ ke commit tertentu, menghapus commit setelahnya (lokal).
   - Contoh:
     ```bash
     git log --oneline
     git reset --hard <commit-id>
     ```
     - `--hard`: folder kerja ikut diubah.
     - `--soft`: HEAD saja, file di folder kerja tak berubah.
2. **`git revert`**
   - Membuat commit baru yang membatalkan perubahan commit lama.
   - Contoh:
     ```bash
     git revert <commit-id>
     ```
   - **Perbedaan**:
     - `reset` → Seolah commit dihapus dari riwayat (lokal).
     - `revert` → Riwayat tetap, tapi ada commit “balikan” untuk membatalkan commit target.

#### 2.10 Simpan Perubahan Sementara dengan `git stash` 💼

1. `git stash` untuk menyimpan perubahan yg belum di-commit.
2. `git stash list` untuk melihat daftar.
3. Kembalikan perubahan:
   ```
   git stash apply stash@{0}
   ```
   atau
   ```
   git stash pop
   ```

#### 2.11 Gunakan GitHub Projects 🗂️

- Masuk tab **Projects** di repo.
- Buat project (Kanban), kolom “To Do”, “In Progress”, “Done”.
- Tambahkan tasks (issue, PR), geser antar kolom sesuai progres.

---

## BAGIAN 2: Cara Mendapatkan GitHub Student Developer Pack 🎓

1. **Buka**: [GitHub Student Developer Pack](https://education.github.com/pack)
2. **Klik “Get student benefits”**
3. **Verifikasi** via **email kampus** atau **dokumen resmi** (foto KTM jelas).
4. **Tunggu** 2–7 hari. Jika disetujui, akun Anda menjadi **Pro** & mendapat banyak bonus (domain gratis, dsb.).

---

## BAGIAN 3: Demo GitHub Copilot & Shortcut di VS Code

![Meme](https://i.imgflip.com/7ea2zo.jpg)

### 1. GitHub Copilot

- **GitHub Copilot** adalah asisten pemrograman berbasis AI yang membantu Anda menulis kode lebih efisien dengan memberikan saran kode saat Anda mengetik.

- **Fitur Utama**:
  - **Inline Suggestions**: Copilot memberikan saran kode langsung saat Anda mengetik di editor. Anda dapat menerima, menolak, atau melihat saran berikutnya.
  - **Chat Interface**: Berinteraksi dengan Copilot melalui antarmuka chat untuk meminta bantuan, penjelasan kode, atau menghasilkan kode berdasarkan deskripsi.

### 2. Shortcut Penting di VS Code untuk GitHub Copilot

Berikut adalah beberapa shortcut yang dapat meningkatkan produktivitas Anda saat menggunakan GitHub Copilot di Visual Studio Code:

| Aksi                                  | Windows/Linux                  | macOS                                            |
| ------------------------------------- | ------------------------------ | ------------------------------------------------ |
| **Menerima saran inline**             | `Tab`                          | `Tab`                                            |
| **Menolak saran inline**              | `Esc`                          | `Esc`                                            |
| **Melihat saran berikutnya**          | `Alt` + `]`                    | `Option (⌥)` + `]`                               |
| **Melihat saran sebelumnya**          | `Alt` + `[`                    | `Option (⌥)` + `[`                               |
| **Memicu saran inline secara manual** | `Alt` + `\`                    | `Option (⌥)` + `\`                               |
| **Membuka panel saran**               | `Ctrl` + `Enter`               | `Control (⌃)` + `Enter`                          |
| **Memulai Chat Copilot**              | `Ctrl` + `Alt` + `I`           | `Control (⌃)` + `Command (⌘)` + `I`              |
| **Memulai Inline Chat**               | `Ctrl` + `I`                   | `Command (⌘)` + `I`                              |
| **Membuka Quick Chat**                | `Ctrl` + `Shift` + `Alt` + `L` | `Shift (⇧)` + `Option (⌥)` + `Command (⌘)` + `L` |

_Catatan: Shortcut dapat disesuaikan sesuai preferensi Anda melalui pengaturan keyboard shortcut di VS Code._ citeturn0search1

### 3. Menggunakan GitHub Copilot di VS Code

- **Menerima atau Menolak Saran**: Saat mengetik, Copilot akan memberikan saran dalam bentuk teks berwarna abu-abu. Tekan `Tab` untuk menerima atau `Esc` untuk menolak saran tersebut.

- **Memicu Saran Secara Manual**: Jika Copilot tidak memberikan saran otomatis, Anda dapat memicu saran secara manual dengan menekan `Alt` + `\` (Windows/Linux) atau `Option (⌥)` + `\` (macOS).

- **Menggunakan Inline Chat**: Untuk meminta bantuan atau penjelasan spesifik, gunakan shortcut `Ctrl` + `I` (Windows/Linux) atau `Command (⌘)` + `I` (macOS) untuk membuka Inline Chat dan mengirim permintaan langsung ke Copilot dari editor.

- **Mengakses Chat Copilot**: Untuk interaksi yang lebih luas, seperti diskusi atau permintaan kompleks, buka antarmuka Chat Copilot dengan menekan `Ctrl` + `Alt` + `I` (Windows/Linux) atau `Control (⌃)` + `Command (⌘)` + `I` (macOS).

---
