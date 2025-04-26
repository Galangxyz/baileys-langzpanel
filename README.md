# baileys-langzpanel

**baileys-langzpanel** adalah modifikasi dari [Baileys](https://github.com/WhiskeySockets/Baileys), sebuah library WhatsApp Web API berbasis Node.js yang powerful.

Proyek ini cocok untuk:
- Developer bot WhatsApp
- Integrator sistem (untuk login via WhatsApp, notifikasi, dll)
- Belajar reverse engineering WhatsApp Web

---

## Instalasi

Untuk menggunakan `baileys-langzpanel`, cukup jalankan:

```bash
npm install baileys-langzpanel

Atau dengan Yarn:

yarn add baileys-langzpanel


---

Fitur Utama

Support Multi-Device: Kompatibel dengan sistem WhatsApp terbaru (tanpa QR berkala).

Written in TypeScript: Support untuk autocomplete dan type-checking yang lengkap.

Plug-and-play: Cukup install dan mulai coding, cocok untuk pemula.

WAProto dan SignalGroup: Sudah termasuk file protokol dan modul signal.

QR Scanner bawaan: Bisa langsung cetak QR ke terminal.



---

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


---

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


---

Fitur Tambahan Button Support

baileys-langzpanel menyediakan berbagai jenis tombol interaktif untuk memperkaya pengalaman pengguna bot, di antaranya:

1. CTA Copy Button (cta_copy)

Mengirim tombol yang ketika diklik langsung menyalin teks ke clipboard.

Contoh:

{
  name: "cta_copy",
  buttonParamsJson: JSON.stringify({
    display_text: "Copy Code",
    copy_code: "Ini adalah teks yang disalin"
  })
}


---

2. Hydrated Buttons

Tombol interaktif WhatsApp yang lebih fleksibel, seperti:

URL Button (membuka tautan)

Call Button (langsung menelepon)

Quick Reply Button (mengirim perintah otomatis)


Contoh:

hydratedButtons: [
  { urlButton: { displayText: "Kunjungi Website", url: "https://example.com" } },
  { callButton: { displayText: "Hubungi Kami", phoneNumber: "+62123456789" } },
  { quickReplyButton: { displayText: "Cek Status", id: ".status" } }
]


---

3. Legacy Buttons (Buttons Message)

Tipe tombol klasik dari WhatsApp, maksimal 3 pilihan, cocok untuk flow sederhana.

Contoh:

buttons: [
  { buttonId: ".list", buttonText: { displayText: "Lihat Produk" }, type: 1 },
  { buttonId: ".order", buttonText: { displayText: "Pesan Sekarang" }, type: 1 },
  { buttonId: ".info", buttonText: { displayText: "Informasi" }, type: 1 }
]


---

Dukungan Media

Semua jenis tombol di atas bisa disertai media gambar, baik dari file lokal atau URL:

Contoh upload gambar:

const media = await prepareWAMessageMedia({
  image: { url: './src/media/gambar.jpg' }
}, { upload: sock.waUploadToServer })


---

Metadata Pesan

baileys-langzpanel juga mendukung pengaturan metadata tambahan seperti:

externalAdReply

forwardedNewsletterMessageInfo

businessMessageForwardInfo

contextInfo untuk tag user (mention) atau promosi.



---

Catatan

Pastikan WhatsApp yang digunakan sudah versi terbaru (multi-device).

Perlu Node.js minimal v16.0.0 ke atas untuk performa terbaik.



---

Lisensi

MIT License


---

© 2025 LangzPanel – dibuat oleh Galangxyz
