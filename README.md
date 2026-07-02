# ✈️ Expense Dinas Tracker

Aplikasi web untuk mencatat dan melacak pengeluaran perjalanan dinas dengan mudah. Build dengan HTML/CSS/JavaScript vanilla, 100% offline, no backend required.

## ✨ Fitur Utama

- 📷 **Foto Bukti** - Tangkap bukti pengeluaran dengan timestamp dan geolocation otomatis
- 💰 **Budget Tracking** - Monitor pengeluaran real-time dengan progress bar
- 🎯 **Dynamic Cap** - Limit budget per kategori menyesuaikan dengan jumlah engineer
- 📊 **Analytics** - Breakdown pengeluaran per kategori dan per trip
- 📄 **PDF Export** - Download laporan lengkap dengan semua foto bukti
- 📱 **Offline First** - Semua data tersimpan lokal, 100% private
- 🔐 **No Backend** - Tidak ada server, tidak ada tracking
- ✅ **Responsive** - Optimal untuk mobile, tablet, desktop

## 🚀 Quick Start

1. **Buka aplikasi** → [https://kyretium.github.io/Pub/](https://kyretium.github.io/Pub/)
2. **Baca panduan** → [https://kyretium.github.io/Pub/guide.html](https://kyretium.github.io/Pub/guide.html)
3. **Buat trip** → Klik "+ Trip" dan isi informasi
4. **Tambah pengeluaran** → Input dengan kategori dan foto bukti
5. **Export PDF** → Download laporan untuk submit

## 📋 Kategori Pengeluaran

| Kategori | Cap Per Hari | Keterangan |
|----------|-------------|-----------|
| 🚗 Transport Dalam Kota | 75.000 × engineer | Taksi, ojek, bus lokal |
| 🍽️ Food & Beverage | (Staff × 125.000) + (Manager × 200.000) | Makanan dan minuman |
| 🛣️ Transport ke Tujuan | 120.000 × engineer | Pesawat, kereta, antar kota |
| 🚆 Overhead | Unlimited | Biaya tambahan |
| 🎉 Entertain | Unlimited | Hiburan, acara |

## 💡 Contoh Penggunaan

**Skenario: Dinas Jakarta 2 Staff**
- Budget total: 5.000.000
- Staff: 2 orang
- Manager: 0 orang

**Cap per hari:**
- Transport Dalam Kota: 75.000 × 2 = 150.000
- Food: 125.000 × 2 = 250.000
- Transport ke Tujuan: 120.000 × 2 = 240.000

**Contoh input hari pertama:**
- Taksi bandara: 200.000 (transport - melebihi cap? Alert!)
- Makan pagi: 50.000
- Makan siang: 80.000
- Makan malam: 70.000 (total food 200.000 - OK)

## 📸 Fitur Foto Bukti

Saat upload foto, aplikasi otomatis mencatat:
- ⏰ **Timestamp** - Tanggal dan jam foto diambil
- 📍 **Geolocation** - Koordinat GPS (jika device support + izin diberikan)

**Support di berbagai device:**
- ✅ iPhone/Android dengan GPS → Koordinat + Timestamp
- ⚠️ iPad WiFi → Timestamp saja (GPS tidak tersedia)
- ⚠️ Desktop → Timestamp saja (GPS tidak tersedia)
- ⚠️ Browser tanpa izin → Timestamp saja

## 📄 PDF Export

Export laporan mencakup:
1. Header (nama trip, tujuan, karyawan, tanggal cetak)
2. Summary (budget, terpakai, sisa, jumlah item)
3. Tabel pengeluaran (tanggal, keterangan, kategori, nominal)
4. Bukti foto (foto + metadata)

File naming: `Laporan_[TripName]_[TodayDate].pdf`

## 🔒 Privacy & Security

- ✅ **100% Offline** - Semua data tersimpan lokal di browser
- ✅ **No Backend** - Tidak ada server yang menyimpan data
- ✅ **No Tracking** - Tidak ada analytics atau tracking pixel
- ✅ **No Login** - Tidak perlu account atau sign-up
- ✅ **Private** - Data 100% private dan tidak dibagikan

## 🛠️ Teknologi

- **Frontend**: HTML5, CSS3, Vanilla JavaScript
- **Storage**: Browser LocalStorage API
- **PDF**: jsPDF library
- **Hosting**: GitHub Pages

## 📱 Browser Support

- ✅ Chrome/Edge (latest)
- ✅ Firefox (latest)
- ✅ Safari (latest)
- ✅ Mobile Browser (iOS Safari, Chrome Mobile)

## 📖 Dokumentasi Lengkap

Baca panduan interaktif di: [guide.html](guide.html)

Topics:
- [Quick Start](guide.html#quick-start)
- [Fitur Utama](guide.html#features)
- [Panduan Detail](guide.html#detailed-guide)
- [Foto & Geolocation](guide.html#photo-guide)
- [PDF Export](guide.html#pdf-export)
- [FAQ](guide.html#faq)
- [Tips & Trik](guide.html#tips)
- [Troubleshooting](guide.html#troubleshooting)

## ❓ FAQ

**Q: Bagaimana kalau data hilang?**
A: Export PDF sebelum clear browser cache. Selalu backup data penting.

**Q: Bisa share data dengan orang lain?**
A: Saat ini hanya bisa via PDF export. Cloud sync sedang dalam development.

**Q: Gimana kalau storage penuh?**
A: Clear data lama atau gunakan device dengan storage lebih besar.

**Q: Kenapa harus upload foto?**
A: Foto bukti mencegah pemalsuan pengeluaran dan membantu verifikasi.

## 🐛 Report Issues

Found a bug? Buka issue di GitHub: [Issues](https://github.com/kyretium/Pub/issues)

## 📝 Changelog

### v2.0 (Latest)
- ✨ Tambah geolocation untuk foto bukti
- ✨ Tambah timestamp otomatis saat foto diambil
- ✨ Tambah kategori "Transport ke Tempat Tujuan"
- ✨ Tambah level staff/manager untuk Food category
- ✨ Dynamic budget cap berdasarkan jumlah engineer
- 📖 Tambah guide.html dokumentasi lengkap

### v1.0
- Initial release
- Expense tracking dengan kategori
- Budget cap per kategori
- PDF export
- Photo proof support

## 📄 License

MIT License - Bebas digunakan untuk personal maupun commercial

## 👨‍💼 Created By

Built with ❤️ for business travel expense tracking

---

**Dokumentasi**: [guide.html](guide.html) | **Issues**: [GitHub Issues](https://github.com/kyretium/Pub/issues)
