<!DOCTYPE html>
<html lang="id" class="h-full bg-slate-50">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Apotek SehatCare — Sistem Informasi & Layanan Apotek Terpadu</title>

  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>

  <!-- Google Fonts: Plus Jakarta Sans -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">

  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['"Plus Jakarta Sans"', 'sans-serif'],
          },
          colors: {
            crimson: {
              50: '#fff1f3',
              100: '#ffe4e8',
              500: '#f43f5e',
              600: '#DC143C', // Primary Crimson Red
              700: '#BE123C', // Hover Crimson Red
              800: '#9f1239',
            },
            skyblue: {
              50: '#f0f9ff',
              100: '#e0f2fe',
              200: '#bae6fd',
              500: '#0EA5E9', // Primary Sky Blue
              600: '#0284c7',
              700: '#0369a1',
            }
          },
          boxShadow: {
            'xs': '0 1px 2px 0 rgba(0, 0, 0, 0.03)',
            'card': '0 4px 20px -4px rgba(15, 23, 42, 0.06), 0 2px 6px -1px rgba(15, 23, 42, 0.03)',
            'hover': '0 12px 28px -6px rgba(14, 165, 233, 0.15), 0 4px 12px -2px rgba(15, 23, 42, 0.04)',
          }
        }
      }
    }
  </script>

  <style>
    /* Custom Scrollbar */
    ::-webkit-scrollbar { width: 6px; height: 6px; }
    ::-webkit-scrollbar-track { background: #F8FAFC; }
    ::-webkit-scrollbar-thumb { background: #CBD5E1; border-radius: 9999px; }
    ::-webkit-scrollbar-thumb:hover { background: #94A3B8; }

    .badge-keras { background-color: #FFF1F3; color: #DC143C; border: 1px solid #FECDD3; }
    .badge-bebas { background-color: #F0FDF4; color: #16A34A; border: 1px solid #BBF7D0; }
    .badge-terbatas { background-color: #F0F9FF; color: #0EA5E9; border: 1px solid #BAE6FD; }

    @media print {
      body * { visibility: hidden; }
      #invoicePrintCanvas, #invoicePrintCanvas * { visibility: visible; }
      #invoicePrintCanvas { position: absolute; left: 0; top: 0; width: 100%; padding: 0; margin: 0; }
      .no-print { display: none !important; }
    }
  </style>
</head>
<body class="h-full flex flex-col font-sans text-slate-700 antialiased bg-slate-50">

  <!-- ================= TOP SWITCHER & SYNC BAR ================= -->
  <header class="bg-white border-b border-slate-200 sticky top-0 z-40 shadow-xs">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex items-center justify-between h-16 gap-4">
        
        <!-- Logo -->
        <div class="flex items-center gap-3">
          <div class="w-10 h-10 rounded-xl bg-gradient-to-br from-crimson-600 to-crimson-700 flex items-center justify-center text-white shadow-sm shrink-0">
            <!-- Medical Cross SVG -->
            <svg class="w-5 h-5 stroke-[2.5]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15"/>
            </svg>
          </div>
          <div>
            <span class="font-extrabold text-base sm:text-lg text-slate-900 tracking-tight flex items-center gap-1.5">
              Apotek <span class="text-crimson-600">SehatCare</span>
            </span>
            <div class="flex items-center gap-2">
              <p class="text-[11px] text-slate-500 font-medium leading-none">Sistem Informasi &amp; Layanan Terpadu</p>
              <span class="w-1.5 h-1.5 rounded-full bg-emerald-500 animate-pulse"></span>
              <span class="text-[10px] text-emerald-600 font-semibold hidden sm:inline">Terhubung Real-time</span>
            </div>
          </div>
        </div>

        <!-- Switcher Pill -->
        <div class="flex items-center bg-slate-100 p-1 rounded-xl border border-slate-200 text-xs font-semibold">
          <button id="btnRolePasien" onclick="switchRole('pasien')" class="flex items-center gap-2 px-3.5 py-1.5 rounded-lg transition-all duration-200 bg-white text-slate-900 shadow-xs font-bold">
            <svg class="w-3.5 h-3.5 text-skyblue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"/>
            </svg>
            <span>Tampilan Masyarakat (Pasien)</span>
          </button>
          <button id="btnRoleAdmin" onclick="switchRole('admin')" class="flex items-center gap-2 px-3.5 py-1.5 rounded-lg transition-all duration-200 text-slate-500 hover:text-slate-900">
            <svg class="w-3.5 h-3.5 text-crimson-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2V6zM14 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2V6zM4 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2v-2zM14 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2v-2z"/>
            </svg>
            <span>Web Dashboard Admin &amp; Kasir</span>
          </button>
        </div>

      </div>
    </div>
  </header>

  <!-- TOAST NOTIFICATION CONTAINER -->
  <div id="toastContainer" class="fixed bottom-5 right-5 z-50 flex flex-col gap-2 max-w-sm pointer-events-none"></div>

  <!-- ========================================================================= -->
  <!-- BAGIAN 1: TAMPILAN MASYARAKAT / PASIEN                                    -->
  <!-- ========================================================================= -->
  <div id="viewPasien" class="flex-1 flex flex-col">

    <!-- Sub Header Navigasi 3 Tab Pasien -->
    <div class="bg-white border-b border-slate-200">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex items-center justify-between py-2 overflow-x-auto gap-4">
          <!-- 3 Tab Buttons -->
          <nav class="flex items-center space-x-1 sm:space-x-2">
            <button onclick="switchPatientTab('katalog')" id="tabNavKatalog" class="flex items-center gap-2 px-4 py-2.5 rounded-xl text-xs font-bold transition-all bg-skyblue-50 text-skyblue-600 border border-skyblue-200">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"/>
              </svg>
              <span>Cari &amp; Pesan Obat</span>
            </button>

            <button onclick="switchPatientTab('pesanan')" id="tabNavPesanan" class="flex items-center gap-2 px-4 py-2.5 rounded-xl text-xs font-semibold text-slate-600 hover:bg-slate-100 transition-all relative">
              <svg class="w-4 h-4 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"/>
              </svg>
              <span>Pesanan &amp; E-Faktur</span>
              <span id="patientOrderBadge" class="bg-crimson-600 text-white text-[10px] font-extrabold px-1.5 py-0.2 rounded-full min-w-[18px] text-center hidden">0</span>
            </button>

            <button onclick="switchPatientTab('konsultasi')" id="tabNavKonsultasi" class="flex items-center gap-2 px-4 py-2.5 rounded-xl text-xs font-semibold text-slate-600 hover:bg-slate-100 transition-all">
              <svg class="w-4 h-4 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z"/>
              </svg>
              <span>Konsultasi Cara Minum</span>
            </button>
          </nav>

          <!-- Keranjang Button -->
          <button onclick="toggleCartDrawer(true)" class="bg-crimson-600 hover:bg-crimson-700 text-white font-bold text-xs px-4 py-2.5 rounded-xl shadow-xs transition transform active:scale-95 flex items-center gap-2 shrink-0">
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 11V7a4 4 0 00-8 0v4M5 9h14l1 12H4L5 9z"/>
            </svg>
            <span class="hidden sm:inline">Keranjang</span>
            <span id="cartBadgeCount" class="bg-white text-crimson-600 text-[11px] font-extrabold px-1.5 py-0.5 rounded-full min-w-[20px] text-center">0</span>
          </button>
        </div>
      </div>
    </div>

    <!-- Main Content Area Pasien -->
    <main class="flex-1 max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6 space-y-6">

      <!-- TAB 1: CARI & PESAN OBAT -->
      <div id="patientTabKatalog" class="space-y-6">
        
        <!-- Modern Split Hero Banner with Clean Pharmacy Vector Illustration -->
        <div class="bg-gradient-to-br from-slate-900 via-slate-800 to-sky-950 text-white rounded-3xl overflow-hidden shadow-card border border-slate-700/50">
          <div class="grid grid-cols-1 lg:grid-cols-12 items-center">
            
            <div class="lg:col-span-7 p-6 sm:p-8 lg:p-10 space-y-5">
              <div class="inline-flex items-center gap-2 bg-white/10 backdrop-blur-md text-skyblue-200 border border-white/15 text-xs font-semibold px-3 py-1 rounded-full">
                <svg class="w-4 h-4 text-emerald-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z"/>
                </svg>
                <span>Apotek Terdaftar Resmi BPOM & Kemenkes</span>
              </div>

              <h1 class="text-2xl sm:text-3xl lg:text-4xl font-extrabold tracking-tight leading-tight text-white">
                Beli Obat Asli Daring, <br class="hidden sm:inline"/>
                <span class="text-transparent bg-clip-text bg-gradient-to-r from-skyblue-400 via-sky-200 to-crimson-400">Jelas Cara Minumnya.</span>
              </h1>

              <p class="text-xs sm:text-sm text-slate-300 max-w-xl leading-relaxed">
                Temukan merek obat tepercaya berdasarkan keluhan Anda. Dilengkapi faktur digital resmi, kartu dosis aturan minum obat interaktif, serta konsultasi apoteker gratis.
              </p>

              <!-- 3 Trust Badges -->
              <div class="pt-2 grid grid-cols-3 gap-3 border-t border-white/10 text-xs">
                <div class="flex items-center gap-2">
                  <div class="w-7 h-7 rounded-lg bg-emerald-500/20 text-emerald-400 flex items-center justify-center shrink-0">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg>
                  </div>
                  <span class="font-bold text-slate-200 text-[11px] leading-tight">100% Obat Asli BPOM</span>
                </div>
                <div class="flex items-center gap-2">
                  <div class="w-7 h-7 rounded-lg bg-skyblue-500/20 text-skyblue-300 flex items-center justify-center shrink-0">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"/></svg>
                  </div>
                  <span class="font-bold text-slate-200 text-[11px] leading-tight">Konsultasi Apoteker Gratis</span>
                </div>
                <div class="flex items-center gap-2">
                  <div class="w-7 h-7 rounded-lg bg-crimson-500/20 text-crimson-300 flex items-center justify-center shrink-0">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/></svg>
                  </div>
                  <span class="font-bold text-slate-200 text-[11px] leading-tight">Siap Antar &amp; Ambil Cepat</span>
                </div>
              </div>
            </div>

            <!-- Hero Vector Illustration Banner -->
            <div class="lg:col-span-5 h-56 lg:h-full min-h-[240px] relative overflow-hidden bg-slate-800 flex items-center justify-center p-4">
              <!-- Custom SVG Pharmacy Counter Graphic -->
              <svg class="w-full h-full max-h-56" viewBox="0 0 400 240" fill="none" xmlns="http://www.w3.org/2000/svg">
                <rect width="400" height="240" rx="16" fill="#0F172A"/>
                <!-- Shelves background -->
                <rect x="30" y="20" width="340" height="130" rx="12" fill="#1E293B" stroke="#334155" stroke-width="2"/>
                <line x1="30" y1="65" x2="370" y2="65" stroke="#334155" stroke-width="2"/>
                <line x1="30" y1="110" x2="370" y2="110" stroke="#334155" stroke-width="2"/>
                <!-- Bottles on shelves -->
                <rect x="50" y="32" width="16" height="26" rx="3" fill="#0EA5E9"/>
                <rect x="72" y="35" width="14" height="23" rx="3" fill="#DC143C"/>
                <rect x="92" y="30" width="18" height="28" rx="3" fill="#10B981"/>
                <rect x="120" y="33" width="15" height="25" rx="3" fill="#F59E0B"/>
                
                <rect x="50" y="75" width="20" height="28" rx="4" fill="#DC143C"/>
                <rect x="76" y="78" width="16" height="25" rx="3" fill="#0EA5E9"/>
                <rect x="100" y="72" width="22" height="31" rx="4" fill="#38BDF8"/>
                <rect x="130" y="76" width="18" height="27" rx="3" fill="#10B981"/>

                <rect x="250" y="30" width="22" height="28" rx="4" fill="#F43F5E"/>
                <rect x="278" y="34" width="16" height="24" rx="3" fill="#0EA5E9"/>
                <rect x="300" y="31" width="18" height="27" rx="3" fill="#38BDF8"/>
                <rect x="324" y="35" width="15" height="23" rx="3" fill="#10B981"/>

                <rect x="250" y="75" width="18" height="28" rx="3" fill="#0EA5E9"/>
                <rect x="274" y="72" width="22" height="31" rx="4" fill="#DC143C"/>
                <rect x="302" y="76" width="16" height="27" rx="3" fill="#F59E0B"/>
                
                <!-- Counter Desk -->
                <path d="M20 160 H380 V220 H20 Z" fill="#0284C7"/>
                <path d="M20 150 H380 V165 H20 Z" fill="#38BDF8"/>
                <!-- Cross Logo on Desk -->
                <circle cx="200" cy="188" r="18" fill="#FFFFFF"/>
                <path d="M200 176 V200 M188 188 H212" stroke="#DC143C" stroke-width="5" stroke-linecap="round"/>
                <!-- Glowing Accent -->
                <circle cx="340" cy="188" r="6" fill="#10B981"/>
                <text x="315" y="192" fill="#FFFFFF" font-size="10" font-weight="bold">BUKA</text>
              </svg>
              
              <!-- Floating Overlay Badge -->
              <div class="absolute bottom-4 right-4 bg-white/95 backdrop-blur-md px-3.5 py-2 rounded-xl shadow-lg border border-slate-200 text-slate-900 text-xs font-bold flex items-center gap-2">
                <span class="w-2.5 h-2.5 rounded-full bg-emerald-500 animate-pulse"></span>
                <span>Apotek Buka: 07.00 - 22.00 WIB</span>
              </div>
            </div>

          </div>
        </div>
        
        <!-- Search & Category Filters -->
        <div class="bg-white border border-slate-200 rounded-2xl p-4 sm:p-5 shadow-card space-y-4">
          <div class="relative">
            <input type="text" id="patientSearchInput" onkeyup="renderPatientCatalog()" placeholder="Cari nama merek obat (misal: OBH Combi, Sanmol), kandungan, atau keluhan..." class="w-full text-xs sm:text-sm pl-10 pr-4 py-3 rounded-xl border border-slate-200 focus:outline-none focus:border-skyblue-500 focus:ring-1 focus:ring-skyblue-500 transition">
            <svg class="w-4 h-4 text-slate-400 absolute left-3.5 top-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"/>
            </svg>
          </div>

          <!-- Category Pills -->
          <div class="flex items-center gap-2 overflow-x-auto pb-1 scrollbar-none" id="patientCategoryPills">
            <!-- Rendered dynamically -->
          </div>
        </div>

        <!-- Section Title & Grid -->
        <div class="flex items-center justify-between">
          <h2 class="text-sm font-bold text-slate-800 uppercase tracking-wider flex items-center gap-2">
            <svg class="w-4 h-4 text-skyblue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19.428 15.428a2 2 0 00-1.022-.547l-2.387-.477a6 6 0 00-3.86.517l-.318.158a6 6 0 01-3.86.517L5.6 15.12a2 2 0 00-1.023.547l-1.2 1.2a2 2 0 000 2.828l1.2 1.2a2 2 0 002.828 0l1.2-1.2a2 2 0 00.547-1.022l.477-2.387a6 6 0 01.517-3.86l.158-.318a6 6 0 00.517-3.86L9.88 5.6a2 2 0 01.547-1.023l1.2-1.2a2 2 0 012.828 0l1.2 1.2a2 2 0 010 2.828l-1.2 1.2a2 2 0 01-1.022.547l-2.387.477a6 6 0 00-3.86-.517l-.318-.158a6 6 0 01-3.86-.517L5.6 8.88"/>
            </svg>
            <span>Daftar Merek Obat Tersedia</span>
          </h2>
          <span id="patientCatalogCount" class="text-xs text-slate-400 font-medium">Menampilkan 0 produk</span>
        </div>

        <div id="patientMedicineGrid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          <!-- Cards rendered dynamically -->
        </div>
      </div>

      <!-- TAB 2: PESANAN & E-FAKTUR -->
      <div id="patientTabPesanan" class="space-y-6 hidden">
        <div class="bg-white border border-slate-200 rounded-2xl p-6 shadow-card space-y-4">
          <div class="border-b border-slate-100 pb-3 flex items-center justify-between">
            <div>
              <h2 class="text-base font-bold text-slate-900">Riwayat Pesanan &amp; E-Faktur Saya</h2>
              <p class="text-xs text-slate-500 mt-0.5">Pantau status pesanan dan unduh e-faktur lengkap dengan jadwal minum obat.</p>
            </div>
            <span class="text-xs bg-skyblue-50 text-skyblue-700 border border-skyblue-200 px-3 py-1 rounded-full font-bold">Faktur Digital Resmi</span>
          </div>

          <div id="patientOrdersContainer" class="space-y-4">
            <!-- Rendered dynamically -->
          </div>
        </div>
      </div>

      <!-- TAB 3: KONSULTASI CARA MINUM -->
      <div id="patientTabKonsultasi" class="space-y-6 hidden">
        <div class="bg-white border border-slate-200 rounded-2xl shadow-card overflow-hidden grid grid-cols-1 lg:grid-cols-12 min-h-[520px]">
          
          <!-- Information Sidebar with Pharmacist Vector Portrait -->
          <div class="lg:col-span-4 bg-slate-50 p-6 border-b lg:border-b-0 lg:border-r border-slate-200 space-y-5">
            
            <!-- Pharmacist Profile Visual Card -->
            <div class="bg-white border border-slate-200 rounded-2xl p-4 shadow-xs text-center space-y-3">
              <div class="relative w-20 h-20 mx-auto rounded-full overflow-hidden border-2 border-skyblue-500 shadow-md flex items-center justify-center bg-slate-100">
                <!-- SVG Pharmacist Avatar -->
                <svg class="w-full h-full" viewBox="0 0 100 100" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <circle cx="50" cy="50" r="50" fill="#E0F2FE"/>
                  <!-- Hair -->
                  <path d="M25 45 C25 20, 75 20, 75 45 C75 50, 70 65, 70 70 L30 70 C30 65, 25 50, 25 45 Z" fill="#1E293B"/>
                  <!-- Face -->
                  <circle cx="50" cy="42" r="18" fill="#FCD34D"/>
                  <!-- Eyes -->
                  <circle cx="43" cy="40" r="2" fill="#0F172A"/>
                  <circle cx="57" cy="40" r="2" fill="#0F172A"/>
                  <!-- Smile -->
                  <path d="M44 48 Q50 53 56 48" stroke="#0F172A" stroke-width="2" stroke-linecap="round"/>
                  <!-- Glasses -->
                  <circle cx="43" cy="40" r="6" stroke="#0284C7" stroke-width="1.5" fill="none"/>
                  <circle cx="57" cy="40" r="6" stroke="#0284C7" stroke-width="1.5" fill="none"/>
                  <line x1="49" y1="40" x2="51" y2="40" stroke="#0284C7" stroke-width="1.5"/>
                  <!-- Lab Coat -->
                  <path d="M20 90 L32 60 L50 68 L68 60 L80 90 Z" fill="#FFFFFF"/>
                  <!-- Stethoscope / Badge -->
                  <path d="M42 62 V78 M58 62 V78 M42 78 Q50 85 58 78" stroke="#DC143C" stroke-width="2" fill="none"/>
                  <rect x="46" y="80" width="8" height="12" rx="2" fill="#0EA5E9"/>
                </svg>
                <span class="absolute bottom-0 right-0 w-4 h-4 bg-emerald-500 border-2 border-white rounded-full"></span>
              </div>

              <div>
                <h3 class="font-bold text-slate-900 text-sm">apt. Rina Wulandari, S.Farm.</h3>
                <p class="text-[11px] text-skyblue-600 font-semibold">Apoteker Penanggung Jawab SIPA</p>
                <span class="inline-block bg-slate-100 text-slate-500 text-[10px] font-mono px-2 py-0.5 rounded mt-1">SIPA: 446/1029/APT/2026</span>
              </div>

              <p class="text-xs text-slate-500 leading-relaxed border-t border-slate-100 pt-2">
                "Silakan konsultasikan dosis, jeda minum obat, atau efek samping obat yang sedang Anda konsumsi."
              </p>
            </div>

            <!-- Quick Chips -->
            <div class="space-y-2">
              <span class="text-[11px] font-bold text-slate-400 uppercase tracking-wider block">Pertanyaan Cepat (Klik):</span>
              <div class="space-y-1.5">
                <button onclick="sendPatientQuickQuestion('Bagaimana aturan minum sebelum / sesudah makan?')" class="w-full text-left text-xs bg-white hover:bg-skyblue-50 text-slate-700 border border-slate-200 px-3 py-2 rounded-lg transition font-medium flex items-center justify-between">
                  <span>❓ Minum Sebelum/Sesudah Makan?</span>
                  <svg class="w-3.5 h-3.5 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"/></svg>
                </button>
                <button onclick="sendPatientQuickQuestion('Apakah obat ini aman untuk anak-anak?')" class="w-full text-left text-xs bg-white hover:bg-skyblue-50 text-slate-700 border border-slate-200 px-3 py-2 rounded-lg transition font-medium flex items-center justify-between">
                  <span>❓ Dosis Untuk Anak-anak?</span>
                  <svg class="w-3.5 h-3.5 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"/></svg>
                </button>
                <button onclick="sendPatientQuickQuestion('Bolehkah obat ini diminum bersamaan obat lain?')" class="w-full text-left text-xs bg-white hover:bg-skyblue-50 text-slate-700 border border-slate-200 px-3 py-2 rounded-lg transition font-medium flex items-center justify-between">
                  <span>❓ Diminum Bersamaan Obat Lain?</span>
                  <svg class="w-3.5 h-3.5 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"/></svg>
                </button>
              </div>
            </div>
          </div>

          <!-- Chat Window -->
          <div class="lg:col-span-8 flex flex-col h-full bg-white">
            <div class="p-4 border-b border-slate-100 flex items-center justify-between bg-slate-50/50">
              <div class="flex items-center gap-2.5">
                <div class="w-8 h-8 rounded-full bg-skyblue-100 text-skyblue-600 flex items-center justify-center font-bold text-xs">
                  <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z"/></svg>
                </div>
                <div>
                  <span class="font-bold text-xs text-slate-900 block leading-none">Konsultasi Dosis &amp; Cara Minum</span>
                  <span class="text-[10px] text-emerald-600 font-semibold">Status: Apoteker Siaga Online</span>
                </div>
              </div>
              <span class="text-[11px] text-slate-400 font-medium">Bebas Biaya</span>
            </div>

            <div id="patientChatBox" class="flex-1 p-6 overflow-y-auto space-y-3 max-h-[400px] bg-slate-50/30">
              <!-- Rendered dynamically -->
            </div>

            <form onsubmit="handlePatientSendMessage(event)" class="p-4 border-t border-slate-200 flex gap-2 bg-white">
              <input type="text" id="patientChatInput" placeholder="Ketik pertanyaan aturan pakai atau dosis obat..." class="flex-1 text-xs border border-slate-200 rounded-xl px-4 py-2.5 focus:outline-none focus:border-skyblue-500 focus:ring-1 focus:ring-skyblue-500">
              <button type="submit" class="bg-crimson-600 hover:bg-crimson-700 text-white font-bold px-5 py-2.5 rounded-xl text-xs shadow-xs transition flex items-center gap-1.5">
                <span>Kirim</span>
                <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"/></svg>
              </button>
            </form>
          </div>

        </div>
      </div>

    </main>
  </div>

  <!-- ========================================================================= -->
  <!-- BAGIAN 2: WEB DASHBOARD ADMIN & KASIR APOTEK                              -->
  <!-- ========================================================================= -->
  <div id="viewAdmin" class="flex-1 flex hidden bg-slate-100">
    <div class="flex-1 flex max-w-7xl w-full mx-auto">
      
      <!-- Left Sidebar -->
      <aside class="w-64 bg-white border-r border-slate-200 flex flex-col shrink-0 p-4 space-y-6">
        <!-- Admin Profile -->
        <div class="bg-slate-50 border border-slate-200 rounded-xl p-3 flex items-center gap-3">
          <div class="w-9 h-9 rounded-xl bg-crimson-600 text-white flex items-center justify-center font-bold text-xs shrink-0 shadow-xs">
            RM
          </div>
          <div class="min-w-0 flex-1">
            <h4 class="font-bold text-xs text-slate-900 truncate">Rina Melati</h4>
            <p class="text-[10px] text-slate-500">Kasir Apotek On-Duty</p>
          </div>
        </div>

        <div>
          <span class="text-[10px] font-bold text-slate-400 uppercase tracking-wider block mb-2">Menu Utama Web Admin</span>
          <nav class="space-y-1">
            <button onclick="switchAdminTab('ringkasan')" id="adminNavRingkasan" class="w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-xs font-bold bg-skyblue-50 text-skyblue-600 border border-skyblue-100 transition">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2V6zM14 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2V6zM4 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2v-2zM14 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2v-2z"/></svg>
              <span>Pelayanan &amp; Ringkasan</span>
            </button>

            <button onclick="switchAdminTab('stok')" id="adminNavStok" class="w-full flex items-center justify-between px-3.5 py-2.5 rounded-xl text-xs font-medium text-slate-600 hover:bg-slate-50 transition">
              <div class="flex items-center gap-3">
                <svg class="w-4 h-4 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4"/></svg>
                <span>Master &amp; Stok Obat</span>
              </div>
              <span id="adminNavStokBadge" class="bg-crimson-600 text-white text-[10px] font-bold px-1.5 py-0.2 rounded-full hidden">0</span>
            </button>

            <button onclick="switchAdminTab('faktur')" id="adminNavFaktur" class="w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-xs font-medium text-slate-600 hover:bg-slate-50 transition">
              <svg class="w-4 h-4 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 17h2a2 2 0 002-2v-4a2 2 0 00-2-2H5a2 2 0 00-2 2v4a2 2 0 002 2h2m2 4h6a2 2 0 002-2v-4a2 2 0 00-2-2H9a2 2 0 00-2 2v4a2 2 0 002 2zm8-12V5a2 2 0 00-2-2H9a2 2 0 00-2 2v4h10z"/></svg>
              <span>Faktur &amp; Kasir (POS)</span>
            </button>

            <button onclick="switchAdminTab('konsultasi')" id="adminNavKonsultasi" class="w-full flex items-center justify-between px-3.5 py-2.5 rounded-xl text-xs font-medium text-slate-600 hover:bg-slate-50 transition">
              <div class="flex items-center gap-3">
                <svg class="w-4 h-4 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z"/></svg>
                <span>Konsultasi Pasien</span>
              </div>
              <span id="adminNavChatBadge" class="bg-skyblue-500 text-white text-[10px] font-bold px-1.5 py-0.2 rounded-full hidden">0</span>
            </button>
          </nav>
        </div>

        <div class="mt-auto pt-4 border-t border-slate-100 text-[11px] text-slate-400 space-y-1">
          <p class="font-bold text-slate-700">Apotek SehatCare System</p>
          <p>Status DB: Synchronized</p>
        </div>
      </aside>

      <!-- Main Admin Content -->
      <main class="flex-1 p-6 overflow-y-auto space-y-6">

        <!-- TAB ADMIN 1: PELAYANAN ONLINE & RINGKASAN -->
        <div id="adminTabRingkasan" class="space-y-6">
          <div class="flex items-center justify-between border-b border-slate-200 pb-4">
            <div>
              <h1 class="text-lg font-bold text-slate-900">Pelayanan Online &amp; Ringkasan Apotek</h1>
              <p class="text-xs text-slate-500">Kelola pesanan masuk dari masyarakat dan pantau indikator stok.</p>
            </div>
            <button onclick="openAddMedicineModal()" class="bg-crimson-600 hover:bg-crimson-700 text-white font-bold text-xs px-4 py-2.5 rounded-xl shadow-xs transition flex items-center gap-2">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"/></svg>
              <span>Tambah Merek Obat Baru</span>
            </button>
          </div>

          <!-- 4 Stat Cards -->
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
            <div class="bg-white border border-slate-200 rounded-xl p-4 shadow-card">
              <span class="text-[11px] font-bold text-slate-400 uppercase tracking-wider block">Pesanan Baru</span>
              <div class="flex items-baseline justify-between mt-2">
                <span id="statNewOrders" class="text-2xl font-extrabold text-slate-900">0</span>
                <span class="text-[10px] font-semibold text-amber-600 bg-amber-50 px-2 py-0.5 rounded">Perlu Aksi</span>
              </div>
            </div>

            <div class="bg-white border border-slate-200 rounded-xl p-4 shadow-card">
              <span class="text-[11px] font-bold text-slate-400 uppercase tracking-wider block">Total Merek Obat</span>
              <div class="flex items-baseline justify-between mt-2">
                <span id="statTotalMedicines" class="text-2xl font-extrabold text-slate-900">0</span>
                <span class="text-[10px] font-semibold text-skyblue-600 bg-skyblue-50 px-2 py-0.5 rounded">Terdaftar</span>
              </div>
            </div>

            <div class="bg-white border border-slate-200 rounded-xl p-4 shadow-card">
              <span class="text-[11px] font-bold text-slate-400 uppercase tracking-wider block">Stok Menipis (&le; 5)</span>
              <div class="flex items-baseline justify-between mt-2">
                <span id="statLowStock" class="text-2xl font-extrabold text-crimson-600">0</span>
                <span class="text-[10px] font-semibold text-crimson-600 bg-crimson-50 px-2 py-0.5 rounded">Peringatan</span>
              </div>
            </div>

            <div class="bg-white border border-slate-200 rounded-xl p-4 shadow-card">
              <span class="text-[11px] font-bold text-slate-400 uppercase tracking-wider block">Total Faktur Terbit</span>
              <div class="flex items-baseline justify-between mt-2">
                <span id="statIssuedInvoices" class="text-2xl font-extrabold text-slate-900">0</span>
                <span class="text-[10px] font-semibold text-emerald-600 bg-emerald-50 px-2 py-0.5 rounded">Resmi</span>
              </div>
            </div>
          </div>

          <!-- Tabel Pesanan Masuk -->
          <div class="bg-white border border-slate-200 rounded-2xl p-5 shadow-card space-y-4">
            <h2 class="text-sm font-bold text-slate-900">Pesanan Masuk dari Masyarakat</h2>
            
            <div class="overflow-x-auto">
              <table class="w-full text-xs text-left border-collapse">
                <thead class="bg-slate-50 text-slate-600 font-bold border-b border-slate-200">
                  <tr>
                    <th class="py-3 px-3">No. Order</th>
                    <th class="py-3 px-3">Nama Pasien</th>
                    <th class="py-3 px-3">Daftar Obat &amp; Resep</th>
                    <th class="py-3 px-3">Metode</th>
                    <th class="py-3 px-3">Status</th>
                    <th class="py-3 px-3 text-right">Aksi Kasir</th>
                  </tr>
                </thead>
                <tbody id="adminOrdersTableBody" class="divide-y divide-slate-100 text-slate-700">
                  <!-- Rendered dynamically -->
                </tbody>
              </table>
            </div>
          </div>
        </div>

        <!-- TAB ADMIN 2: MASTER & STOK OBAT -->
        <div id="adminTabStok" class="space-y-6 hidden">
          <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 border-b border-slate-200 pb-4">
            <div>
              <h1 class="text-lg font-bold text-slate-900">Master &amp; Stok Obat Apotek</h1>
              <p class="text-xs text-slate-500">Kelola katalog merek obat, penambahan merek baru, dan visual gambar obat.</p>
            </div>
            <button onclick="openAddMedicineModal()" class="bg-crimson-600 hover:bg-crimson-700 text-white font-bold text-xs px-4 py-2.5 rounded-xl shadow-xs transition flex items-center gap-2">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"/></svg>
              <span>Tambah Merek Obat Baru</span>
            </button>
          </div>

          <!-- Search & Filter Bar -->
          <div class="bg-white border border-slate-200 rounded-xl p-3 flex flex-col sm:flex-row items-center justify-between gap-3 shadow-xs">
            <input type="text" id="adminStokSearch" onkeyup="renderAdminStokTable()" placeholder="Cari nama merek obat..." class="w-full sm:w-72 text-xs border border-slate-200 rounded-lg px-3 py-2 focus:outline-none focus:border-skyblue-500">
            <select id="adminStokCategory" onchange="renderAdminStokTable()" class="w-full sm:w-48 text-xs border border-slate-200 rounded-lg px-3 py-2 focus:outline-none focus:border-skyblue-500">
              <option value="Semua">Semua Kategori</option>
              <option value="Batuk">Batuk</option>
              <option value="Pilek & Flu">Pilek &amp; Flu</option>
              <option value="Meriang & Demam">Meriang &amp; Demam</option>
              <option value="Diare">Diare</option>
              <option value="Vitamin & Suplemen">Vitamin &amp; Suplemen</option>
              <option value="Obat Keras">Obat Keras</option>
            </select>
          </div>

          <!-- Tabel Master Obat -->
          <div class="bg-white border border-slate-200 rounded-2xl overflow-hidden shadow-card">
            <div class="overflow-x-auto">
              <table class="w-full text-xs text-left border-collapse">
                <thead class="bg-slate-800 text-slate-200 font-bold">
                  <tr>
                    <th class="py-3 px-3">Visual Kemasan</th>
                    <th class="py-3 px-3">Merek Dagang</th>
                    <th class="py-3 px-3">Kategori</th>
                    <th class="py-3 px-3">Golongan</th>
                    <th class="py-3 px-3">Satuan</th>
                    <th class="py-3 px-3">Harga (Rp)</th>
                    <th class="py-3 px-3">Stok</th>
                    <th class="py-3 px-3">Aturan Pakai Default</th>
                    <th class="py-3 px-3 text-center">Restok</th>
                  </tr>
                </thead>
                <tbody id="adminStokTableBody" class="divide-y divide-slate-200 text-slate-700">
                  <!-- Rendered dynamically -->
                </tbody>
              </table>
            </div>
          </div>
        </div>

        <!-- TAB ADMIN 3: FAKTUR & KASIR (POS) -->
        <div id="adminTabFaktur" class="space-y-6 hidden">
          <div class="flex items-center justify-between border-b border-slate-200 pb-4">
            <div>
              <h1 class="text-lg font-bold text-slate-900">Faktur &amp; Kasir POS Walk-in</h1>
              <p class="text-xs text-slate-500">Transaksi langsung di meja apotek dan riwayat faktur diterbitkan.</p>
            </div>
            <button onclick="openWalkinPosModal()" class="bg-skyblue-600 hover:bg-skyblue-700 text-white font-bold text-xs px-4 py-2.5 rounded-xl shadow-xs transition flex items-center gap-2">
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"/></svg>
              <span>Buat Faktur Kasir (Walk-in)</span>
            </button>
          </div>

          <div class="bg-white border border-slate-200 rounded-2xl overflow-hidden shadow-card">
            <div class="overflow-x-auto">
              <table class="w-full text-xs text-left border-collapse">
                <thead class="bg-slate-800 text-slate-200 font-bold">
                  <tr>
                    <th class="py-3 px-3">No. Faktur</th>
                    <th class="py-3 px-3">Tanggal &amp; Waktu</th>
                    <th class="py-3 px-3">Nama Pasien</th>
                    <th class="py-3 px-3">Tipe</th>
                    <th class="py-3 px-3">Kasir</th>
                    <th class="py-3 px-3">Total (Rp)</th>
                    <th class="py-3 px-3 text-center">Aksi</th>
                  </tr>
                </thead>
                <tbody id="adminInvoicesTableBody" class="divide-y divide-slate-200 text-slate-700">
                  <!-- Rendered dynamically -->
                </tbody>
              </table>
            </div>
          </div>
        </div>

        <!-- TAB ADMIN 4: KONSULTASI PASIEN -->
        <div id="adminTabKonsultasi" class="space-y-6 hidden">
          <div class="border-b border-slate-200 pb-4">
            <h1 class="text-lg font-bold text-slate-900">Konsol Konsultasi Pasien</h1>
            <p class="text-xs text-slate-500">Balas pertanyaan masyarakat seputar cara penggunaan obat.</p>
          </div>

          <div class="bg-white border border-slate-200 rounded-2xl shadow-card overflow-hidden grid grid-cols-1 lg:grid-cols-12 min-h-[480px]">
            <div class="lg:col-span-4 bg-slate-50 p-4 border-r border-slate-200 space-y-3">
              <span class="text-[11px] font-bold text-slate-400 uppercase tracking-wider block">Percakapan Aktif</span>
              <div class="bg-white border border-skyblue-300 rounded-xl p-3 shadow-xs flex items-center gap-3 cursor-pointer">
                <div class="w-8 h-8 rounded-full bg-skyblue-600 text-white font-bold flex items-center justify-center text-xs shrink-0">
                  PS
                </div>
                <div class="flex-1 min-w-0">
                  <h4 class="font-bold text-slate-900 text-xs truncate">Pasien Online</h4>
                  <p class="text-[11px] text-slate-500 truncate" id="adminLastChatText">Pertanyaan masuk...</p>
                </div>
              </div>
            </div>

            <div class="lg:col-span-8 flex flex-col h-full bg-white">
              <div id="adminChatBox" class="flex-1 p-6 overflow-y-auto space-y-3 bg-slate-50/40 max-h-[360px]">
                <!-- Messages rendered dynamically -->
              </div>

              <!-- Quick Template Reply Chips -->
              <div class="p-3 bg-skyblue-50 border-t border-skyblue-100 space-y-1.5">
                <span class="text-[10px] font-bold text-skyblue-700 uppercase tracking-wider block">Template Jawaban Cepat Kasir:</span>
                <div class="flex flex-wrap gap-1.5">
                  <button onclick="sendAdminQuickReply('Diminum 3 x sehari 1 tablet sesudah makan. Kocok botol terlebih dahulu jika sirup.')" class="bg-white hover:bg-skyblue-100 text-skyblue-800 border border-skyblue-200 px-2.5 py-1 rounded-lg text-xs font-semibold transition">
                    3x1 Sesudah Makan
                  </button>
                  <button onclick="sendAdminQuickReply('Aman untuk dewasa. Untuk anak-anak gunakan setengah dosis dewasa.')" class="bg-white hover:bg-skyblue-100 text-skyblue-800 border border-skyblue-200 px-2.5 py-1 rounded-lg text-xs font-semibold transition">
                    Dosis Anak
                  </button>
                  <button onclick="sendAdminQuickReply('Harap beri jeda 2 jam jika diminum bersamaan dengan obat lain.')" class="bg-white hover:bg-skyblue-100 text-skyblue-800 border border-skyblue-200 px-2.5 py-1 rounded-lg text-xs font-semibold transition">
                    Jeda 2 Jam
                  </button>
                </div>
              </div>

              <form onsubmit="handleAdminSendMessage(event)" class="p-3 border-t border-slate-200 flex gap-2">
                <input type="text" id="adminChatInput" placeholder="Ketik balasan instruksi obat..." class="flex-1 text-xs border border-slate-200 rounded-xl px-4 py-2.5 focus:outline-none focus:border-skyblue-500">
                <button type="submit" class="bg-crimson-600 hover:bg-crimson-700 text-white font-bold px-4 py-2.5 rounded-xl text-xs shadow-xs transition">Kirim Balasan</button>
              </form>
            </div>
          </div>
        </div>

      </main>
    </div>
  </div>


  <!-- ========================================================================= -->
  <!-- MODALS & SLIDE-OVER DRAWERS                                              -->
  <!-- ========================================================================= -->

  <!-- 1. SLIDE-OVER DRAWER KERANJANG BELANJA -->
  <div id="cartDrawer" class="fixed inset-0 z-50 overflow-hidden hidden">
    <div class="absolute inset-0 bg-slate-900/40 backdrop-blur-xs transition-opacity" onclick="toggleCartDrawer(false)"></div>

    <div class="pointer-events-none fixed inset-y-0 right-0 flex max-w-full pl-10">
      <div class="pointer-events-auto w-screen max-w-md bg-white shadow-2xl flex flex-col">
        <!-- Header -->
        <div class="p-4 bg-slate-900 text-white flex items-center justify-between">
          <div class="flex items-center gap-2">
            <svg class="w-5 h-5 text-crimson-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 11V7a4 4 0 00-8 0v4M5 9h14l1 12H4L5 9z"/></svg>
            <h2 class="font-bold text-sm">Keranjang Pemesanan Obat</h2>
          </div>
          <button onclick="toggleCartDrawer(false)" class="text-slate-400 hover:text-white">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
          </button>
        </div>

        <!-- Body -->
        <div class="flex-1 overflow-y-auto p-5 space-y-5">
          <div id="cartItemsList" class="space-y-3">
            <!-- Rendered dynamically -->
          </div>

          <!-- Prescription Upload / Sample Box -->
          <div id="cartPrescriptionBox" class="bg-crimson-50 border border-crimson-200 rounded-xl p-4 space-y-3 hidden">
            <div class="flex items-center gap-2 text-crimson-700 font-bold text-xs">
              <span class="w-5 h-5 rounded-full bg-crimson-600 text-white flex items-center justify-center text-[10px] shrink-0 font-extrabold">K</span>
              <span>Diperlukan Foto Resep Dokter (Obat Keras)</span>
            </div>
            <p class="text-[11px] text-slate-600 leading-relaxed">Keranjang Anda mengandung obat bertanda merah (Obat Keras). Lampirkan foto resep dokter Anda atau gunakan contoh foto resep resmi.</p>
            
            <div class="flex flex-col gap-2">
              <input type="file" onchange="handlePrescriptionUploaded(event)" class="text-xs text-slate-500 file:mr-2 file:py-1 file:px-3 file:rounded-lg file:border-0 file:text-xs file:font-bold file:bg-crimson-600 file:text-white hover:file:bg-crimson-700 cursor-pointer">
              
              <button type="button" onclick="useSamplePrescription()" class="text-xs bg-white hover:bg-slate-50 border border-crimson-300 text-crimson-700 font-bold px-3 py-2 rounded-lg flex items-center justify-center gap-1.5 transition">
                <svg class="w-3.5 h-3.5 text-crimson-600" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/></svg>
                <span>Gunakan Contoh Foto Resep Dokter</span>
              </button>
            </div>

            <!-- Visual Preview Card of Prescription -->
            <div id="prescriptionPreviewCard" class="bg-white border border-slate-200 rounded-lg p-3 space-y-1 text-xs hidden shadow-xs">
              <div class="flex items-center justify-between text-[10px] font-bold text-slate-400">
                <span>RESEP DOKTER TERLAMPIR</span>
                <span class="text-emerald-600">✓ Terverifikasi</span>
              </div>
              <p class="font-bold text-slate-900 text-xs">Dr. H. Ahmad Subagyo, Sp.PD</p>
              <p class="text-[10px] text-slate-500">SIP: 503/449/SIP.D/2026 • RS Medika Utama</p>
              <div class="bg-slate-50 p-2 rounded text-[11px] font-mono text-slate-700 mt-1">
                R/ Amoxicillin 500mg tab No. X <br/>
                S 3 dd tab I (Sesudah makan)
              </div>
            </div>
          </div>

          <!-- Patient Info Form -->
          <div class="border-t border-slate-100 pt-4 space-y-3">
            <h3 class="font-bold text-slate-900 text-xs uppercase tracking-wider">Data Pemesan</h3>
            
            <div>
              <label class="block text-xs font-semibold text-slate-700 mb-1">Nama Pasien *</label>
              <input type="text" id="checkoutPatientName" value="Budi Santoso" class="w-full text-xs border border-slate-200 rounded-lg p-2.5 focus:outline-none focus:border-skyblue-500">
            </div>

            <div>
              <label class="block text-xs font-semibold text-slate-700 mb-1">Layanan Penerimaan *</label>
              <select id="checkoutDelivery" onchange="renderCartDrawer()" class="w-full text-xs border border-slate-200 rounded-lg p-2.5 focus:outline-none focus:border-skyblue-500">
                <option value="Ambil di Apotek">Ambil Mandiri di Apotek (Gratis)</option>
                <option value="Antar ke Rumah">Antar ke Rumah (Ongkir Rp 8.000)</option>
              </select>
            </div>

            <div>
              <label class="block text-xs font-semibold text-slate-700 mb-1">Alamat / No. HP *</label>
              <input type="text" id="checkoutAddress" value="Jl. Mawar No. 12, Jakarta (0812-3456-7890)" class="w-full text-xs border border-slate-200 rounded-lg p-2.5 focus:outline-none focus:border-skyblue-500">
            </div>
          </div>
        </div>

        <!-- Footer -->
        <div class="p-5 bg-slate-50 border-t border-slate-200 space-y-3">
          <div class="flex items-center justify-between text-base font-extrabold text-slate-900">
            <span>Total Pembayaran</span>
            <span id="cartTotalText" class="text-crimson-600">Rp 0</span>
          </div>

          <button onclick="submitPatientOrder()" class="w-full bg-crimson-600 hover:bg-crimson-700 text-white font-bold py-3 rounded-xl text-xs shadow-xs transition flex items-center justify-center gap-2">
            <span>Buat Pesanan Sekarang</span>
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3"/></svg>
          </button>
        </div>
      </div>
    </div>
  </div>


  <!-- 2. MODAL TAMBAH MEREK OBAT BARU -->
  <div id="addMedicineModal" class="fixed inset-0 z-50 overflow-y-auto hidden">
    <div class="flex items-center justify-center min-h-screen px-4 pt-4 pb-20 text-center sm:block sm:p-0">
      <div class="fixed inset-0 bg-slate-900/50 backdrop-blur-xs transition-opacity" onclick="closeAddMedicineModal()"></div>

      <div class="inline-block align-bottom bg-white rounded-2xl text-left overflow-hidden shadow-2xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full">
        <div class="bg-slate-900 p-4 text-white flex items-center justify-between">
          <h3 class="font-bold text-sm">Tambah Merek Obat Baru</h3>
          <button onclick="closeAddMedicineModal()" class="text-slate-400 hover:text-white">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
          </button>
        </div>

        <form onsubmit="submitNewMedicine(event)" class="p-6 space-y-4 text-xs">
          <div>
            <label class="block font-semibold text-slate-700 mb-1">Merek Dagang Obat *</label>
            <input type="text" id="newBrand" required placeholder="Contoh: Bodrexin, Komix, Panadol..." class="w-full border border-slate-200 rounded-lg p-2.5 focus:outline-none focus:border-skyblue-500">
          </div>

          <div class="grid grid-cols-2 gap-3">
            <div>
              <label class="block font-semibold text-slate-700 mb-1">Kategori Gejala *</label>
              <select id="newCategory" required class="w-full border border-slate-200 rounded-lg p-2.5 focus:outline-none focus:border-skyblue-500">
                <option value="Batuk">Batuk</option>
                <option value="Pilek & Flu">Pilek &amp; Flu</option>
                <option value="Meriang & Demam">Meriang &amp; Demam</option>
                <option value="Diare">Diare</option>
                <option value="Vitamin & Suplemen">Vitamin &amp; Suplemen</option>
                <option value="Obat Keras">Obat Keras</option>
              </select>
            </div>
            <div>
              <label class="block font-semibold text-slate-700 mb-1">Golongan Obat *</label>
              <select id="newGrade" required class="w-full border border-slate-200 rounded-lg p-2.5 focus:outline-none focus:border-skyblue-500">
                <option value="Bebas">Bebas (Hijau)</option>
                <option value="Bebas Terbatas">Bebas Terbatas (Biru)</option>
                <option value="Keras">Keras (Merah-K)</option>
              </select>
            </div>
          </div>

          <!-- Visual Packaging Preset Selector -->
          <div>
            <label class="block font-semibold text-slate-700 mb-1">Bentuk Sediaan Kemasan *</label>
            <select id="newImagePreset" class="w-full border border-slate-200 rounded-lg p-2.5 focus:outline-none focus:border-skyblue-500">
              <option value="sirup">Botol Sirup Obat (Syrup Bottle)</option>
              <option value="tablet">Strip Tablet Blister (Blister Strip)</option>
              <option value="kapsul">Pack Kapsul Dua Warna (Capsules)</option>
              <option value="vitamin">Botol Vitamin Suplemen (Vitamin Bottle)</option>
              <option value="p3k">Kotak P3K / Antiseptik (First Aid)</option>
            </select>
          </div>

          <div class="grid grid-cols-3 gap-3">
            <div>
              <label class="block font-semibold text-slate-700 mb-1">Satuan *</label>
              <input type="text" id="newUnit" required placeholder="Strip / Botol" class="w-full border border-slate-200 rounded-lg p-2.5 focus:outline-none focus:border-skyblue-500">
            </div>
            <div>
              <label class="block font-semibold text-slate-700 mb-1">Stok Awal *</label>
              <input type="number" id="newStock" required min="1" value="20" class="w-full border border-slate-200 rounded-lg p-2.5 focus:outline-none focus:border-skyblue-500">
            </div>
            <div>
              <label class="block font-semibold text-slate-700 mb-1">Harga (Rp) *</label>
              <input type="number" id="newPrice" required min="500" value="12000" class="w-full border border-slate-200 rounded-lg p-2.5 focus:outline-none focus:border-skyblue-500">
            </div>
          </div>

          <div>
            <label class="block font-semibold text-slate-700 mb-1">Aturan Pakai Default *</label>
            <input type="text" id="newDosage" required placeholder="Contoh: 3 x sehari 1 tablet sesudah makan." class="w-full border border-slate-200 rounded-lg p-2.5 focus:outline-none focus:border-skyblue-500">
          </div>

          <div class="pt-2 flex justify-end gap-2">
            <button type="button" onclick="closeAddMedicineModal()" class="px-4 py-2.5 border border-slate-200 rounded-xl font-bold text-slate-600 hover:bg-slate-50">Batal</button>
            <button type="submit" class="px-4 py-2.5 bg-crimson-600 hover:bg-crimson-700 text-white font-bold rounded-xl shadow-xs">Simpan &amp; Rilis Merek</button>
          </div>
        </form>
      </div>
    </div>
  </div>


  <!-- 3. MODAL POS WALK-IN KASIR -->
  <div id="walkinPosModal" class="fixed inset-0 z-50 overflow-y-auto hidden">
    <div class="flex items-center justify-center min-h-screen px-4 pt-4 pb-20 text-center sm:block sm:p-0">
      <div class="fixed inset-0 bg-slate-900/50 backdrop-blur-xs transition-opacity" onclick="closeWalkinPosModal()"></div>

      <div class="inline-block align-bottom bg-white rounded-2xl text-left overflow-hidden shadow-2xl transform transition-all sm:my-8 sm:align-middle sm:max-w-md sm:w-full">
        <div class="bg-skyblue-600 p-4 text-white flex items-center justify-between">
          <h3 class="font-bold text-sm">Buat Faktur Kasir (Walk-in)</h3>
          <button onclick="closeWalkinPosModal()" class="text-white hover:opacity-80">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
          </button>
        </div>

        <form onsubmit="submitWalkinPos(event)" class="p-6 space-y-4 text-xs">
          <div>
            <label class="block font-semibold text-slate-700 mb-1">Nama Pasien / Pembeli *</label>
            <input type="text" id="posPatientName" required placeholder="Pasien Walk-in / Umum" class="w-full border border-slate-200 rounded-lg p-2.5">
          </div>

          <div>
            <label class="block font-semibold text-slate-700 mb-1">Pilih Merek Obat *</label>
            <select id="posMedicineSelect" required class="w-full border border-slate-200 rounded-lg p-2.5">
              <!-- Rendered dynamically -->
            </select>
          </div>

          <div class="grid grid-cols-2 gap-3">
            <div>
              <label class="block font-semibold text-slate-700 mb-1">Kuantitas (Qty) *</label>
              <input type="number" id="posQty" required min="1" value="1" class="w-full border border-slate-200 rounded-lg p-2.5">
            </div>
            <div>
              <label class="block font-semibold text-slate-700 mb-1">Kasir</label>
              <input type="text" readonly value="Rina Melati" class="w-full bg-slate-100 border border-slate-200 rounded-lg p-2.5 font-bold">
            </div>
          </div>

          <div class="pt-2 flex justify-end gap-2">
            <button type="button" onclick="closeWalkinPosModal()" class="px-4 py-2.5 border border-slate-200 rounded-xl font-bold text-slate-600">Batal</button>
            <button type="submit" class="px-4 py-2.5 bg-skyblue-600 hover:bg-skyblue-700 text-white font-bold rounded-xl shadow-xs">Terbitkan Faktur</button>
          </div>
        </form>
      </div>
    </div>
  </div>


  <!-- 4. MODAL INSPEKSI RESEP DOKTER (ADMIN) -->
  <div id="prescriptionInspectModal" class="fixed inset-0 z-50 overflow-y-auto hidden">
    <div class="flex items-center justify-center min-h-screen px-4 pt-4 pb-20 text-center sm:block sm:p-0">
      <div class="fixed inset-0 bg-slate-900/50 backdrop-blur-xs transition-opacity" onclick="closePrescriptionInspectModal()"></div>

      <div class="inline-block align-bottom bg-white rounded-2xl text-left overflow-hidden shadow-2xl transform transition-all sm:my-8 sm:align-middle sm:max-w-md sm:w-full">
        <div class="bg-crimson-600 p-4 text-white flex items-center justify-between">
          <h3 class="font-bold text-sm">Verifikasi Foto Resep Dokter</h3>
          <button onclick="closePrescriptionInspectModal()" class="text-white">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
          </button>
        </div>

        <div class="p-6 space-y-4 text-xs">
          <div class="bg-slate-50 border border-slate-200 rounded-xl p-4 space-y-2">
            <div class="flex items-center justify-between text-[10px] font-bold text-slate-400">
              <span>LEMBAR RESEP RESMI DOKTER</span>
              <span class="text-emerald-600">✓ SAH</span>
            </div>
            <h4 class="font-bold text-slate-900 text-sm">Dr. H. Ahmad Subagyo, Sp.PD</h4>
            <p class="text-[11px] text-slate-500">SIP: 503/449/SIP.D/2026 • Rumah Sakit Medika Utama</p>
            <hr class="border-slate-200"/>
            <div class="bg-white p-3 rounded-lg font-mono text-slate-800 text-xs border border-slate-200 space-y-1">
              <p class="font-bold text-crimson-600">R/ Amoxicillin 500mg tab No. X</p>
              <p class="text-slate-600">S 3 dd tab I (Sesudah makan — habiskan)</p>
              <p class="text-[10px] text-slate-400 mt-2">Pro: Budi Santoso (34 Thn)</p>
            </div>
          </div>

          <div class="flex justify-end">
            <button onclick="closePrescriptionInspectModal()" class="px-4 py-2 bg-slate-800 text-white font-bold rounded-xl">
              Tutup Preview Resep
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>


  <!-- 5. MODAL PRATINJAU FAKTUR RESMI -->
  <div id="invoiceModal" class="fixed inset-0 z-50 overflow-y-auto hidden">
    <div class="flex items-center justify-center min-h-screen px-4 pt-4 pb-20 text-center sm:block sm:p-0">
      <div class="fixed inset-0 bg-slate-900/50 backdrop-blur-xs transition-opacity" onclick="closeInvoiceModal()"></div>

      <div class="inline-block align-bottom bg-white rounded-2xl text-left overflow-hidden shadow-2xl transform transition-all sm:my-8 sm:align-middle sm:max-w-2xl sm:w-full">
        <!-- Action Bar -->
        <div class="bg-slate-900 p-4 text-white flex items-center justify-between no-print">
          <span class="font-bold text-xs text-skyblue-400">FAKTUR RESMI APOTEK SEHATCARE</span>
          <div class="flex items-center gap-2">
            <button onclick="window.print()" class="bg-crimson-600 hover:bg-crimson-700 text-white font-bold px-3 py-1.5 rounded-lg text-xs flex items-center gap-1.5 shadow-xs">
              <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 17h2a2 2 0 002-2v-4a2 2 0 00-2-2H5a2 2 0 00-2 2v4a2 2 0 002 2h2m2 4h6a2 2 0 002-2v-4a2 2 0 00-2-2H9a2 2 0 00-2 2v4a2 2 0 002 2zm8-12V5a2 2 0 00-2-2H9a2 2 0 00-2 2v4h10z"/></svg>
              <span>Cetak Faktur</span>
            </button>
            <button onclick="closeInvoiceModal()" class="text-slate-400 hover:text-white">
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
            </button>
          </div>
        </div>

        <!-- Printable Canvas -->
        <div id="invoicePrintCanvas" class="p-8 bg-white space-y-6 text-slate-800">
          <!-- Rendered dynamically -->
        </div>
      </div>
    </div>
  </div>


  <!-- ========================================================================= -->
  <!-- JAVASCRIPT STATE & LOGIC                                                  -->
  <!-- ========================================================================= -->
  <script>
    // HIGH-QUALITY SVG ARTWORK GENERATOR FOR MEDICINE PACKAGING
    function getMedicineSVG(type, brandName, grade) {
      const isKeras = grade === 'Keras';
      const isBebas = grade === 'Bebas';

      if (type === 'sirup') {
        return `
          <svg class="w-full h-full" viewBox="0 0 300 180" fill="none" xmlns="http://www.w3.org/2000/svg">
            <rect width="300" height="180" fill="#F8FAFC"/>
            <!-- Medicine Box -->
            <rect x="50" y="25" width="90" height="135" rx="8" fill="#0EA5E9"/>
            <rect x="50" y="25" width="90" height="30" fill="#0284C7"/>
            <rect x="62" y="70" width="66" height="40" rx="4" fill="#FFFFFF"/>
            <text x="95" y="90" fill="#0284C7" font-size="10" font-weight="bold" text-anchor="middle">${brandName.substring(0, 10)}</text>
            <text x="95" y="102" fill="#64748B" font-size="8" text-anchor="middle">Syrup 100ml</text>
            
            <!-- Amber Glass Bottle -->
            <rect x="180" y="45" width="65" height="110" rx="10" fill="#78350F"/>
            <rect x="198" y="28" width="28" height="18" rx="3" fill="#D97706"/>
            <!-- White Cap -->
            <rect x="194" y="16" width="36" height="14" rx="3" fill="#F8FAFC" stroke="#CBD5E1" stroke-width="1"/>
            <!-- Bottle Label -->
            <rect x="184" y="65" width="57" height="60" rx="4" fill="#FFFFFF"/>
            <path d="M190 75 H235 M190 83 H225 M190 91 H230" stroke="#0EA5E9" stroke-width="2" stroke-linecap="round"/>
            
            <!-- Grade Badge Symbol -->
            <circle cx="225" cy="112" r="7" fill="${isKeras ? '#DC143C' : (isBebas ? '#10B981' : '#0EA5E9')}"/>
            ${isKeras ? '<text x="225" y="115" fill="#FFF" font-size="9" font-weight="bold" text-anchor="middle">K</text>' : ''}
          </svg>
        `;
      }

      if (type === 'tablet') {
        return `
          <svg class="w-full h-full" viewBox="0 0 300 180" fill="none" xmlns="http://www.w3.org/2000/svg">
            <rect width="300" height="180" fill="#F8FAFC"/>
            <!-- Pharmacy Box -->
            <rect x="40" y="30" width="110" height="120" rx="8" fill="#DC143C"/>
            <rect x="40" y="30" width="110" height="26" fill="#B91C1C"/>
            <rect x="52" y="70" width="86" height="42" rx="4" fill="#FFFFFF"/>
            <text x="95" y="88" fill="#DC143C" font-size="10" font-weight="bold" text-anchor="middle">${brandName.substring(0, 11)}</text>
            <text x="95" y="102" fill="#64748B" font-size="8" text-anchor="middle">Caplet Strip</text>

            <!-- Blister Foil Pack -->
            <rect x="170" y="35" width="90" height="110" rx="6" fill="#E2E8F0" stroke="#94A3B8" stroke-width="1.5"/>
            <!-- Silver Foil Lines -->
            <line x1="170" y1="72" x2="260" y2="72" stroke="#CBD5E1" stroke-width="1.5"/>
            <line x1="170" y1="108" x2="260" y2="108" stroke="#CBD5E1" stroke-width="1.5"/>

            <!-- Round Pills in Blister -->
            <circle cx="192" cy="54" r="11" fill="#FFFFFF" stroke="#CBD5E1" stroke-width="1"/>
            <circle cx="238" cy="54" r="11" fill="#FFFFFF" stroke="#CBD5E1" stroke-width="1"/>
            <circle cx="192" cy="90" r="11" fill="#FFFFFF" stroke="#CBD5E1" stroke-width="1"/>
            <circle cx="238" cy="90" r="11" fill="#FFFFFF" stroke="#CBD5E1" stroke-width="1"/>
            <circle cx="192" cy="126" r="11" fill="#FFFFFF" stroke="#CBD5E1" stroke-width="1"/>
            <circle cx="238" cy="126" r="11" fill="#FFFFFF" stroke="#CBD5E1" stroke-width="1"/>
          </svg>
        `;
      }

      if (type === 'kapsul') {
        return `
          <svg class="w-full h-full" viewBox="0 0 300 180" fill="none" xmlns="http://www.w3.org/2000/svg">
            <rect width="300" height="180" fill="#FFF1F3"/>
            <!-- Rx Hard Medicine Box -->
            <rect x="40" y="25" width="115" height="130" rx="8" fill="#FFFFFF" stroke="#FECDD3" stroke-width="2"/>
            <rect x="40" y="25" width="115" height="28" fill="#DC143C"/>
            <text x="97" y="43" fill="#FFFFFF" font-size="9" font-weight="bold" text-anchor="middle">OBAT KERAS (HARUS RESEP)</text>
            <text x="97" y="80" fill="#0F172A" font-size="11" font-weight="extrabold" text-anchor="middle">${brandName.substring(0, 11)}</text>
            
            <!-- Red Circle K Logo -->
            <circle cx="97" cy="115" r="14" fill="#DC143C"/>
            <circle cx="97" cy="115" r="12" fill="none" stroke="#FFFFFF" stroke-width="1.5"/>
            <text x="97" y="120" fill="#FFFFFF" font-size="14" font-weight="extrabold" text-anchor="middle">K</text>

            <!-- Blister Capsule Pack -->
            <rect x="175" y="30" width="85" height="120" rx="6" fill="#F1F5F9" stroke="#94A3B8" stroke-width="1.5"/>
            <!-- Capsules Two Tone (Red/White) -->
            <g transform="translate(190, 42)">
              <rect x="0" y="0" width="22" height="10" rx="5" fill="#DC143C"/>
              <rect x="0" y="10" width="22" height="10" rx="5" fill="#FFFFFF" stroke="#E2E8F0"/>
            </g>
            <g transform="translate(225, 42)">
              <rect x="0" y="0" width="22" height="10" rx="5" fill="#DC143C"/>
              <rect x="0" y="10" width="22" height="10" rx="5" fill="#FFFFFF" stroke="#E2E8F0"/>
            </g>
            <g transform="translate(190, 78)">
              <rect x="0" y="0" width="22" height="10" rx="5" fill="#DC143C"/>
              <rect x="0" y="10" width="22" height="10" rx="5" fill="#FFFFFF" stroke="#E2E8F0"/>
            </g>
            <g transform="translate(225, 78)">
              <rect x="0" y="0" width="22" height="10" rx="5" fill="#DC143C"/>
              <rect x="0" y="10" width="22" height="10" rx="5" fill="#FFFFFF" stroke="#E2E8F0"/>
            </g>
          </svg>
        `;
      }

      if (type === 'vitamin') {
        return `
          <svg class="w-full h-full" viewBox="0 0 300 180" fill="none" xmlns="http://www.w3.org/2000/svg">
            <rect width="300" height="180" fill="#F0F9FF"/>
            <!-- Vitamin Container Bottle -->
            <rect x="110" y="35" width="80" height="125" rx="14" fill="#FFFFFF" stroke="#BAE6FD" stroke-width="2"/>
            <!-- Cap -->
            <rect x="122" y="18" width="56" height="18" rx="4" fill="#0EA5E9"/>
            
            <!-- Shield Immune Icon -->
            <path d="M150 65 L170 75 V95 C170 110 150 120 150 120 C150 120 130 110 130 95 V75 L150 65 Z" fill="#0EA5E9"/>
            <path d="M150 78 V100 M140 89 H160" stroke="#FFFFFF" stroke-width="3" stroke-linecap="round"/>

            <text x="150" y="140" fill="#0369A1" font-size="9" font-weight="bold" text-anchor="middle">${brandName.substring(0, 12)}</text>
          </svg>
        `;
      }

      // Default P3K / Pack
      return `
        <svg class="w-full h-full" viewBox="0 0 300 180" fill="none" xmlns="http://www.w3.org/2000/svg">
          <rect width="300" height="180" fill="#F8FAFC"/>
          <rect x="70" y="30" width="160" height="120" rx="10" fill="#FFFFFF" stroke="#CBD5E1" stroke-width="2"/>
          <rect x="70" y="30" width="160" height="30" fill="#0EA5E9"/>
          <circle cx="150" cy="100" r="22" fill="#DC143C"/>
          <path d="M150 88 V112 M138 100 H162" stroke="#FFFFFF" stroke-width="4" stroke-linecap="round"/>
          <text x="150" y="140" fill="#475569" font-size="10" font-weight="bold" text-anchor="middle">${brandName}</text>
        </svg>
      `;
    }

    // INITIAL REALISTIC DATASET
    const state = {
      currentRole: 'pasien',
      activePatientTab: 'katalog',
      activeAdminTab: 'ringkasan',
      selectedCategory: 'Semua',
      hasPrescription: false,

      medicines: [
        { id: 1, brand: 'OBH Combi Batuk Berdahak', category: 'Batuk', grade: 'Bebas Terbatas', unit: 'Botol 100ml', stock: 24, price: 18500, svgType: 'sirup', dosage: '3 x sehari 1 sendok takar (15 ml) sesudah makan' },
        { id: 2, brand: 'Siladex Antitussive', category: 'Batuk', grade: 'Bebas Terbatas', unit: 'Botol 60ml', stock: 18, price: 16000, svgType: 'sirup', dosage: '3 x sehari 1 sendok takar (5 ml) sesudah makan' },
        { id: 3, brand: 'Decolgen Flu & Batuk', category: 'Pilek & Flu', grade: 'Bebas Terbatas', unit: 'Strip 4 Tablet', stock: 40, price: 4500, svgType: 'tablet', dosage: '3 x sehari 1 tablet sesudah makan' },
        { id: 4, brand: 'Sanmol Paracetamol 500mg', category: 'Meriang & Demam', grade: 'Bebas', unit: 'Strip 4 Tablet', stock: 35, price: 5000, svgType: 'tablet', dosage: '3-4 x sehari 1 tablet bila demam/nyeri, sesudah makan' },
        { id: 5, brand: 'Panadol Extra', category: 'Meriang & Demam', grade: 'Bebas', unit: 'Strip 10 Kaplet', stock: 22, price: 14500, svgType: 'tablet', dosage: '3-4 x sehari 1 kaplet sesudah makan, maks 8 kaplet/hari' },
        { id: 6, brand: 'New Diatabs', category: 'Diare', grade: 'Bebas', unit: 'Strip 4 Tablet', stock: 30, price: 3500, svgType: 'tablet', dosage: '2 tablet setiap selesai buang air besar, maks 12 tablet/hari' },
        { id: 7, brand: 'Entrostop Obat Diare', category: 'Diare', grade: 'Bebas', unit: 'Strip 12 Tablet', stock: 4, price: 9500, svgType: 'tablet', dosage: '2 tablet setiap kali setelah buang air besar' },
        { id: 8, brand: 'Imboost Force', category: 'Vitamin & Suplemen', grade: 'Bebas', unit: 'Strip 10 Kaplet', stock: 15, price: 42000, svgType: 'vitamin', dosage: '1 x sehari 1 kaplet sesudah makan pagi' },
        { id: 9, brand: 'Amoxicillin 500mg', category: 'Obat Keras', grade: 'Keras', unit: 'Strip 10 Kaplet', stock: 12, price: 12000, svgType: 'kapsul', dosage: '3 x sehari 1 kaplet tiap 8 jam sesudah makan — wajib dihabiskan' }
      ],

      cart: [
        { medicineId: 1, qty: 1 }
      ],

      orders: [
        {
          id: 'ORD-901',
          patientName: 'Budi Santoso',
          items: [{ medicineId: 4, brand: 'Sanmol Paracetamol 500mg', qty: 2, price: 5000, dosage: '3-4 x sehari 1 tablet bila demam/nyeri, sesudah makan' }],
          subtotal: 10000,
          deliveryFee: 0,
          total: 10000,
          delivery: 'Ambil di Apotek',
          address: 'Jl. Mawar No. 12',
          hasPrescription: false,
          status: 'Sedang Disiapkan',
          date: '24 Nov 2026 10:15',
          cashierName: 'Rina Melati'
        }
      ],

      invoices: [
        {
          invoiceNo: 'INV-2026-001',
          date: '24 Nov 2026 09:30',
          patientName: 'Siti Aminah',
          type: 'Walk-in POS',
          cashierName: 'Rina Melati',
          items: [
            { brand: 'OBH Combi Batuk Berdahak', qty: 1, price: 18500, dosage: '3 x sehari 1 sendok takar (15 ml) sesudah makan' }
          ],
          total: 18500
        }
      ],

      chats: [
        { sender: 'pasien', text: 'Halo Apoteker Rina, apakah Sanmol aman diminum bersama OBH Combi?', time: '10:00' },
        { sender: 'kasir', text: 'Halo Pak Budi! Ya, keduanya aman diminum bersamaan. Pastikan Sanmol diminum sesudah makan ya.', time: '10:02' }
      ]
    };

    const categories = ['Semua', 'Batuk', 'Pilek & Flu', 'Meriang & Demam', 'Diare', 'Vitamin & Suplemen', 'Obat Keras'];

    window.onload = function() {
      renderCategoryPills();
      renderPatientCatalog();
      renderCartDrawer();
      renderAdminDashboard();
      renderAdminStokTable();
      renderAdminOrdersTable();
      renderAdminInvoicesTable();
      renderChatMessages();
      renderPatientOrders();
    };

    // ROLE SWITCHER
    function switchRole(role) {
      state.currentRole = role;
      const vPasien = document.getElementById('viewPasien');
      const vAdmin = document.getElementById('viewAdmin');
      const btnP = document.getElementById('btnRolePasien');
      const btnA = document.getElementById('btnRoleAdmin');

      if (role === 'pasien') {
        vPasien.classList.remove('hidden');
        vAdmin.classList.add('hidden');
        btnP.className = "flex items-center gap-2 px-3.5 py-1.5 rounded-lg transition-all duration-200 bg-white text-slate-900 shadow-xs font-bold";
        btnA.className = "flex items-center gap-2 px-3.5 py-1.5 rounded-lg transition-all duration-200 text-slate-500 hover:text-slate-900";
        showToast('Beralih ke Tampilan Masyarakat (Pasien)');
      } else {
        vPasien.classList.add('hidden');
        vAdmin.classList.remove('hidden');
        btnP.className = "flex items-center gap-2 px-3.5 py-1.5 rounded-lg transition-all duration-200 text-slate-500 hover:text-slate-900";
        btnA.className = "flex items-center gap-2 px-3.5 py-1.5 rounded-lg transition-all duration-200 bg-white text-slate-900 shadow-xs font-bold";
        showToast('Beralih ke Web Dashboard Admin Apotek');
      }
    }

    // PASIEN TABS
    function switchPatientTab(tabName) {
      state.activePatientTab = tabName;
      ['katalog', 'pesanan', 'konsultasi'].forEach(t => {
        const content = document.getElementById('patientTab' + t.charAt(0).toUpperCase() + t.slice(1));
        const btn = document.getElementById('tabNav' + t.charAt(0).toUpperCase() + t.slice(1));

        if (t === tabName) {
          content.classList.remove('hidden');
          btn.className = "flex items-center gap-2 px-4 py-2.5 rounded-xl text-xs font-bold transition-all bg-skyblue-50 text-skyblue-600 border border-skyblue-200";
        } else {
          content.classList.add('hidden');
          btn.className = "flex items-center gap-2 px-4 py-2.5 rounded-xl text-xs font-semibold text-slate-600 hover:bg-slate-100 transition-all";
        }
      });
    }

    // PATIENT CATALOG & FILTERS
    function renderCategoryPills() {
      const container = document.getElementById('patientCategoryPills');
      container.innerHTML = categories.map(cat => `
        <button onclick="selectCategory('${cat}')" class="px-3.5 py-1.5 rounded-xl text-xs font-bold transition shrink-0 ${
          state.selectedCategory === cat
            ? 'bg-skyblue-500 text-white shadow-xs'
            : 'bg-slate-100 text-slate-600 hover:bg-slate-200'
        }">
          ${cat}
        </button>
      `).join('');
    }

    function selectCategory(cat) {
      state.selectedCategory = cat;
      renderCategoryPills();
      renderPatientCatalog();
    }

    function renderPatientCatalog() {
      const query = (document.getElementById('patientSearchInput').value || '').toLowerCase();
      const grid = document.getElementById('patientMedicineGrid');

      const filtered = state.medicines.filter(m => {
        const matchCat = state.selectedCategory === 'Semua' || m.category === state.selectedCategory;
        const matchQ = m.brand.toLowerCase().includes(query) || m.category.toLowerCase().includes(query);
        return matchCat && matchQ;
      });

      document.getElementById('patientCatalogCount').innerText = `Menampilkan ${filtered.length} produk`;

      if (filtered.length === 0) {
        grid.innerHTML = `
          <div class="col-span-full py-12 text-center text-slate-400 space-y-2">
            <svg class="w-10 h-10 mx-auto text-slate-300" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 13V6a2 2 0 00-2-2H6a2 2 0 00-2 2v7m16 0v5a2 2 0 01-2 2H6a2 2 0 01-2-2v-5m16 0h-2.586a1 1 0 00-.707.293l-2.414 2.414a1 1 0 01-.707.293h-3.172a1 1 0 01-.707-.293l-2.414-2.414A1 1 0 006.586 13H4"/></svg>
            <p class="font-bold text-xs">Merek obat tidak ditemukan.</p>
          </div>
        `;
        return;
      }

      grid.innerHTML = filtered.map(m => {
        let gradeClass = 'badge-bebas';
        let gradeLabel = 'Obat Bebas';
        if (m.grade === 'Bebas Terbatas') { gradeClass = 'badge-terbatas'; gradeLabel = 'Bebas Terbatas'; }
        if (m.grade === 'Keras') { gradeClass = 'badge-keras'; gradeLabel = 'Obat Keras (K)'; }

        const isLow = m.stock <= 5;
        const svgArtwork = getMedicineSVG(m.svgType, m.brand, m.grade);

        return `
          <div class="bg-white border border-slate-200 rounded-2xl overflow-hidden shadow-card hover:shadow-hover transition duration-300 flex flex-col justify-between">
            
            <!-- SVG Artwork Header -->
            <div class="h-44 bg-slate-50 relative overflow-hidden group border-b border-slate-100 flex items-center justify-center">
              ${svgArtwork}
              
              <!-- Badges -->
              <div class="absolute top-3 left-3 flex items-center gap-1.5">
                <span class="text-[10px] font-bold px-2.5 py-0.5 rounded-full shadow-xs ${gradeClass}">${gradeLabel}</span>
              </div>

              <div class="absolute bottom-3 right-3">
                <span class="text-[11px] font-bold px-2.5 py-1 rounded-lg backdrop-blur-md ${isLow ? 'bg-crimson-600 text-white' : 'bg-white/90 text-slate-900 border border-slate-200'} shadow-xs">
                  Stok: ${m.stock} ${m.unit}
                </span>
              </div>
            </div>

            <!-- Content Area -->
            <div class="p-5 flex-1 flex flex-col justify-between space-y-4">
              <div class="space-y-2">
                <div class="flex items-center justify-between">
                  <span class="text-[10px] font-bold text-skyblue-600 bg-skyblue-50 border border-skyblue-100 px-2 py-0.5 rounded uppercase">${m.category}</span>
                  <span class="text-[10px] text-slate-400 font-medium">Original BPOM</span>
                </div>

                <h3 class="font-bold text-slate-900 text-sm leading-snug">${m.brand}</h3>

                <div class="bg-slate-50 border border-slate-200 rounded-xl p-3 text-[11px] text-slate-700 space-y-1">
                  <span class="font-bold text-skybrand-700 block uppercase text-[10px] text-skyblue-600 tracking-wider flex items-center gap-1">
                    <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"/></svg>
                    <span>Aturan Pakai Dosis:</span>
                  </span>
                  <p class="leading-relaxed text-slate-600 font-medium">${m.dosage}</p>
                </div>
              </div>

              <div class="pt-3 border-t border-slate-100 flex items-center justify-between gap-2">
                <div>
                  <span class="text-[10px] text-slate-400 block font-medium">Harga / ${m.unit}</span>
                  <span class="text-sm font-extrabold text-slate-900">Rp ${m.price.toLocaleString('id-ID')}</span>
                </div>

                <div class="flex items-center gap-1.5">
                  <button onclick="askDosage('${m.brand}')" class="px-3 py-2 rounded-xl text-[11px] font-bold border border-skyblue-200 text-skyblue-600 hover:bg-skyblue-50 transition">
                    Tanya Dosis
                  </button>
                  <!-- SINGLE CART ICON BUTTON (NO DOUBLE PLUS) -->
                  <button onclick="addToCart(${m.id})" class="px-3.5 py-2 rounded-xl text-[11px] font-bold bg-crimson-600 hover:bg-crimson-700 text-white shadow-xs transition flex items-center gap-1.5">
                    <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 11V7a4 4 0 00-8 0v4M5 9h14l1 12H4L5 9z"/></svg>
                    <span>Keranjang</span>
                  </button>
                </div>
              </div>
            </div>

          </div>
        `;
      }).join('');
    }

    function askDosage(brandName) {
      switchPatientTab('konsultasi');
      sendPatientQuickQuestion(`Bagaimana dosis dan aturan minum yang tepat untuk obat ${brandName}?`);
    }

    // CART & DRAWER
    function addToCart(medId) {
      const med = state.medicines.find(m => m.id === medId);
      if (!med || med.stock <= 0) {
        showToast('Stok obat habis!', 'error');
        return;
      }

      const exist = state.cart.find(c => c.medicineId === medId);
      if (exist) {
        if (exist.qty < med.stock) {
          exist.qty++;
        } else {
          showToast('Mencapai batas sisa stok!', 'error');
          return;
        }
      } else {
        state.cart.push({ medicineId: medId, qty: 1 });
      }

      renderCartDrawer();
      showToast(`${med.brand} ditambahkan ke keranjang.`);
    }

    function toggleCartDrawer(show) {
      const drawer = document.getElementById('cartDrawer');
      if (show) {
        drawer.classList.remove('hidden');
        renderCartDrawer();
      } else {
        drawer.classList.add('hidden');
      }
    }

    function renderCartDrawer() {
      const list = document.getElementById('cartItemsList');
      const badge = document.getElementById('cartBadgeCount');
      const totalCount = state.cart.reduce((sum, item) => sum + item.qty, 0);
      badge.innerText = totalCount;

      if (state.cart.length === 0) {
        list.innerHTML = `
          <div class="py-12 text-center text-slate-400 space-y-2">
            <svg class="w-10 h-10 mx-auto text-slate-300" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 11V7a4 4 0 00-8 0v4M5 9h14l1 12H4L5 9z"/></svg>
            <p class="text-xs font-bold">Keranjang belanja masih kosong</p>
          </div>
        `;
        document.getElementById('cartTotalText').innerText = 'Rp 0';
        document.getElementById('cartPrescriptionBox').classList.add('hidden');
        return;
      }

      let subtotal = 0;
      let hasKeras = false;

      list.innerHTML = state.cart.map(c => {
        const med = state.medicines.find(m => m.id === c.medicineId);
        if (!med) return '';
        if (med.grade === 'Keras') hasKeras = true;
        const itemTotal = med.price * c.qty;
        subtotal += itemTotal;

        return `
          <div class="flex items-center gap-3 p-3 bg-slate-50 border border-slate-200 rounded-xl">
            <div class="w-12 h-12 rounded-lg bg-white border border-slate-200 shrink-0 overflow-hidden">
              ${getMedicineSVG(med.svgType, med.brand, med.grade)}
            </div>
            <div class="flex-1 min-w-0">
              <h4 class="font-bold text-slate-900 text-xs truncate">${med.brand}</h4>
              <p class="text-[10px] text-slate-500">Rp ${med.price.toLocaleString('id-ID')} / ${med.unit}</p>
            </div>
            <div class="flex items-center gap-2">
              <div class="flex items-center border border-slate-300 rounded-lg bg-white text-xs font-bold">
                <button onclick="updateCartQty(${med.id}, -1)" class="px-2 py-0.5 hover:bg-slate-100">-</button>
                <span class="px-2">${c.qty}</span>
                <button onclick="updateCartQty(${med.id}, 1)" class="px-2 py-0.5 hover:bg-slate-100">+</button>
              </div>
              <span class="text-xs font-bold text-slate-900 min-w-[60px] text-right">Rp ${itemTotal.toLocaleString('id-ID')}</span>
            </div>
          </div>
        `;
      }).join('');

      const pBox = document.getElementById('cartPrescriptionBox');
      if (hasKeras) pBox.classList.remove('hidden');
      else pBox.classList.add('hidden');

      const delivery = document.getElementById('checkoutDelivery').value;
      const fee = delivery === 'Antar ke Rumah' ? 8000 : 0;
      const total = subtotal + fee;

      document.getElementById('cartTotalText').innerText = `Rp ${total.toLocaleString('id-ID')}`;
    }

    function updateCartQty(medId, delta) {
      const c = state.cart.find(x => x.medicineId === medId);
      const m = state.medicines.find(x => x.id === medId);
      if (!c || !m) return;

      c.qty += delta;
      if (c.qty > m.stock) c.qty = m.stock;
      if (c.qty <= 0) state.cart = state.cart.filter(x => x.medicineId !== medId);
      renderCartDrawer();
    }

    function handlePrescriptionUploaded(e) {
      state.hasPrescription = true;
      document.getElementById('prescriptionPreviewCard').classList.remove('hidden');
      showToast('File resep dokter berhasil terlampir.');
    }

    function useSamplePrescription() {
      state.hasPrescription = true;
      document.getElementById('prescriptionPreviewCard').classList.remove('hidden');
      showToast('Contoh Foto Resep Dokter Dr. H. Ahmad Subagyo terpasang.');
    }

    function submitPatientOrder() {
      if (state.cart.length === 0) {
        showToast('Keranjang kosong!', 'error');
        return;
      }

      const hasKeras = state.cart.some(item => {
        const m = state.medicines.find(x => x.id === item.medicineId);
        return m && m.grade === 'Keras';
      });

      if (hasKeras && !state.hasPrescription) {
        showToast('Unggah resep dokter untuk obat keras!', 'error');
        return;
      }

      const pName = document.getElementById('checkoutPatientName').value || 'Budi Santoso';
      const delivery = document.getElementById('checkoutDelivery').value;
      const addr = document.getElementById('checkoutAddress').value || '-';

      let subtotal = 0;
      const items = state.cart.map(c => {
        const m = state.medicines.find(x => x.id === c.medicineId);
        subtotal += m.price * c.qty;
        return {
          medicineId: m.id,
          brand: m.brand,
          qty: c.qty,
          price: m.price,
          dosage: m.dosage
        };
      });

      const fee = delivery === 'Antar ke Rumah' ? 8000 : 0;
      const total = subtotal + fee;

      const newOrder = {
        id: 'ORD-' + Math.floor(100 + Math.random() * 900),
        patientName: pName,
        items: items,
        subtotal: subtotal,
        deliveryFee: fee,
        total: total,
        delivery: delivery,
        address: addr,
        hasPrescription: hasKeras,
        status: 'Menunggu Konfirmasi',
        date: new Date().toLocaleString('id-ID', { dateStyle: 'medium', timeStyle: 'short' }),
        cashierName: 'Rina Melati'
      };

      state.orders.unshift(newOrder);
      state.cart = [];
      state.hasPrescription = false;

      toggleCartDrawer(false);
      showToast('Pesanan berhasil dibuat!');

      renderPatientOrders();
      renderAdminDashboard();
      renderAdminOrdersTable();
      switchPatientTab('pesanan');
    }

    // PATIENT ORDERS LIST & DIGITAL DOSAGE CARDS
    function renderPatientOrders() {
      const container = document.getElementById('patientOrdersContainer');
      const badge = document.getElementById('patientOrderBadge');
      
      if (state.orders.length > 0) {
        badge.innerText = state.orders.length;
        badge.classList.remove('hidden');
      }

      if (state.orders.length === 0) {
        container.innerHTML = '<p class="text-xs text-slate-400 py-4">Belum ada riwayat pesanan.</p>';
        return;
      }

      container.innerHTML = state.orders.map(o => {
        let statusBadge = 'bg-amber-100 text-amber-800 border-amber-200';
        if (o.status === 'Sedang Disiapkan') statusBadge = 'bg-skyblue-100 text-skyblue-800 border-skyblue-200';
        if (o.status === 'Selesai & Faktur Terbit') statusBadge = 'bg-emerald-100 text-emerald-800 border-emerald-200';

        return `
          <div class="border border-slate-200 rounded-2xl p-5 bg-white shadow-card space-y-4">
            <div class="flex items-center justify-between border-b border-slate-100 pb-3">
              <div>
                <span class="font-extrabold text-sm text-slate-900">${o.id}</span>
                <span class="text-[11px] text-slate-400 ml-2">${o.date}</span>
              </div>
              <span class="text-[11px] font-bold px-3 py-1 rounded-full border ${statusBadge}">${o.status}</span>
            </div>

            <div class="space-y-1 text-xs text-slate-700">
              ${o.items.map(i => `<div class="flex justify-between"><span>• ${i.brand} (x${i.qty})</span><span class="font-bold">Rp ${(i.price * i.qty).toLocaleString('id-ID')}</span></div>`).join('')}
            </div>

            <!-- Visual Kartu Aturan Minum Obat Digital -->
            <div class="bg-gradient-to-r from-skyblue-50 to-sky-50 border border-skyblue-200 rounded-xl p-4 space-y-3">
              <div class="flex items-center justify-between">
                <span class="font-bold text-skyblue-700 text-xs flex items-center gap-1.5 uppercase tracking-wider">
                  <svg class="w-4 h-4 text-skyblue-600" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"/></svg>
                  <span>Kartu Aturan Minum Obat Digital</span>
                </span>
                <span class="text-[10px] bg-white text-skyblue-600 px-2 py-0.5 rounded font-bold border border-skyblue-200">Resmi Apotek</span>
              </div>

              <div class="grid grid-cols-1 sm:grid-cols-3 gap-2">
                <div class="bg-white p-2.5 rounded-lg border border-slate-200 text-center space-y-1">
                  <span class="text-xs">☀️ Pagi</span>
                  <p class="text-[10px] text-slate-500 font-medium">Sesudah Makan Pagi</p>
                </div>
                <div class="bg-white p-2.5 rounded-lg border border-slate-200 text-center space-y-1">
                  <span class="text-xs">🌤️ Siang</span>
                  <p class="text-[10px] text-slate-500 font-medium">Sesudah Makan Siang</p>
                </div>
                <div class="bg-white p-2.5 rounded-lg border border-slate-200 text-center space-y-1">
                  <span class="text-xs">🌙 Malam</span>
                  <p class="text-[10px] text-slate-500 font-medium">Sebelum Tidur</p>
                </div>
              </div>

              <div class="space-y-1 text-xs pt-1">
                ${o.items.map(i => `<p class="text-slate-700"><strong>• ${i.brand}:</strong> ${i.dosage}</p>`).join('')}
              </div>
            </div>

            <div class="flex items-center justify-between pt-2 text-xs border-t border-slate-100">
              <span class="text-slate-500 font-medium">Layanan: <strong>${o.delivery}</strong></span>
              <div class="flex items-center gap-3">
                <span class="font-extrabold text-slate-900 text-sm">Total: Rp ${o.total.toLocaleString('id-ID')}</span>
                ${o.status.includes('Selesai') ? `
                  <button onclick="viewInvoiceByOrderId('${o.id}')" class="bg-crimson-600 hover:bg-crimson-700 text-white font-bold px-3.5 py-1.5 rounded-xl text-xs shadow-xs transition flex items-center gap-1">
                    <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"/></svg>
                    <span>Lihat E-Faktur</span>
                  </button>
                ` : ''}
              </div>
            </div>
          </div>
        `;
      }).join('');
    }

    // CHAT & CONSULTATION
    function sendPatientQuickQuestion(text) {
      document.getElementById('patientChatInput').value = text;
    }

    function handlePatientSendMessage(e) {
      e.preventDefault();
      const input = document.getElementById('patientChatInput');
      const text = input.value.trim();
      if (!text) return;

      const time = new Date().toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' });
      state.chats.push({ sender: 'pasien', text: text, time: time });
      input.value = '';
      renderChatMessages();

      setTimeout(() => {
        state.chats.push({ 
          sender: 'kasir', 
          text: 'Halo! Saya apt. Rina Wulandari, S.Farm. Selalu pastikan konsumsi obat sesuai instruksi dosis di faktur ya.', 
          time: new Date().toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }) 
        });
        renderChatMessages();
      }, 1000);
    }

    function handleAdminSendMessage(e) {
      e.preventDefault();
      const input = document.getElementById('adminChatInput');
      const text = input.value.trim();
      if (!text) return;

      const time = new Date().toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' });
      state.chats.push({ sender: 'kasir', text: text, time: time });
      input.value = '';
      renderChatMessages();
    }

    function sendAdminQuickReply(text) {
      const time = new Date().toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' });
      state.chats.push({ sender: 'kasir', text: text, time: time });
      renderChatMessages();
    }

    function renderChatMessages() {
      const pBox = document.getElementById('patientChatBox');
      const aBox = document.getElementById('adminChatBox');

      const html = state.chats.map(c => {
        const isP = c.sender === 'pasien';
        return `
          <div class="flex flex-col ${isP ? 'items-end' : 'items-start'}">
            <div class="max-w-[80%] rounded-2xl px-4 py-2.5 text-xs shadow-xs ${
              isP ? 'bg-skyblue-500 text-white rounded-tr-none' : 'bg-white border border-slate-200 text-slate-800 rounded-tl-none'
            }">
              ${!isP ? '<p class="text-[10px] font-bold text-skyblue-600 mb-0.5">apt. Rina Wulandari, S.Farm.</p>' : ''}
              <p class="leading-relaxed">${c.text}</p>
              <span class="text-[9px] block text-right mt-1 opacity-70">${c.time}</span>
            </div>
          </div>
        `;
      }).join('');

      pBox.innerHTML = html;
      aBox.innerHTML = html;
      pBox.scrollTop = pBox.scrollHeight;
      aBox.scrollTop = aBox.scrollHeight;

      const last = state.chats[state.chats.length - 1];
      if (last) document.getElementById('adminLastChatText').innerText = last.text;
    }

    // ADMIN TABS & DASHBOARD
    function switchAdminTab(tabName) {
      state.activeAdminTab = tabName;
      ['ringkasan', 'stok', 'faktur', 'konsultasi'].forEach(t => {
        const content = document.getElementById('adminTab' + t.charAt(0).toUpperCase() + t.slice(1));
        const nav = document.getElementById('adminNav' + t.charAt(0).toUpperCase() + t.slice(1));

        if (t === tabName) {
          content.classList.remove('hidden');
          nav.className = "w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-xs font-bold bg-skyblue-50 text-skyblue-600 border border-skyblue-100 transition";
        } else {
          content.classList.add('hidden');
          nav.className = "w-full flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-xs font-medium text-slate-600 hover:bg-slate-50 transition";
        }
      });
    }

    function renderAdminDashboard() {
      const newOrders = state.orders.filter(o => o.status === 'Menunggu Konfirmasi' || o.status === 'Sedang Disiapkan').length;
      const lowStock = state.medicines.filter(m => m.stock <= 5).length;

      document.getElementById('statNewOrders').innerText = newOrders;
      document.getElementById('statTotalMedicines').innerText = state.medicines.length;
      document.getElementById('statLowStock').innerText = lowStock;
      document.getElementById('statIssuedInvoices').innerText = state.invoices.length;

      const stokBadge = document.getElementById('adminNavStokBadge');
      if (lowStock > 0) {
        stokBadge.innerText = lowStock;
        stokBadge.classList.remove('hidden');
      } else {
        stokBadge.classList.add('hidden');
      }
    }

    function renderAdminOrdersTable() {
      const tbody = document.getElementById('adminOrdersTableBody');
      if (state.orders.length === 0) {
        tbody.innerHTML = '<tr><td colspan="6" class="py-4 text-center text-slate-400">Belum ada pesanan masuk.</td></tr>';
        return;
      }

      tbody.innerHTML = state.orders.map(o => `
        <tr>
          <td class="py-3 px-3 font-bold text-slate-900">${o.id}</td>
          <td class="py-3 px-3 font-medium">${o.patientName}</td>
          <td class="py-3 px-3">
            <div class="space-y-0.5">
              <span>${o.items.map(i => i.brand).join(', ')}</span>
              ${o.hasPrescription ? `
                <button onclick="openPrescriptionInspectModal()" class="text-[10px] bg-crimson-50 text-crimson-600 border border-crimson-200 px-2 py-0.5 rounded font-bold block hover:bg-crimson-100">
                  Inspeksi Resep Dokter
                </button>
              ` : ''}
            </div>
          </td>
          <td class="py-3 px-3"><span class="bg-slate-100 text-slate-700 px-2 py-0.5 rounded text-[10px] font-semibold">${o.delivery}</span></td>
          <td class="py-3 px-3"><span class="font-bold ${o.status.includes('Selesai') ? 'text-emerald-600' : 'text-amber-600'}">${o.status}</span></td>
          <td class="py-3 px-3 text-right">
            ${!o.status.includes('Selesai') ? `
              <button onclick="approveOrder('${o.id}')" class="bg-crimson-600 hover:bg-crimson-700 text-white font-bold text-[11px] px-3 py-1.5 rounded-lg shadow-xs transition">
                Konfirmasi &amp; Terbitkan Faktur
              </button>
            ` : '<span class="text-emerald-600 font-bold text-[11px]">✓ Selesai</span>'}
          </td>
        </tr>
      `).join('');
    }

    function openPrescriptionInspectModal() {
      document.getElementById('prescriptionInspectModal').classList.remove('hidden');
    }

    function closePrescriptionInspectModal() {
      document.getElementById('prescriptionInspectModal').classList.add('hidden');
    }

    function approveOrder(orderId) {
      const order = state.orders.find(o => o.id === orderId);
      if (!order) return;

      order.items.forEach(it => {
        const med = state.medicines.find(m => m.id === it.medicineId);
        if (med) med.stock = Math.max(0, med.stock - it.qty);
      });

      order.status = 'Selesai & Faktur Terbit';

      const invNo = 'INV-2026-' + Math.floor(100 + Math.random() * 900);
      const newInv = {
        invoiceNo: invNo,
        orderId: order.id,
        date: new Date().toLocaleString('id-ID', { dateStyle: 'medium', timeStyle: 'short' }),
        patientName: order.patientName,
        type: 'Online Order',
        cashierName: 'Rina Melati',
        items: order.items,
        total: order.total
      };

      state.invoices.unshift(newInv);

      showToast(`Pesanan ${orderId} disetujui! Faktur ${invNo} terbit.`);

      renderAdminDashboard();
      renderAdminOrdersTable();
      renderAdminStokTable();
      renderPatientCatalog();
      renderAdminInvoicesTable();
      renderPatientOrders();
    }

    // ADMIN STOK TABLE & ADD MEDICINE
    function renderAdminStokTable() {
      const query = (document.getElementById('adminStokSearch')?.value || '').toLowerCase();
      const cat = document.getElementById('adminStokCategory')?.value || 'Semua';
      const tbody = document.getElementById('adminStokTableBody');

      const filtered = state.medicines.filter(m => {
        const matchCat = cat === 'Semua' || m.category === cat;
        const matchQ = m.brand.toLowerCase().includes(query);
        return matchCat && matchQ;
      });

      tbody.innerHTML = filtered.map(m => {
        const isLow = m.stock <= 5;
        return `
          <tr class="${isLow ? 'bg-crimson-50/40' : 'hover:bg-slate-50'}">
            <td class="py-2.5 px-3">
              <div class="w-10 h-10 rounded-lg bg-white border border-slate-200 overflow-hidden shadow-xs">
                ${getMedicineSVG(m.svgType, m.brand, m.grade)}
              </div>
            </td>
            <td class="py-2.5 px-3 font-bold text-slate-900">${m.brand}</td>
            <td class="py-2.5 px-3">${m.category}</td>
            <td class="py-2.5 px-3 font-semibold ${m.grade === 'Keras' ? 'text-crimson-600' : 'text-slate-700'}">${m.grade}</td>
            <td class="py-2.5 px-3">${m.unit}</td>
            <td class="py-2.5 px-3 font-bold">Rp ${m.price.toLocaleString('id-ID')}</td>
            <td class="py-2.5 px-3">
              <span class="font-extrabold px-2 py-0.5 rounded ${isLow ? 'bg-crimson-600 text-white' : 'text-slate-900'}">
                ${m.stock}
              </span>
            </td>
            <td class="py-2.5 px-3 text-slate-500 text-[11px] max-w-xs truncate">${m.dosage}</td>
            <td class="py-2.5 px-3 text-center">
              <button onclick="addStockQuick(${m.id})" class="bg-skyblue-50 hover:bg-skyblue-100 text-skyblue-700 border border-skyblue-200 font-bold px-2.5 py-1 rounded-lg text-[10px]">
                Restok (+10)
              </button>
            </td>
          </tr>
        `;
      }).join('');
    }

    function addStockQuick(medId) {
      const med = state.medicines.find(m => m.id === medId);
      if (med) {
        med.stock += 10;
        renderAdminStokTable();
        renderPatientCatalog();
        renderAdminDashboard();
        showToast(`Stok ${med.brand} bertambah 10 unit.`);
      }
    }

    function openAddMedicineModal() {
      document.getElementById('addMedicineModal').classList.remove('hidden');
    }

    function closeAddMedicineModal() {
      document.getElementById('addMedicineModal').classList.add('hidden');
    }

    function submitNewMedicine(e) {
      e.preventDefault();
      const brand = document.getElementById('newBrand').value;
      const category = document.getElementById('newCategory').value;
      const grade = document.getElementById('newGrade').value;
      const unit = document.getElementById('newUnit').value;
      const stock = parseInt(document.getElementById('newStock').value);
      const price = parseInt(document.getElementById('newPrice').value);
      const dosage = document.getElementById('newDosage').value;
      const svgType = document.getElementById('newImagePreset').value;

      const newMed = {
        id: Date.now(),
        brand: brand,
        category: category,
        grade: grade,
        unit: unit,
        stock: stock,
        price: price,
        svgType: svgType,
        dosage: dosage
      };

      state.medicines.unshift(newMed);
      closeAddMedicineModal();
      showToast(`Merek '${brand}' berhasil ditambahkan!`);

      renderAdminStokTable();
      renderPatientCatalog();
      renderAdminDashboard();
    }

    // WALK-IN POS & INVOICES
    function renderAdminInvoicesTable() {
      const tbody = document.getElementById('adminInvoicesTableBody');
      tbody.innerHTML = state.invoices.map(inv => `
        <tr>
          <td class="py-3 px-3 font-mono font-bold text-skyblue-600">${inv.invoiceNo}</td>
          <td class="py-3 px-3 text-slate-500">${inv.date}</td>
          <td class="py-3 px-3 font-bold text-slate-900">${inv.patientName}</td>
          <td class="py-3 px-3"><span class="bg-slate-100 text-slate-700 px-2 py-0.5 rounded text-[10px] font-semibold">${inv.type}</span></td>
          <td class="py-3 px-3">${inv.cashierName}</td>
          <td class="py-3 px-3 font-bold">Rp ${inv.total.toLocaleString('id-ID')}</td>
          <td class="py-3 px-3 text-center">
            <button onclick="renderInvoiceModal('${inv.invoiceNo}')" class="bg-slate-800 hover:bg-slate-900 text-white font-bold px-2.5 py-1 rounded text-[10px]">
              Lihat Faktur
            </button>
          </td>
        </tr>
      `).join('');
    }

    function openWalkinPosModal() {
      const select = document.getElementById('posMedicineSelect');
      select.innerHTML = state.medicines.map(m => `<option value="${m.id}">${m.brand} - Rp ${m.price.toLocaleString('id-ID')} (Stok: ${m.stock})</option>`).join('');
      document.getElementById('walkinPosModal').classList.remove('hidden');
    }

    function closeWalkinPosModal() {
      document.getElementById('walkinPosModal').classList.add('hidden');
    }

    function submitWalkinPos(e) {
      e.preventDefault();
      const pName = document.getElementById('posPatientName').value;
      const medId = parseInt(document.getElementById('posMedicineSelect').value);
      const qty = parseInt(document.getElementById('posQty').value);

      const med = state.medicines.find(m => m.id === medId);
      if (!med || med.stock < qty) {
        showToast('Stok tidak mencukupi!', 'error');
        return;
      }

      med.stock -= qty;

      const invNo = 'INV-2026-' + Math.floor(100 + Math.random() * 900);
      const newInv = {
        invoiceNo: invNo,
        date: new Date().toLocaleString('id-ID', { dateStyle: 'medium', timeStyle: 'short' }),
        patientName: pName,
        type: 'Walk-in POS',
        cashierName: 'Rina Melati',
        items: [{ brand: med.brand, qty: qty, price: med.price, dosage: med.dosage }],
        total: med.price * qty
      };

      state.invoices.unshift(newInv);
      closeWalkinPosModal();
      showToast(`Faktur ${invNo} terbit.`);

      renderAdminDashboard();
      renderAdminInvoicesTable();
      renderAdminStokTable();
      renderPatientCatalog();
      renderInvoiceModal(invNo);
    }

    function viewInvoiceByOrderId(orderId) {
      const inv = state.invoices.find(i => i.orderId === orderId) || state.invoices[0];
      if (inv) renderInvoiceModal(inv.invoiceNo);
    }

    function renderInvoiceModal(invNo) {
      const inv = state.invoices.find(i => i.invoiceNo === invNo);
      if (!inv) return;

      const canvas = document.getElementById('invoicePrintCanvas');
      canvas.innerHTML = `
        <div class="border-b-2 border-slate-900 pb-4 flex items-center justify-between">
          <div>
            <h2 class="text-lg font-extrabold text-slate-900 uppercase tracking-tight">Apotek SehatCare</h2>
            <p class="text-xs text-slate-500">Sistem Informasi &amp; Layanan Apotek Terpadu</p>
            <p class="text-[11px] text-slate-400">Jl. Kesehatan Raya No. 45 • Telp: (021) 555-0192</p>
          </div>
          <div class="text-right">
            <span class="inline-block bg-crimson-600 text-white font-bold text-[10px] px-2.5 py-0.5 rounded">FAKTUR RESMI</span>
            <p class="font-mono text-sm font-bold text-slate-900 mt-1">${inv.invoiceNo}</p>
            <p class="text-[11px] text-slate-400">${inv.date}</p>
          </div>
        </div>

        <div class="grid grid-cols-2 gap-4 text-xs bg-slate-50 p-3 rounded-xl border border-slate-200">
          <div>
            <span class="text-slate-400 block text-[10px]">Nama Pasien:</span>
            <strong class="text-slate-900 text-sm">${inv.patientName}</strong>
          </div>
          <div>
            <span class="text-slate-400 block text-[10px]">Kasir Melayani:</span>
            <strong class="text-slate-900">${inv.cashierName} (${inv.type})</strong>
          </div>
        </div>

        <table class="w-full text-xs text-left border-collapse">
          <thead class="bg-slate-800 text-slate-100 font-bold">
            <tr>
              <th class="py-2 px-3">Merek Obat</th>
              <th class="py-2 px-3">Instruksi Cara Minum Obat</th>
              <th class="py-2 px-3 text-center">Qty</th>
              <th class="py-2 px-3 text-right">Subtotal</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-slate-200">
            ${inv.items.map(i => `
              <tr>
                <td class="py-2.5 px-3 font-bold text-slate-900">${i.brand}</td>
                <td class="py-2.5 px-3 text-skyblue-700 font-medium">${i.dosage}</td>
                <td class="py-2.5 px-3 text-center font-bold">${i.qty}</td>
                <td class="py-2.5 px-3 text-right font-bold">Rp ${(i.price * i.qty).toLocaleString('id-ID')}</td>
              </tr>
            `).join('')}
          </tbody>
        </table>

        <div class="flex justify-end pt-3 border-t-2 border-slate-900 text-sm">
          <div class="text-right">
            <span class="text-xs text-slate-500 font-semibold block">Total Pembayaran:</span>
            <span class="text-lg font-extrabold text-crimson-600">Rp ${inv.total.toLocaleString('id-ID')}</span>
          </div>
        </div>

        <div class="pt-4 border-t border-slate-100 text-center text-xs text-slate-500 space-y-0.5">
          <p class="font-bold text-slate-700">Semoga Lekas Sembuh!</p>
          <p class="text-[10px]">Simpan faktur ini sebagai bukti instruksi dosis konsumsi obat Anda.</p>
        </div>
      `;

      document.getElementById('invoiceModal').classList.remove('hidden');
    }

    function closeInvoiceModal() {
      document.getElementById('invoiceModal').classList.add('hidden');
    }

    // TOAST NOTIFICATIONS
    function showToast(msg, type = 'success') {
      const container = document.getElementById('toastContainer');
      const toast = document.createElement('div');
      
      const bg = type === 'error' ? 'bg-crimson-600' : 'bg-slate-900';

      toast.className = `${bg} text-white text-xs font-bold px-4 py-3 rounded-xl shadow-2xl flex items-center gap-2 transition-all duration-300 translate-y-2 opacity-0 pointer-events-auto`;
      toast.innerHTML = `
        <span class="w-2 h-2 rounded-full bg-skyblue-400"></span>
        <span>${msg}</span>
      `;

      container.appendChild(toast);

      setTimeout(() => toast.classList.remove('translate-y-2', 'opacity-0'), 10);
      setTimeout(() => {
        toast.classList.add('opacity-0', 'translate-y-2');
        setTimeout(() => toast.remove(), 300);
      }, 2800);
    }
  </script>
</body>
</html>
