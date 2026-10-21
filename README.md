# PAI City Quest 3D — Aplikasi React + EduWorld

Aplikasi utama adalah PAI City Quest 3D, sebuah game belajar PAI kelas 1–6
berbasis React, Three.js, dan Firebase. Host dapat memilih kelas, mengaktifkan
soal untuk setiap sesi, dan siswa hanya menerima soal yang sesuai dengan kelas
serta pilihan tersebut. Bank kelas 1–5 berisi pertanyaan orisinal yang disusun
berdasarkan topik buku PAI semester 1; bank kelas 6 mempertahankan soal yang
sudah tersedia. Prototipe Petualangan EduWorld berbasis
Phaser tetap tersedia sebagai halaman terpisah.

Di dalam dunia 3D, pemain dapat memilih lima suasana peta: Kota Ceria, Kota
Senja, Malam Berbintang, Oasis Gurun, dan Negeri Salju. Pilihan ini mengubah
tampilan lingkungan tanpa mengubah distrik maupun misi. Menu suasana dan
teleportasi peta hanya tersedia untuk host/guru. Siswa melihat informasi materi
saat berada di dekat distrik dan dapat memulai misi dari panel lokasi. Kota juga
dilengkapi burung, kucing, taman bunga, kolam air mancur, lima tank, serta piring
terbang yang bisa dinaiki menggunakan tombol saat pemain berada di dekatnya.
Tank dapat menembakkan roket yang mengejar UFO. Pemain dalam mode terbang juga
dapat meluncurkan roket melalui tombol layar atau tombol F. Lima UFO berpatroli
ke seluruh peta, masing-masing terbang maksimal 25 detik setiap giliran, menyorot
area di bawahnya, dan menembakkan proyektil. UFO yang terkena roket meledak dan
kembali berpatroli setelah 5 menit. Pemain yang terkena proyektil UFO tampak
hangus selama 10 detik sebelum pulih.

Musik `The Orchard Path` diputar sebagai backsound ketika pemain memasuki dunia
3D. Jika browser menahan autoplay, ketuk tombol **Aktifkan Backsound** yang
muncul di layar.

## Menjalankan aplikasi utama

1. Pasang Node.js, lalu jalankan `npm install` dari folder proyek.
2. Jalankan `npm run dev` dan buka alamat lokal yang ditampilkan Vite.
3. Untuk mode Firebase, salin `.env.example` menjadi `.env.local`, lalu isi
   `VITE_FIREBASE_CONFIG` dengan konfigurasi Firebase web milik proyek Anda.
   Tanpa konfigurasi, aplikasi berjalan dalam mode demo lokal. Firebase
   Authentication anonim dan aturan Firestore yang sesuai perlu diaktifkan
   untuk sinkronisasi realtime.
4. Jalankan `npm run build` untuk membuat versi produksi di `dist/`.

Contoh penyiapan lokal di PowerShell:

```powershell
Copy-Item .env.example .env.local
npm install
npm run dev
```

Jangan memasukkan kredensial privat atau service-account ke konfigurasi browser.
Konfigurasi Firebase web digunakan di sisi klien; batasi akses dengan Firebase
Security Rules.

## Mempublikasikan gim agar bisa diakses orang banyak

Firebase Hosting sudah dikonfigurasi untuk menerbitkan hasil build dari folder
`dist/`. Setelah dipublikasikan, bagikan URL Hosting yang diberikan Firebase;
jangan membagikan alamat `localhost`, karena alamat tersebut hanya berlaku di
perangkat sendiri.

