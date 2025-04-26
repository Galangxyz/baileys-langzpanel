# baileys-langzpanel

**baileys-langzpanel** adalah modifikasi dari [Baileys](https://github.com/WhiskeySockets/Baileys), sebuah library WhatsApp Web API berbasis Node.js yang mendukung multi-device. Proyek ini dibuat ulang agar lebih mudah digunakan dan dipahami, dengan penambahan dokumentasi, konfigurasi TypeScript, dan struktur yang rapi.

Proyek ini cocok untuk:
- Developer bot WhatsApp
- Integrator sistem (untuk login via WhatsApp, notifikasi dll)
- Belajar reverse engineering WhatsApp Web

## Instalasi

Untuk menggunakan `baileys-langzpanel`, cukup jalankan:

```bash
npm install baileys-langzpanel

Atau dengan Yarn:

yarn add baileys-langzpanel

Fitur Utama

Support Multi-Device: Kompatibel dengan sistem WhatsApp terbaru (tanpa QR berkala).

Written in TypeScript: Support untuk autocomplete dan type-checking yang lengkap.

Plug-and-play: Cukup install dan mulai coding, cocok untuk pemula.

WAProto dan SignalGroup: Sudah termasuk file protokol dan modul signal.

QR Scanner bawaan: Bisa langsung cetak QR ke terminal.


Struktur Direktori

baileys-langzpanel/
├── lib/                   # Hasil compile TypeScript
├── src/                   # Source code utama
├── WAProto/               # File protobuf WhatsApp
├── WASignalGroup/         # Implementasi signal untuk grup
├── Example/               # Contoh penggunaan langsung
├── session.json           # Tempat penyimpanan auth session
├── tsconfig.json          # Konfigurasi TypeScript
├── package.json
└── README.md

Contoh Penggunaan

import makeWASocket from 'baileys-langzpanel'
import { useSingleFileAuthState } from 'baileys-langzpanel/lib/Auth'
import Pino from 'pino'

const { state, saveState } = useSingleFileAuthState('./session.json')

const sock = makeWASocket({
  auth: state,
  printQRInTerminal: true,
  logger: Pino({ level: 'silent' }),
})

sock.ev.on('connection.update', (update) => {
  const { connection, lastDisconnect } = update
  if (connection === 'open') {
    console.log('Terhubung ke WhatsApp!')
  } else if (connection === 'close') {
    console.log('Terputus. Mencoba ulang...')
  }
})

sock.ev.on('messages.upsert', async (m) => {
  const msg = m.messages[0]
  if (!msg.message) return
  const sender = msg.key.remoteJid
  const text = msg.message.conversation || msg.message.extendedTextMessage?.text

  if (text === 'ping') {
    await sock.sendMessage(sender, { text: 'pong!' })
  }
})

Build Manual

Kalau kamu mau kontribusi atau memodifikasi source-nya, compile dulu dengan:

npm run build:tsc

Untuk membangun dokumentasi secara otomatis:

npm run build:docs

Cara Menyimpan Session Login

Gunakan useSingleFileAuthState untuk menyimpan session di file. Kamu bisa juga ganti dengan database jika lebih kompleks.

import { useSingleFileAuthState } from 'baileys-langzpanel/lib/Auth'

const { state, saveState } = useSingleFileAuthState('./auth_info.json')

Pastikan untuk memanggil saveState() saat koneksi atau session berubah agar data tersimpan.

Troubleshooting

Q: QR code tidak muncul?
A: Pastikan printQRInTerminal: true dan tidak ada session aktif sebelumnya.

Q: Koneksi terputus terus?
A: Cek apakah jaringan stabil dan pastikan nomor WA tidak logout dari perangkat.

Q: Saya ingin pakai database untuk auth?
A: Bisa! Tapi perlu sedikit modifikasi di auth handler-nya (multiFileAuthState, custom adapter, dll).

Kontribusi

Pull Request dan issue sangat diterima! Proyek ini terbuka untuk siapa saja yang ingin membantu memperbaiki bugs, menambah fitur, atau memperbarui dokumentasi.

Lisensi

MIT License
Dikembangkan oleh Galang