1. Buat proyek di [Firebase Console](https://console.firebase.google.com/), lalu
   tambahkan aplikasi Web Firebase. Salin objek konfigurasi aplikasi Web.
2. Di Firebase Console, aktifkan **Authentication > Sign-in method > Anonymous**
   dan buat database **Cloud Firestore**.
3. Buat file `.env.local` di folder proyek. Isi menggunakan nilai dari
   konfigurasi aplikasi Web Anda:

   ```dotenv
   VITE_APP_ID=pai-city-quest-3d
   VITE_FIREBASE_CONFIG={"apiKey":"GANTI_DENGAN_NILAI_FIREBASE","authDomain":"GANTI_DENGAN_NILAI_FIREBASE","projectId":"GANTI_DENGAN_PROJECT_ID","storageBucket":"GANTI_DENGAN_NILAI_FIREBASE","messagingSenderId":"GANTI_DENGAN_NILAI_FIREBASE","appId":"GANTI_DENGAN_NILAI_FIREBASE"}
   ```

   Ganti setiap nilai contoh dengan nilai sebenarnya. Jangan pernah menaruh
   service-account, private key, atau kredensial admin di aplikasi web.
4. Dari PowerShell di folder proyek, jalankan:

   ```powershell
   npx firebase-tools login
   npx firebase-tools use --add
   npm run build
   npx firebase-tools deploy --only hosting
   ```

   Saat `use --add` bertanya, pilih proyek Firebase yang dibuat pada langkah 1.
   Setelah deploy berhasil, Firebase menampilkan URL publik, biasanya
   `https://PROJECT_ID.web.app`.
5. Uji URL tersebut dari perangkat lain atau jaringan seluler. Untuk sesi
   bersama, pastikan status gim menunjukkan **Multiplayer Cloud Aktif**.

Publikasi situs membuat halaman gim dapat dibuka oleh siapa pun yang memiliki
tautannya. Itu berbeda dari keamanan data multiplayer: sebelum mengaktifkan
room untuk umum, atur Firestore Security Rules agar pengguna tidak dapat
mengubah sesi atau data pemain lain. Jangan memakai aturan `allow read, write:
if true` atau aturan sementara yang membuka seluruh database. Mode demo lokal
tetap dapat dimainkan tanpa Firebase, tetapi progres dan sesi tidak dibagikan
antarperangkat.

## Mempublikasikan gim gratis melalui GitHub Pages

Workflow GitHub Actions tersedia di
`.github/workflows/deploy-pages.yml`. Setiap kali ada perubahan yang dikirim ke
branch `main`, workflow membangun gim dan menerbitkannya ke GitHub Pages.

1. Buat repository baru di GitHub. Untuk membuat gim dapat diakses umum, pilih
   visibilitas **Public** dan gunakan nama repository biasa, misalnya
   `pai-city-quest`.
2. Unggah kode proyek ke repository. Sertakan folder `src`, `public`, `docs`,
   file `index.html`, `eduworld.html`, `styles.css`, `package.json`,
   `package-lock.json`, `vite.config.ts`, `firebase.json`, dan folder
   `.github/workflows`. Jangan unggah `node_modules`, `dist`, atau `.env.local`.
3. Jika menggunakan GitHub melalui browser, pilih **Add file > Upload files**,
   seret file proyek tersebut, lalu simpan perubahan ke branch `main`.
4. Buka repository **Settings > Pages**. Pada **Build and deployment**, pilih
   **Source: GitHub Actions**.
5. Buka tab **Actions**. Tunggu workflow **Deploy game to GitHub Pages** selesai
   dengan status hijau. URL publik akan terlihat pada bagian Pages atau detail
   deployment, biasanya
   `https://NAMA-AKUN.github.io/NAMA-REPOSITORY/`.
6. Setelah perubahan berikutnya, unggah atau push perubahan ke branch `main`;
   workflow akan memperbarui situs secara otomatis.

Vite dikonfigurasi oleh workflow untuk repository Pages yang memakai alamat
`/.../NAMA-REPOSITORY/`, termasuk logo, gambar latar, backsound, dan prototipe
EduWorld.

GitHub Pages hanya menerbitkan situs statis; ia tidak menyediakan database
multiplayer. Tanpa Firebase, gim tetap terbuka melalui URL publik, tetapi
permainan bersama lintas perangkat tidak aktif. Untuk multiplayer, buat
konfigurasi Firebase web di **Settings > Secrets and variables > Actions**:

- Tambahkan repository secret bernama `VITE_FIREBASE_CONFIG`, berisi objek
  konfigurasi Firebase Web sebagai JSON satu baris.
- Opsional: tambahkan repository variable `VITE_APP_ID` untuk ID ruang data
  Firebase yang ingin digunakan.
- Tambahkan domain `NAMA-AKUN.github.io` ke **Firebase Authentication >
  Settings > Authorized domains**.
- Aktifkan Anonymous Authentication dan siapkan Cloud Firestore. Rancang
  Firestore Security Rules untuk membatasi siapa yang dapat membuat/mengubah
  sesi dan data pemain sebelum mengundang murid.

Konfigurasi Firebase Web bukan service-account. Jangan pernah menyimpan
service-account, private key, atau kredensial admin di repository atau GitHub
Actions. Jangan membuka semua akses Firestore dengan aturan publik
`allow read, write: if true`.

## Prototipe EduWorld (Phaser)

Buka `eduworld.html` melalui server statis untuk memainkan prototipe EduWorld.
Di dalam proyek ini halaman tersebut tetap memakai `styles.css`, Phaser dari
CDN, dan modul `src/main.js`.

Mode EduWorld adalah demo lokal untuk satu pemain. Nama dan progres hanya
digunakan selama sesi di browser. Room multiplayer dan penyimpanan progres
bersama belum diimplementasikan.

## Struktur aplikasi

```text
index.html                 Entry point aplikasi React PAI City Quest
eduworld.html               Entry point prototipe Phaser EduWorld
src/App.tsx                 Game PAI City Quest 3D
src/main.tsx                Bootstrap React
src/index.css               Tailwind CSS dan gaya global
vite.config.ts              Konfigurasi Vite, React, dan Tailwind
styles.css                  Gaya responsif prototipe EduWorld
src/
├── main.js                Bootstrap Phaser dan jembatan UI/kuis
├── data/
│   ├── asset-manifest.json
│   └── questions.js       Data contoh kuis Taman Ceria
└── scenes/
    ├── GameScene.js       Arena, gerakan, NPC, misi, dan HUD
    └── PreloadScene.example.js
```

## Pengembangan berikutnya untuk prototipe EduWorld

- Ganti grafis placeholder dengan tilemap dan sprite dari `public/assets/`.
- Tambahkan arena, misi, soal, dan progres persisten.
- Integrasikan backend realtime (misalnya Firebase) dengan autentikasi guru,
  aturan database, validasi jawaban tepercaya, room code, serta pemulihan koneksi.
- Bangun lobi dan dashboard host setelah layanan backend tersedia.

Template aset, konvensi nama file, ukuran yang disarankan, dan contoh pemuatan
Phaser 3 tetap didokumentasikan di bawah.

## Struktur utama

```text
public/assets/
├── characters/
│   ├── players/             # Avatar murid satu per file PNG
│   ├── npcs/                # Guru, petani, nelayan, warga, pedagang
│   └── spritesheets/        # Sprite sheet animasi pemain/NPC
├── arenas/
│   ├── taman-ceria/
│   │   ├── tiles/           # Tile tanah, rumput, air, jalan
│   │   └── objects/         # Pohon, bangku, kolam, bunga
│   ├── sekolah-pintar/
│   ├── kebun-pengetahuan/
│   ├── pantai-petualangan/
│   ├── lapangan-juara/
│   └── kampung-harmoni/
├── ui/
│   ├── buttons/
│   ├── icons/
│   └── panels/
├── items/
│   ├── collectibles/        # Koin, bintang, permata, kunci
│   └── rewards/             # Piala, lencana, hadiah
├── effects/                 # Sparkle, level-up, jawaban benar/salah
└── audio/
    ├── music/
    └── sfx/

src/
├── scenes/                  # BootScene, PreloadScene, MainMenuScene, GameScene
├── entities/                # Player, NPC, Collectible
├── systems/                 # Multiplayer, quiz, room, scoring
└── data/                    # arena.json, questions.json, asset-manifest.json
```

## Konvensi nama file

Gunakan huruf kecil, tanpa spasi, dan tanda hubung:
- `player-boy-01.png`
- `player-girl-hijab-01.png`
- `npc-guru.png`
- `taman-ceria-tiles.png`
- `taman-ceria-objects.png`
- `ui-button-primary.png`
- `coin-gold.png`
- `fx-correct-answer.png`

## Ukuran dan format yang disarankan

- Karakter: frame 32×32 atau 48×48 px; PNG transparan.
- Tilemap: tile 16×16 atau 32×32 px; pilih satu ukuran utama dan konsisten.
- Sprite sheet: grid frame konsisten, tanpa jarak antar-frame kecuali dikonfigurasi.
- UI: PNG transparan; gunakan SVG hanya untuk ikon yang memang berbasis vektor.
- Peta arena: JSON dari Tiled Map Editor (`.json`) dan tileset PNG.
- Audio: OGG atau MP3 untuk musik, WAV/OGG untuk efek suara.

Catatan: gambar referensi gabungan bukan sprite sheet produksi. Potong aset secara manual atau hasilkan aset terpisah sebelum dipakai sebagai sprite/tileset.

## Contoh pemuatan Phaser 3

```js
// Dalam PreloadScene.js
this.load.spritesheet("player-boy", "assets/characters/spritesheets/player-boy.png", {
  frameWidth: 32,
  frameHeight: 32
});

this.load.image("taman-tiles", "assets/arenas/taman-ceria/tiles/taman-ceria-tiles.png");
this.load.tilemapTiledJSON("taman-map", "assets/arenas/taman-ceria/taman-ceria-map.json");
this.load.image("coin-gold", "assets/items/collectibles/coin-gold.png");
```

Sesuaikan path relatif dengan konfigurasi server dan lokasi `public/` proyek Anda.

## Checklist sebelum game dijalankan

1. Masukkan file PNG nyata ke folder terkait.
2. Pastikan karakter dan objek yang perlu transparan memiliki alpha channel.
3. Pastikan ukuran frame sprite sheet konsisten.
4. Buat tilemap JSON melalui Tiled Map Editor dan cocokkan nama tileset.
5. Perbarui `src/data/asset-manifest.json` bila nama file berubah.
6. Uji path aset melalui dev server; jangan membuka `index.html` langsung jika memakai modul atau backend.
