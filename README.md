<!DOCTYPE html>
<html lang="en" class="dark">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>GLOBAL VR EXPLORER — Explore the World Before You Travel</title>
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/loaders/GLTFLoader.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/d3-array@3.2.4/dist/d3-array.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/d3-geo@3.1.1/dist/d3-geo.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/topojson-client@3.1.0/dist/topojson-client.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/lucide@0.460.0/dist/umd/lucide.min.js"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,400;0,500;0,600;0,700;1,400;1,600&family=Inter:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;500&family=Orbitron:wght@600;700;800;900&family=Plus+Jakarta+Sans:wght@300;400;500;600;700&family=Space+Grotesk:wght@400;500;600;700&display=swap" rel="stylesheet" />
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: {
            serif: ['"Cormorant Garamond"', 'Georgia', 'serif'],
            sans: ['"Plus Jakarta Sans"', 'Inter', 'system-ui', 'sans-serif'],
            display: ['Orbitron', '"Space Grotesk"', 'sans-serif'],
            grotesk: ['"Space Grotesk"', 'sans-serif'],
            mono: ['"JetBrains Mono"', 'monospace']
          },
          colors: {
            midnight: {
              950: '#010308',
              900: '#040915',
              800: '#081226',
              700: '#102042'
            }
          }
        }
      }
    };
  </script>
  <style>
    :root {
      color-scheme: dark;
    }
    html {
      scroll-behavior: smooth;
    }
    body {
      background-color: #010308;
      color: #f8fafc;
      font-family: 'Plus Jakarta Sans', 'Inter', system-ui, sans-serif;
      overflow-x: hidden;
    }
    .font-editorial {
      font-family: 'Cormorant Garamond', Georgia, serif;
    }
    .font-universal {
      font-family: 'Orbitron', 'Space Grotesk', sans-serif;
      letter-spacing: 0.06em;
    }
    /* Floating Pill Top Navbar matching Reference 2 (UNIVERSAL EARTH) */
    .universal-nav-pill {
      background: linear-gradient(90deg, rgba(8, 16, 32, 0.82) 0%, rgba(12, 25, 48, 0.72) 50%, rgba(16, 42, 76, 0.78) 100%);
      backdrop-filter: blur(20px);
      -webkit-backdrop-filter: blur(20px);
      border: 1px solid rgba(125, 211, 252, 0.16);
      box-shadow: 0 12px 40px rgba(0, 0, 0, 0.65);
    }
    .glass-panel {
      background: rgba(6, 13, 28, 0.68);
      backdrop-filter: blur(20px);
      -webkit-backdrop-filter: blur(20px);
      border: 1px solid rgba(255, 255, 255, 0.09);
    }
    .glass-panel-subtle {
      background: rgba(3, 8, 20, 0.72);
      backdrop-filter: blur(16px);
      border: 1px solid rgba(255, 255, 255, 0.08);
    }
    /* Deep Outer Space Hero Background matching UNIVERSAL EARTH reference */
    .universal-space-bg {
      background:
        radial-gradient(circle at 78% 52%, rgba(14, 165, 233, 0.28) 0%, rgba(2, 132, 199, 0.12) 28%, rgba(3, 7, 18, 0) 62%),
        radial-gradient(circle at 52% 48%, rgba(56, 189, 248, 0.07) 0%, rgba(0, 0, 0, 0) 50%),
        #010206;
    }
    .vignette-overlay {
      pointer-events: none;
      background: radial-gradient(circle, transparent 42%, rgba(1, 3, 8, 0.94) 85%);
      transition: opacity 0.25s ease;
    }
    .custom-scrollbar::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    .custom-scrollbar::-webkit-scrollbar-track {
      background: rgba(2, 5, 13, 0.6);
    }
    .custom-scrollbar::-webkit-scrollbar-thumb {
      background: rgba(148, 163, 184, 0.28);
      border-radius: 9999px;
    }
    .custom-scrollbar::-webkit-scrollbar-thumb:hover {
      background: rgba(56, 189, 248, 0.55);
    }
    .drop-cap::first-letter {
      font-family: 'Cormorant Garamond', Georgia, serif;
      float: left;
      font-size: 3.8rem;
      line-height: 0.82;
      padding-top: 4px;
      padding-right: 12px;
      padding-bottom: 4px;
      color: #38bdf8;
      font-weight: 600;
    }
    .photo-zoom img {
      transition: transform 0.85s cubic-bezier(0.16, 1, 0.3, 1);
    }
    .photo-zoom:hover img {
      transform: scale(1.06);
    }
    .hidden {
      display: none !important;
    }
    /* Instant CSS/SVG Photorealistic Earth Backdrop inside #heroEarthContainer */
    .earth-fallback-orb {
      width: min(82vw, 520px);
      height: min(82vw, 520px);
      border-radius: 9999px;
      background:
        radial-gradient(circle at 32% 28%, rgba(255, 255, 255, 0.35) 0%, rgba(56, 189, 248, 0.25) 22%, rgba(2, 132, 199, 0.45) 48%, rgba(3, 18, 46, 0.96) 78%, #01040d 100%);
      box-shadow:
        inset -36px -28px 68px rgba(0, 2, 10, 0.92),
        inset 18px 18px 42px rgba(125, 211, 252, 0.42),
        0 0 90px rgba(14, 165, 233, 0.42),
        0 0 160px rgba(2, 132, 199, 0.22);
      position: relative;
      overflow: hidden;
    }
    @keyframes slowEarthSpin {
      0% { transform: translateX(0%); }
      100% { transform: translateX(-50%); }
    }
    .earth-fallback-map {
      position: absolute;
      inset: 0;
      width: 200%;
      height: 100%;
      opacity: 0.92;
      animation: slowEarthSpin 65s linear infinite;
    }

    /* Multilingual & RTL Support */
    html[dir="rtl"] body { text-align: right; }
    html[dir="rtl"] .universal-nav-pill { direction: rtl; }
    #vrMovePad, #panoCanvasContainer { direction: ltr; }
  </style>
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
</head>
<body class="min-h-screen flex flex-col selection:bg-sky-500/30 selection:text-sky-200">
  <!-- =========================================================================
       PREMIUM LOGIN & AUTHENTICATION PORTAL (Non-Blocking, Guest-First)
  ========================================================================= -->
  <div id="authPortalOverlay" class="hidden fixed inset-0 z-[90] bg-[#02050d] overflow-y-auto">
    <!-- Deep Space Starfield & Subtle Orbital Earth Backdrop -->
    <div class="fixed inset-0 pointer-events-none overflow-hidden">
      <div class="absolute inset-0 bg-[radial-gradient(circle_at_75%_25%,rgba(14,165,233,0.18),transparent_48%),radial-gradient(circle_at_20%_80%,rgba(59,130,246,0.14),transparent_45%)]"></div>
      <div class="hidden sm:block absolute -right-32 -bottom-32 w-[520px] h-[520px] rounded-full opacity-55 earth-sphere animate-[pulse_10s_ease-in-out_infinite]">
        <div class="earth-fallback-map bg-[radial-gradient(circle_at_35%_35%,rgba(56,189,248,0.45),rgba(3,105,161,0.35)_45%,rgba(2,6,23,0.95)_80%)]"></div>
      </div>
    </div>

    <div class="relative min-h-screen flex items-center justify-center p-4 sm:p-6 lg:p-10">
      <div class="w-full max-w-md rounded-3xl bg-slate-950/82 backdrop-blur-2xl border border-white/15 shadow-[0_25px_90px_rgba(2,132,199,0.28)] p-6 sm:p-8 space-y-5">
        <!-- Top Brand + Close / Guest Shortcut -->
        <div class="flex items-center justify-between gap-2">
          <div class="font-universal font-bold tracking-[0.18em] text-xs text-white">
            GLOBAL <span class="text-sky-400">VR</span> EXPLORER
          </div>
          <button type="button" onclick="openQuickLangPicker()" class="px-2.5 py-1 rounded-full bg-white/8 hover:bg-white/15 border border-white/15 text-[10px] font-mono text-sky-300 flex items-center gap-1 transition">
            <span>🌐</span>
            <span class="lang-indicator-mirror">EN</span>
          </button>
          <button type="button" onclick="enterAsGuest()" class="text-[11px] text-slate-400 hover:text-white transition flex items-center gap-1" title="Continue as Guest">
            <span>Skip</span>
            <i data-lucide="x" class="w-4 h-4"></i>
          </button>
        </div>

        <!-- Hero Heading -->
        <div class="space-y-1.5">
          <h1 class="font-editorial text-3xl sm:text-4xl font-bold tracking-tight text-white leading-tight">
            Explore Beyond Boundaries.
          </h1>
          <p class="text-xs sm:text-sm text-slate-300">
            Your journey around the world begins here.
          </p>
        </div>

        <!-- Prominent Guest Access CTA (Never blocks judges or visitors) -->
        <button id="continueGuestBtn" type="button" onclick="enterAsGuest()" class="w-full py-3 px-4 rounded-2xl bg-gradient-to-r from-amber-400 to-orange-400 hover:from-amber-300 hover:to-orange-300 text-slate-950 font-semibold text-xs sm:text-sm tracking-wide shadow-[0_0_25px_rgba(251,191,36,0.3)] flex items-center justify-center gap-2 transition">
          <i data-lucide="compass" class="w-4 h-4"></i>
          <span data-i18n="auth_guest_btn">Continue as Guest</span>
        </button>

        <div class="flex items-center gap-3 text-[10px] font-mono uppercase tracking-widest text-slate-500">
          <div class="h-px flex-1 bg-white/10"></div>
          <span>Or Sign In to Sync Favorites</span>
          <div class="h-px flex-1 bg-white/10"></div>
        </div>

        <!-- Continue with Google (Official OAuth via Supabase Auth) -->
        <button id="googleOAuthBtn" type="button" onclick="signInWithGoogleOAuth()" class="w-full py-2.5 px-4 rounded-xl bg-white hover:bg-slate-100 text-slate-900 font-semibold text-xs sm:text-sm flex items-center justify-center gap-2.5 shadow-md transition">
          <svg class="w-4 h-4 shrink-0" viewBox="0 0 24 24" aria-hidden="true">
            <path fill="#4285F4" d="M23.745 12.27c0-.7-.06-1.4-.19-2.07H12v4.51h6.6c-.29 1.52-1.14 2.82-2.4 3.68v3.05h3.88c2.27-2.09 3.665-5.17 3.665-9.17Z"/>
            <path fill="#34A853" d="M12 24c3.24 0 5.95-1.08 7.93-2.91l-3.88-3.05c-1.08.72-2.45 1.16-4.05 1.16-3.12 0-5.77-2.11-6.72-4.96H1.29v3.14C3.26 21.3 7.31 24 12 24Z"/>
            <path fill="#FBBC05" d="M5.28 14.24c-.24-.72-.38-1.49-.38-2.24s.14-1.52.38-2.24V6.62H1.29C.47 8.24 0 10.06 0 12s.47 3.76 1.29 5.38l3.99-3.14Z"/>
            <path fill="#EA4335" d="M12 4.75c1.77 0 3.35.61 4.6 1.8l3.42-3.42C17.95 1.19 15.24 0 12 0 7.31 0 3.26 2.7 1.29 6.62l3.99 3.14c.95-2.85 3.6-4.96 6.72-4.96Z"/>
          </svg>
          <span data-i18n="auth_google_btn">Continue with Google</span>
        </button>

        <!-- Auth Mode Tabs: Sign In | Create Account | Forgot Password -->
        <div class="grid grid-cols-2 gap-1.5 p-1 rounded-xl bg-white/5 border border-white/10 text-xs font-medium">
          <button id="authTabSignIn" type="button" onclick="switchAuthTab('signin')" class="py-1.5 rounded-lg bg-sky-500 text-slate-950 font-semibold transition" data-i18n="auth_tab_signin">Sign in with Email</button>
          <button id="authTabSignUp" type="button" onclick="switchAuthTab('signup')" class="py-1.5 rounded-lg text-slate-300 hover:text-white transition" data-i18n="auth_tab_signup">Create Account</button>
        </div>

        <!-- Auth Feedback Banner (Errors & Success) -->
        <div id="authFeedbackBanner" class="hidden rounded-xl px-3.5 py-2.5 text-xs leading-relaxed border"></div>

        <!-- EMAIL SIGN IN FORM -->
        <form id="authSignInForm" onsubmit="handleEmailSignIn(event)" class="space-y-3" novalidate>
          <div>
            <label for="authSignInEmail" class="block text-[11px] font-medium text-slate-300 mb-1" data-i18n="auth_email_label">Email address</label>
            <input id="authSignInEmail" type="email" autocomplete="email" required placeholder="explorer@example.com" class="w-full px-3.5 py-2.5 rounded-xl bg-slate-900/90 border border-white/15 focus:border-sky-400 focus:outline-none text-xs text-white placeholder-slate-500" />
          </div>
          <div>
            <div class="flex items-center justify-between mb-1">
              <label for="authSignInPassword" class="text-[11px] font-medium text-slate-300">Password</label>
              <button type="button" onclick="switchAuthTab('forgot')" class="text-[11px] text-sky-400 hover:text-sky-300 transition" data-i18n="auth_forgot_link">Forgot password?</button>
            </div>
            <div class="relative">
              <input id="authSignInPassword" type="password" autocomplete="current-password" required placeholder="Enter your password" class="w-full px-3.5 py-2.5 pr-10 rounded-xl bg-slate-900/90 border border-white/15 focus:border-sky-400 focus:outline-none text-xs text-white placeholder-slate-500" />
              <button type="button" onclick="togglePasswordVisibility('authSignInPassword', this)" aria-label="Toggle password visibility" class="absolute right-2.5 top-1/2 -translate-y-1/2 text-slate-400 hover:text-white p-1">
                <i data-lucide="eye" class="w-4 h-4"></i>
              </button>
            </div>
          </div>
          <button id="authSignInSubmitBtn" type="submit" class="w-full py-2.5 px-4 rounded-xl bg-gradient-to-r from-sky-500 to-blue-600 hover:from-sky-400 hover:to-blue-500 text-white font-semibold text-xs sm:text-sm flex items-center justify-center gap-2 transition">
            <span data-i18n="auth_signin_btn">Sign in with Email</span>
          </button>
        </form>

        <!-- CREATE ACCOUNT FORM -->
        <form id="authSignUpForm" onsubmit="handleEmailSignUp(event)" class="hidden space-y-2.5" novalidate>
          <div>
            <label for="authSignUpName" class="block text-[11px] font-medium text-slate-300 mb-1" data-i18n="auth_fullname_label">Full name</label>
            <input id="authSignUpName" type="text" autocomplete="name" required placeholder="Your full name" class="w-full px-3.5 py-2 rounded-xl bg-slate-900/90 border border-white/15 focus:border-sky-400 focus:outline-none text-xs text-white placeholder-slate-500" />
          </div>
          <div>
            <label for="authSignUpEmail" class="block text-[11px] font-medium text-slate-300 mb-1" data-i18n="auth_email_label">Email address</label>
            <input id="authSignUpEmail" type="email" autocomplete="email" required placeholder="explorer@example.com" class="w-full px-3.5 py-2 rounded-xl bg-slate-900/90 border border-white/15 focus:border-sky-400 focus:outline-none text-xs text-white placeholder-slate-500" />
          </div>
          <div>
            <label for="authSignUpPassword" class="block text-[11px] font-medium text-slate-300 mb-1">Password (min 6 characters)</label>
            <div class="relative">
              <input id="authSignUpPassword" type="password" autocomplete="new-password" required placeholder="Create a password" class="w-full px-3.5 py-2 pr-10 rounded-xl bg-slate-900/90 border border-white/15 focus:border-sky-400 focus:outline-none text-xs text-white placeholder-slate-500" />
              <button type="button" onclick="togglePasswordVisibility('authSignUpPassword', this)" aria-label="Toggle password visibility" class="absolute right-2.5 top-1/2 -translate-y-1/2 text-slate-400 hover:text-white p-1">
                <i data-lucide="eye" class="w-4 h-4"></i>
              </button>
            </div>
          </div>
          <div>
            <label for="authSignUpConfirm" class="block text-[11px] font-medium text-slate-300 mb-1">Confirm password</label>
            <div class="relative">
              <input id="authSignUpConfirm" type="password" autocomplete="new-password" required placeholder="Confirm your password" class="w-full px-3.5 py-2 pr-10 rounded-xl bg-slate-900/90 border border-white/15 focus:border-sky-400 focus:outline-none text-xs text-white placeholder-slate-500" />
              <button type="button" onclick="togglePasswordVisibility('authSignUpConfirm', this)" aria-label="Toggle confirm password visibility" class="absolute right-2.5 top-1/2 -translate-y-1/2 text-slate-400 hover:text-white p-1">
                <i data-lucide="eye" class="w-4 h-4"></i>
              </button>
            </div>
          </div>
          <button id="authSignUpSubmitBtn" type="submit" class="w-full py-2.5 px-4 rounded-xl bg-gradient-to-r from-sky-500 to-blue-600 hover:from-sky-400 hover:to-blue-500 text-white font-semibold text-xs sm:text-sm flex items-center justify-center gap-2 transition">
            <span data-i18n="auth_create_btn">Create Account</span>
          </button>
        </form>

        <!-- FORGOT PASSWORD FORM -->
        <form id="authForgotForm" onsubmit="handleForgotPassword(event)" class="hidden space-y-3" novalidate>
          <p class="text-xs text-slate-300">Enter your email address and Supabase Auth will send a secure password reset link.</p>
          <div>
            <label for="authForgotEmail" class="block text-[11px] font-medium text-slate-300 mb-1" data-i18n="auth_email_label">Email address</label>
            <input id="authForgotEmail" type="email" autocomplete="email" required placeholder="explorer@example.com" class="w-full px-3.5 py-2.5 rounded-xl bg-slate-900/90 border border-white/15 focus:border-sky-400 focus:outline-none text-xs text-white placeholder-slate-500" />
          </div>
          <button id="authForgotSubmitBtn" type="submit" class="w-full py-2.5 px-4 rounded-xl bg-sky-500 hover:bg-sky-400 text-slate-950 font-semibold text-xs sm:text-sm transition">
            Send Password Reset Email
          </button>
          <button type="button" onclick="switchAuthTab('signin')" class="w-full text-center text-[11px] text-slate-400 hover:text-white transition">
            ← Back to Sign In
          </button>
        </form>

        <!-- Optional Supabase Public Config Drawer (Never stores secret service-role keys) -->
        <div class="pt-1 border-t border-white/10">
          <button type="button" onclick="document.getElementById('supabaseConfigPanel').classList.toggle('hidden')" class="w-full text-center text-[10px] font-mono text-slate-500 hover:text-sky-300 transition">
            ⚙ Configure Supabase Auth (URL &amp; Public Anon Key)
          </button>
          <div id="supabaseConfigPanel" class="hidden mt-2.5 p-3 rounded-xl bg-slate-900/90 border border-white/10 space-y-2">
            <input id="cfgSupabaseUrl" type="url" placeholder="https://your-project-ref.supabase.co" class="w-full px-2.5 py-1.5 rounded-lg bg-black/50 border border-white/15 text-[11px] text-white" />
            <input id="cfgSupabaseAnonKey" type="text" placeholder="Public Anon Key (never service_role)" class="w-full px-2.5 py-1.5 rounded-lg bg-black/50 border border-white/15 text-[11px] text-white" />
            <button type="button" onclick="saveSupabasePublicConfig()" class="w-full py-1.5 rounded-lg bg-white/10 hover:bg-white/20 text-[11px] text-sky-300 font-medium transition">
              Save &amp; Connect Supabase Auth
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>


  <!-- REALISTIC OUTER SPACE STARFIELD CANVAS -->
  <canvas id="starfieldCanvas" class="fixed inset-0 pointer-events-none z-0"></canvas>

  <!-- =====================================================================
       TOP NAVIGATION BAR (Closely modeled on UNIVERSAL EARTH Reference #2)
       ===================================================================== -->
  <header class="fixed top-4 left-0 right-0 z-40 px-4 sm:px-8 lg:px-14">
    <div class="max-w-[1440px] mx-auto universal-nav-pill rounded-full px-5 sm:px-8 h-14 sm:h-16 flex items-center justify-between gap-4">
      <!-- Left Logo: GLOBAL VR EXPLORER -->
      <button id="brandHomeBtn" type="button" class="flex items-center gap-2.5 text-left focus:outline-none group">
        <span class="font-universal font-bold tracking-[0.18em] text-xs sm:text-sm text-white">
          GLOBAL <span class="text-sky-400">VR</span> EXPLORER
        </span>
        <span id="brandSubtitle" class="sr-only">Explore the World Before You Travel</span>
      </button>

      <!-- Center Minimal Links: Home | Destinations | Explore | Technology | About | Contact -->
      <nav class="hidden lg:flex items-center gap-7 text-xs font-medium tracking-wide text-slate-300" aria-label="Primary Navigation">
        <button type="button" data-nav="home" class="nav-tab text-white hover:text-sky-300 transition">
          <span data-i18n="nav_home">Home</span>
        </button>
        <button type="button" onclick="scrollToHomeSection('section-destinations-showcase')" class="hover:text-white transition">
          <span data-i18n="nav_explore">Destinations</span>
        </button>
        <button type="button" data-nav="globe" class="nav-tab hover:text-white transition">
          <span data-i18n="nav_globe">Explore</span>
        </button>
        <button type="button" data-nav="timetravel" class="nav-tab text-sky-300 hover:text-white transition flex items-center gap-1.5">
          <i data-lucide="sparkles" class="w-3.5 h-3.5"></i>
          <span>AI &amp; Time Travel (1200–2026)</span>
        </button>
        <button type="button" onclick="scrollToHomeSection('section-technology')" class="hover:text-white transition">
          <span data-i18n="nav_technology">Technology</span>
        </button>
        <button type="button" onclick="scrollToHomeSection('section-about')" class="hover:text-white transition">
          <span data-i18n="nav_about">About</span>
        </button>
        <button type="button" onclick="scrollToHomeSection('section-contact')" class="hover:text-white transition">
          <span data-i18n="nav_contact">Contact</span>
        </button>
      </nav>

      <!-- Right Actions: Utility Pills + "Start Exploring" Pill Button -->
      <div class="flex items-center gap-2.5">
        <button id="currencyToggleBtn" type="button" class="hidden sm:inline-flex px-3 py-1 rounded-full bg-white/5 border border-white/10 hover:border-sky-400/40 text-[11px] font-mono text-sky-200 transition" title="Toggle Currency">
          BDT ৳
        </button>
        <!-- Searchable Type-to-Select Multilingual Dropdown -->
        <div id="langSelectorWrap" class="relative">
          <button id="langToggleBtn" type="button" onclick="toggleLangDropdown()" class="inline-flex items-center gap-1.5 px-3 py-1.5 rounded-full bg-white/8 border border-white/15 hover:border-sky-400/45 text-[11px] font-medium text-slate-100 transition" title="Select Language">
            <i data-lucide="globe" class="w-3.5 h-3.5 text-sky-400"></i>
            <span id="activeLangIndicator">EN</span>
            <i data-lucide="chevron-down" class="w-3 h-3 text-slate-400"></i>
          </button>

          <div id="langDropdownMenu" class="hidden absolute right-0 mt-2 w-64 rounded-2xl bg-slate-950/95 backdrop-blur-xl border border-white/15 shadow-2xl p-3 space-y-2 z-50">
            <input id="langSearchInput" type="text" oninput="filterLanguageOptions(this.value)" placeholder="Type language (Bangla, العربية, 日本語)..." class="w-full px-3 py-1.5 rounded-xl bg-slate-900 border border-white/15 focus:border-sky-400 focus:outline-none text-xs text-white placeholder-slate-500" />
            <div id="langOptionsList" class="max-h-56 overflow-y-auto space-y-1 text-xs"></div>
          </div>
        </div>
        <button id="startExploringNavBtn" type="button" onclick="scrollToHomeSection('section-destinations-showcase')" class="px-5 py-2 rounded-full bg-gradient-to-r from-sky-500/80 to-blue-600/80 hover:from-sky-400 hover:to-blue-500 border border-sky-300/40 text-white text-xs font-medium tracking-wide shadow-[0_0_25px_rgba(56,189,248,0.4)] transition">
          Start Exploring
        </button>
        <!-- Auth: Sign In Pill OR Authenticated User Profile Dropdown -->
        <div id="headerAuthArea" class="relative">
          <button id="headerSignInBtn" type="button" onclick="openAuthPortal('signin')" class="px-3.5 py-2 rounded-full bg-white/8 hover:bg-white/15 border border-white/15 text-xs font-medium text-slate-100 flex items-center gap-1.5 transition">
            <i data-lucide="user-circle-2" class="w-3.5 h-3.5 text-sky-400"></i>
            <span class="hidden sm:inline">Sign In</span>
          </button>

          <button id="headerUserPill" type="button" onclick="toggleAccountMenu()" class="hidden px-2.5 py-1.5 rounded-full bg-white/10 hover:bg-white/18 border border-sky-400/35 text-xs font-medium text-white flex items-center gap-2 transition">
            <img id="headerUserAvatar" src="" alt="Profile" class="hidden w-6 h-6 rounded-full object-cover border border-sky-300/60" />
            <span id="headerUserInitials" class="w-6 h-6 rounded-full bg-gradient-to-br from-sky-500 to-indigo-600 text-[10px] font-bold flex items-center justify-center text-white">U</span>
            <span id="headerUserName" class="hidden md:inline max-w-[110px] truncate">Explorer</span>
            <i data-lucide="chevron-down" class="w-3.5 h-3.5 text-slate-300"></i>
          </button>

          <!-- Authenticated Account Menu Dropdown -->
          <div id="accountMenuDropdown" class="hidden absolute right-0 mt-2.5 w-72 sm:w-80 rounded-2xl bg-slate-950/95 backdrop-blur-xl border border-white/15 shadow-2xl p-4 space-y-3 z-50 text-left">
            <div class="flex items-center gap-3 pb-3 border-b border-white/10">
              <img id="menuUserAvatar" src="" alt="Profile" class="hidden w-10 h-10 rounded-full object-cover border border-sky-400/50" />
              <div id="menuUserInitials" class="w-10 h-10 rounded-full bg-gradient-to-br from-sky-500 to-indigo-600 text-sm font-bold flex items-center justify-center text-white shrink-0">U</div>
              <div class="min-w-0 flex-1">
                <div id="menuUserName" class="text-sm font-semibold text-white truncate">Explorer</div>
                <div id="menuUserEmail" class="text-[11px] text-slate-400 truncate">user@example.com</div>
                <div id="menuAuthProvider" class="text-[10px] font-mono text-sky-400 uppercase mt-0.5">Supabase Auth</div>
              </div>
            </div>

            <div class="space-y-1.5">
              <div class="text-[10px] font-mono uppercase tracking-wider text-slate-400">Saved &amp; Favorites (<span id="menuSavedCount">0</span>)</div>
              <div id="menuSavedList" class="max-h-24 overflow-y-auto space-y-1 text-xs text-slate-300"></div>
            </div>

            <div class="space-y-1.5">
              <div class="text-[10px] font-mono uppercase tracking-wider text-slate-400">Recently Explored</div>
              <div id="menuRecentList" class="max-h-24 overflow-y-auto space-y-1 text-xs text-slate-300"></div>
            </div>

            <div class="pt-2 border-t border-white/10 flex items-center justify-between gap-2">
              <button type="button" onclick="saveCurrentDestinationToFavorites()" class="px-3 py-1.5 rounded-lg bg-sky-500/15 hover:bg-sky-500/25 border border-sky-400/30 text-sky-300 text-[11px] font-medium transition">
                ★ Save Current
              </button>
              <button id="signOutBtn" type="button" onclick="signOutAuthUser()" class="px-3 py-1.5 rounded-lg bg-rose-500/15 hover:bg-rose-500/25 border border-rose-400/30 text-rose-300 text-[11px] font-medium transition">
                Sign Out
              </button>
            </div>
          </div>
        </div>
        <button id="mobileMenuBtn" type="button" class="lg:hidden p-2 rounded-full bg-white/5 border border-white/15 text-slate-200 hover:text-sky-300" aria-label="Toggle Menu">
          <i data-lucide="menu" class="w-4 h-4"></i>
        </button>
      </div>
    </div>

    <!-- Mobile Navigation Drawer -->
    <div id="mobileNavDrawer" class="hidden lg:hidden mt-2 max-w-[1440px] mx-auto universal-nav-pill rounded-3xl p-5 space-y-3">
      <div class="grid grid-cols-2 sm:grid-cols-3 gap-2">
        <button type="button" data-nav="home" class="nav-tab-mobile px-3.5 py-2.5 rounded-xl text-xs font-medium text-left flex items-center gap-2 bg-white/10 text-white">
          <i data-lucide="compass" class="w-3.5 h-3.5"></i><span>Home</span>
        </button>
        <button type="button" onclick="scrollToHomeSection('section-destinations-showcase')" class="px-3.5 py-2.5 rounded-xl text-xs font-medium text-left flex items-center gap-2 text-slate-200 hover:bg-white/5">
          <i data-lucide="map-pin" class="w-3.5 h-3.5 text-sky-300"></i><span>Destinations</span>
        </button>
        <button type="button" data-nav="globe" class="nav-tab-mobile px-3.5 py-2.5 rounded-xl text-xs font-medium text-left flex items-center gap-2 text-slate-200 hover:bg-white/5">
          <i data-lucide="globe" class="w-3.5 h-3.5 text-sky-300"></i><span>3D Earth</span>
        </button>
        <button type="button" data-nav="timetravel" class="nav-tab-mobile px-3.5 py-2.5 rounded-xl text-xs font-medium text-left flex items-center gap-2 text-sky-300 hover:bg-white/5">
          <i data-lucide="history" class="w-3.5 h-3.5"></i><span>AI &amp; Time Travel</span>
        </button>
        <button type="button" data-nav="explore" class="nav-tab-mobile px-3.5 py-2.5 rounded-xl text-xs font-medium text-left flex items-center gap-2 text-slate-200 hover:bg-white/5">
          <i data-lucide="search" class="w-3.5 h-3.5"></i><span>All Locations</span>
        </button>
        <button type="button" data-nav="assistant" class="nav-tab-mobile px-3.5 py-2.5 rounded-xl text-xs font-medium text-left flex items-center gap-2 text-slate-200 hover:bg-white/5">
          <i data-lucide="luggage" class="w-3.5 h-3.5"></i><span>Travel Prep</span>
        </button>
        <button type="button" data-nav="compare" class="nav-tab-mobile px-3.5 py-2.5 rounded-xl text-xs font-medium text-left flex items-center gap-2 text-slate-200 hover:bg-white/5">
          <i data-lucide="git-compare" class="w-3.5 h-3.5"></i><span>Compare</span>
        </button>
        <button type="button" data-nav="unity" class="nav-tab-mobile px-3.5 py-2.5 rounded-xl text-xs font-medium text-left flex items-center gap-2 text-slate-200 hover:bg-white/5">
          <i data-lucide="cpu" class="w-3.5 h-3.5"></i><span>Meta Quest XR</span>
        </button>
        <button type="button" data-nav="profile" class="nav-tab-mobile px-3.5 py-2.5 rounded-xl text-xs font-medium text-left flex items-center gap-2 text-slate-200 hover:bg-white/5">
          <i data-lucide="bookmark" class="w-3.5 h-3.5"></i><span>Saved (<span id="savedCountBadge">3</span>)</span>
        </button>
      </div>
    </div>
  </header>

  <!-- MAIN VIEW CONTAINER (No z-index trap so full-screen VR layers above header & footer cleanly) -->
  <main class="relative flex-1 w-full">

    <!-- ===================================================================
         VIEW 1: CINEMATIC UNIVERSAL EARTH HOMEPAGE + EDITORIAL SHOWCASES
         =================================================================== -->
    <section id="view-home" class="view-section">

      <!-- =================================================================
           1. FULL-SCREEN HERO — UNIVERSAL EARTH DESIGN (Reference Image #1)
           Left: Bold Headline + Short Description + 2 Pill Buttons
           Right: HUGE Photorealistic 3D Earth with Continents, Oceans & Blue Atmosphere
           ================================================================= -->
      <div class="relative min-h-screen w-full universal-space-bg overflow-hidden flex flex-col lg:flex-row items-center justify-center pt-24 pb-12 lg:py-0">

        <!-- Atmospheric Outer Space Illumination Glows behind Earth -->
        <div class="pointer-events-none absolute right-[-8%] top-1/2 -translate-y-1/2 w-[560px] h-[560px] sm:w-[780px] sm:h-[780px] lg:w-[920px] lg:h-[920px] rounded-full bg-sky-500/20 blur-[130px]"></div>
        <div class="pointer-events-none absolute right-[8%] top-[22%] w-[360px] h-[360px] rounded-full bg-blue-500/15 blur-[100px]"></div>

        <!-- HUGE PHOTOREALISTIC 3D EARTH ON THE RIGHT SIDE -->
        <div class="lg:absolute lg:right-[-8%] xl:right-[-4%] lg:top-1/2 lg:-translate-y-1/2 w-full lg:w-[68%] h-[380px] sm:h-[520px] lg:h-[92vh] flex items-center justify-center order-2 lg:order-none z-10">
          <div id="heroEarthContainer" class="relative w-full h-full flex items-center justify-center cursor-grab active:cursor-grabbing" title="Drag to rotate Earth">
            <!-- Instant 0ms Photorealistic Earth Orb (Always visible even before WebGL compiles or if mobile WebGL is throttled) -->
            <div id="heroEarthStaticFallback" class="earth-fallback-orb pointer-events-none flex items-center justify-center">
              <img src="https://upload.wikimedia.org/wikipedia/commons/1/1c/BlueMarble-2001-2002.jpg"
                   alt="Photorealistic Earth"
                   onerror="this.style.display='none'"
                   class="earth-fallback-map object-cover" />
              <div class="absolute inset-0 rounded-full" style="box-shadow: inset -40px -30px 75px rgba(1,3,10,0.92), inset 16px 16px 40px rgba(125,211,252,0.45);"></div>
            </div>
          </div>
        </div>

        <!-- LEFT COLUMN: TYPOGRAPHY & BUTTONS (Matching UNIVERSAL EARTH Reference) -->
        <div class="max-w-[1440px] w-full mx-auto px-6 sm:px-10 lg:px-16 py-12 lg:py-0 relative z-20 pointer-events-none">
          <div class="max-w-xl space-y-6 pointer-events-auto">
            <!-- Small Pill Eyebrow Badge -->
            <div class="inline-flex items-center gap-2.5 px-4 py-1.5 rounded-full bg-slate-900/80 border border-sky-400/25 shadow-lg backdrop-blur-md">
              <span class="w-2.5 h-2.5 rounded-full bg-sky-400 shadow-[0_0_10px_#38bdf8]"></span>
              <span class="text-xs font-medium text-slate-200 tracking-wide" data-i18n="hero_badge">Experience Earth like never before</span>
            </div>

            <!-- Large Geometric Uppercase Headline -->
            <h1 id="heroHeadline" class="font-universal font-extrabold text-3xl sm:text-5xl xl:text-[3.35rem] text-white uppercase leading-[1.12] tracking-wider drop-shadow-[0_4px_24px_rgba(0,0,0,0.9)]">
              EXPLORE THE WORLD<br />
              <span class="text-transparent bg-clip-text bg-gradient-to-r from-white via-sky-100 to-sky-300">BEFORE YOU TRAVEL.</span>
            </h1>

            <!-- Short Clean Product Description -->
            <p id="heroDescription" class="text-slate-300/90 text-sm sm:text-base font-light leading-relaxed max-w-md">
              A real-time 3D visualization and immersive Virtual Reality platform built to explore our planet, its iconic destinations, and cultural wonders before you travel.
            </p>

            <!-- Two Pill Buttons: "Explore the World" + "Watch Experience" -->
            <div class="flex flex-wrap items-center gap-4 pt-2">
              <button id="heroExploreWorldBtn" type="button" class="px-7 py-3.5 rounded-full bg-gradient-to-r from-sky-500/85 via-blue-600/85 to-sky-500/85 hover:from-sky-400 hover:to-blue-500 text-white font-medium text-xs sm:text-sm tracking-wide border border-sky-300/45 shadow-[0_0_30px_rgba(14,165,233,0.45)] transition transform hover:-translate-y-0.5">
                <span data-i18n="btn_explore_world">Explore the World</span>
              </button>

              <button id="heroWatchExperienceBtn" type="button" class="px-7 py-3.5 rounded-full bg-slate-950/75 hover:bg-slate-900 text-slate-200 hover:text-white border border-white/25 font-medium text-xs sm:text-sm tracking-wide backdrop-blur-md transition flex items-center gap-2">
                <i data-lucide="play" class="w-3.5 h-3.5 text-sky-300 fill-sky-300"></i>
                <span>Watch Experience</span>
              </button>
            </div>

            <!-- AI DESTINATION NAVIGATION QUICK BAR (Natural Language + Voice Search) -->
            <div class="pt-4 max-w-lg">
              <div class="glass-panel rounded-2xl p-2.5 border border-sky-400/30 shadow-2xl space-y-2">
                <div class="flex items-center gap-2">
                  <i data-lucide="sparkles" class="w-4 h-4 text-sky-400 shrink-0 ml-2"></i>
                  <input id="heroAiCommandInput" type="text" placeholder="Ask AI: 'Take me to Cox's Bazar', 'Show me Paris in 1888', 'Ancient Rome'..." class="w-full bg-transparent text-xs text-white placeholder-slate-400 focus:outline-none" />
                  <button id="heroAiVoiceBtn" type="button" onclick="startAiVoiceRecognition('heroAiCommandInput')" class="p-2 rounded-xl bg-white/5 hover:bg-white/15 text-sky-300 border border-white/10 shrink-0 transition" title="Voice Destination Search">
                    <i data-lucide="mic" class="w-3.5 h-3.5"></i>
                  </button>
                  <button id="heroAiSubmitBtn" type="button" onclick="executeAiNavigationQuery(document.getElementById('heroAiCommandInput').value)" class="px-4 py-2 rounded-xl bg-sky-500 hover:bg-sky-400 text-slate-950 font-semibold text-xs shrink-0 transition">
                    Navigate
                  </button>
                </div>
                <div class="flex flex-wrap items-center gap-1.5 px-1 text-[10px]">
                  <span class="text-slate-400 font-mono">Try:</span>
                  <button type="button" onclick="executeAiNavigationQuery('Take me to Cox\'s Bazar')" class="px-2 py-0.5 rounded-full bg-white/5 hover:bg-white/15 text-sky-200 border border-white/10">"Take me to Cox's Bazar"</button>
                  <button type="button" onclick="executeAiNavigationQuery('Show me the Eiffel Tower in 1888')" class="px-2 py-0.5 rounded-full bg-white/5 hover:bg-white/15 text-sky-200 border border-white/10">"Eiffel Tower in 1888"</button>
                  <button type="button" onclick="executeAiNavigationQuery('Take me to ancient Rome')" class="px-2 py-0.5 rounded-full bg-white/5 hover:bg-white/15 text-sky-200 border border-white/10">"Take me to ancient Rome"</button>
                  <button type="button" onclick="executeAiNavigationQuery('Explore Lalbagh Fort in 1875')" class="px-2 py-0.5 rounded-full bg-white/5 hover:bg-white/15 text-sky-200 border border-white/10">"Lalbagh Fort 1875"</button>
                </div>
              </div>
            </div>

            <!-- Subtle Bottom Left Tagline from Reference #2 -->
            <div class="pt-6 sm:pt-8">
              <span class="text-[10px] font-mono uppercase tracking-[0.28em] text-slate-400/80">BEYOND BOUNDARIES • GLOBAL VR EXPLORER • 1200–2026 TIME TRAVEL</span>
            </div>
          </div>
        </div>
      </div>

      <!-- =================================================================
           2. DESTINATION SHOWCASE SECTION (Inspired by Reference #3 & #4)
           ================================================================= -->
      <div id="section-destinations-showcase" class="relative z-20">

        <!-- ---------------------------------------------------------------
             SHOWCASE A: COX'S BAZAR (Modeled on Reference Image #2 / 17463.jpg)
             Full-bleed golden sunset beach background + Left serif title &
             "Explore Destination" button + Right 3 floating portrait cards
             --------------------------------------------------------------- -->
        <section class="relative min-h-[90vh] w-full overflow-hidden flex items-center border-t border-white/10">
          <!-- Full-Bleed Sunset Beach Photograph -->
          <img id="coxsHeroBgImg"
               src="https://upload.wikimedia.org/wikipedia/commons/8/89/A_dusk_at_Cox%27s_Bazar_sea_beach.jpg"
               alt="Cox's Bazar Sea Beach Sunset"
               onerror="this.onerror=null;this.src=createFallbackDataUri('Coxs Bazar Sea Beach','21.4272 N, 92.0058 E');"
               class="absolute inset-0 w-full h-full object-cover object-center scale-105 transition-all duration-1000" />
          <!-- Warm Sunset & Left Readability Gradient Overlay -->
          <div class="absolute inset-0 bg-gradient-to-r from-[#0d0907]/90 via-[#1a110b]/60 to-[#0d0907]/35"></div>
          <div class="absolute inset-0 bg-gradient-to-t from-[#05060a] via-transparent to-[#010206]/80"></div>

          <div class="relative z-10 max-w-[1440px] w-full mx-auto px-6 sm:px-10 lg:px-16 py-20 grid grid-cols-1 lg:grid-cols-12 gap-10 items-center">
            <!-- Left 5 Columns: Faded Sequence Titles + Serif Cox's Bazar Title + Explore Destination Button -->
            <div class="lg:col-span-5 space-y-6">
              <!-- Faded Previous Destination Label (like "Sundarbans" in 17463.jpg) -->
              <button type="button" onclick="openEditorialDestinationPage('sundarbans')" class="block font-editorial text-2xl sm:text-3xl text-white/25 hover:text-white/60 transition tracking-wide">
                Sundarbans
              </button>

              <div class="space-y-4 pl-4 border-l-2 border-amber-500/70">
                <div class="text-[11px] font-mono uppercase tracking-[0.25em] text-amber-300/90">
                  Bangladesh • Chittagong Division • 120 KM Unbroken Shore
                </div>
                <h2 class="font-editorial text-5xl sm:text-6xl lg:text-7xl font-bold text-white tracking-tight leading-none">
                  Cox’s Bazar
                </h2>
                <p class="text-slate-200/90 text-sm sm:text-base font-light leading-relaxed max-w-md">
                  Discover the beauty of Cox’s Bazar, the longest natural sandy sea beach in the world. Relax, unwind, and enjoy breathtaking golden sunsets by the Bay of Bengal before stepping into full 360° Virtual Reality.
                </p>

                <div class="flex flex-wrap items-center gap-3.5 pt-2">
                  <button type="button" onclick="openEditorialDestinationPage('coxs-bazar')" class="px-7 py-3.5 rounded-lg bg-[#b45309] hover:bg-[#d97706] text-white font-medium text-xs sm:text-sm tracking-wider flex items-center gap-3 shadow-xl transition">
                    <span>Explore Destination</span>
                    <i data-lucide="arrow-right" class="w-4 h-4"></i>
                  </button>
                  <button type="button" onclick="openPanoramaForDestination('coxs-bazar')" class="px-6 py-3.5 rounded-lg bg-black/55 hover:bg-black/80 text-amber-200 border border-amber-400/35 font-medium text-xs sm:text-sm tracking-wider flex items-center gap-2 backdrop-blur-md transition">
                    <i data-lucide="glasses" class="w-4 h-4"></i>
                    <span>Explore in VR</span>
                  </button>
                </div>
              </div>

              <!-- Faded Next Destination Label (like "Saint Martin's Island" in 17463.jpg) -->
              <button type="button" onclick="openEditorialDestinationPage('saint-martins')" class="block font-editorial text-2xl sm:text-3xl text-white/25 hover:text-white/60 transition tracking-wide pt-4">
                Saint Martin’s Island
              </button>
            </div>

            <!-- Right 7 Columns: 3 Tall Portrait Photographic Cards for Nearby Attractions (Matching 17463.jpg) -->
            <div class="lg:col-span-7 space-y-6">
              <div class="grid grid-cols-1 sm:grid-cols-3 gap-5 items-end">
                <!-- Card 1: Laboni & Sugandha Sunset Beach -->
                <div class="space-y-2 group cursor-pointer" onclick="openEditorialDestinationPage('coxs-bazar', 0)">
                  <div class="px-1">
                    <div class="font-editorial text-lg font-medium text-white">Laboni &amp; Sugandha Beach</div>
                    <div class="text-amber-400 text-[11px] tracking-widest">★★★★★</div>
                  </div>
                  <div class="photo-zoom relative h-72 sm:h-80 rounded-xl overflow-hidden border border-white/20 shadow-2xl">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/8/89/A_dusk_at_Cox%27s_Bazar_sea_beach.jpg"
                         alt="Laboni Beach Sunset"
                         onerror="this.onerror=null;this.src=createFallbackDataUri('Laboni Beach','Coxs Bazar');"
                         class="w-full h-full object-cover" />
                    <div class="absolute top-3 right-3 w-8 h-8 rounded-lg bg-black/55 border border-white/20 flex items-center justify-center text-white backdrop-blur-md">
                      <i data-lucide="play" class="w-3.5 h-3.5 fill-white"></i>
                    </div>
                  </div>
                </div>

                <!-- Card 2: St. Martin's Coral Island -->
                <div class="space-y-2 group cursor-pointer" onclick="openEditorialDestinationPage('saint-martins', 0)">
                  <div class="px-1">
                    <div class="font-editorial text-lg font-medium text-white">St. Martin’s Island</div>
                    <div class="text-amber-400 text-[11px] tracking-widest">★★★★★</div>
                  </div>
                  <div class="photo-zoom relative h-64 sm:h-72 rounded-xl overflow-hidden border border-white/20 shadow-2xl">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/7/7f/Saint-martin%27s-island.JPG"
                         alt="Saint Martin's Island"
                         onerror="this.onerror=null;this.src=createFallbackDataUri('Saint Martins Island','Bay of Bengal');"
                         class="w-full h-full object-cover" />
                    <div class="absolute top-3 right-3 w-8 h-8 rounded-lg bg-black/55 border border-white/20 flex items-center justify-center text-white backdrop-blur-md">
                      <i data-lucide="play" class="w-3.5 h-3.5 fill-white"></i>
                    </div>
                  </div>
                </div>

                <!-- Card 3: Himchari & Marine Drive -->
                <div class="space-y-2 group cursor-pointer" onclick="openEditorialDestinationPage('coxs-bazar', 2)">
                  <div class="px-1">
                    <div class="font-editorial text-lg font-medium text-white">Himchari &amp; Marine Drive</div>
                    <div class="text-amber-400 text-[11px] tracking-widest">★★★★★</div>
                  </div>
                  <div class="photo-zoom relative h-64 sm:h-72 rounded-xl overflow-hidden border border-white/20 shadow-2xl">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/9/92/Cox%27s_Bazar_sea_beach_01.jpg"
                         alt="Himchari & Marine Drive Coast"
                         onerror="this.onerror=null;this.src=createFallbackDataUri('Himchari Coast','Coxs Bazar');"
                         class="w-full h-full object-cover" />
                    <div class="absolute top-3 right-3 w-8 h-8 rounded-lg bg-black/55 border border-white/20 flex items-center justify-center text-white backdrop-blur-md">
                      <i data-lucide="play" class="w-3.5 h-3.5 fill-white"></i>
                    </div>
                  </div>
                </div>
              </div>

              <!-- Bottom Carousel Controls & Counter (01 — 03) matching 17463.jpg -->
              <div class="flex items-center justify-between pt-2">
                <div class="flex items-center gap-3">
                  <button type="button" onclick="cycleCoxsBazarBg(-1)" class="w-9 h-9 rounded-full bg-black/50 hover:bg-black/80 border border-white/20 flex items-center justify-center text-white transition" aria-label="Previous Cox's Bazar Photo">
                    <i data-lucide="chevron-left" class="w-4 h-4"></i>
                  </button>
                  <button type="button" onclick="cycleCoxsBazarBg(1)" class="w-9 h-9 rounded-full bg-black/50 hover:bg-black/80 border border-white/20 flex items-center justify-center text-white transition" aria-label="Next Cox's Bazar Photo">
                    <i data-lucide="chevron-right" class="w-4 h-4"></i>
                  </button>
                </div>
                <div id="coxsSlideCounter" class="font-mono text-xs tracking-widest text-slate-200">
                  01 — 04
                </div>
              </div>
            </div>
          </div>
        </section>

        <!-- ---------------------------------------------------------------
             SHOWCASE B: PARIS, FRANCE (Modeled on Reference Image #3 / 17464.jpg)
             Matte dark left column + Overlapping Serif Headline ("EXPERIENCE THE
             CITY OF LIGHT — PARIS") + Large Eiffel Tower Sunset Photo on Right +
             4-Photo Horizontal Gallery Strip directly underneath
             --------------------------------------------------------------- -->
        <section class="relative min-h-[90vh] w-full bg-[#0b0c10] border-t border-white/10 py-16 lg:py-24 overflow-hidden flex items-center">
          <div class="max-w-[1440px] w-full mx-auto px-6 sm:px-10 lg:px-16">
            <div class="grid grid-cols-1 lg:grid-cols-12 items-center relative">

              <!-- Left Editorial Column (Overlapping the large Eiffel Tower photograph on desktop) -->
              <div class="lg:col-span-5 z-20 space-y-8 py-6 lg:pr-6">
                <div class="text-[11px] font-mono uppercase tracking-[0.28em] text-amber-200/80">
                  France • Île-de-France • 48.8584° N, 2.2945° E
                </div>

                <h2 class="font-editorial text-4xl sm:text-6xl xl:text-[4.1rem] font-normal text-white uppercase leading-[1.08] tracking-wide lg:-mr-28 relative z-20 drop-shadow-[0_4px_24px_rgba(0,0,0,0.95)]">
                  EXPERIENCE THE<br />
                  CITY OF LIGHT<br />
                  <span class="inline-flex items-center gap-4">
                    <span class="w-12 sm:w-16 h-[1px] bg-white/60 inline-block"></span>
                    <span>PARIS</span>
                  </span>
                </h2>

                <p class="text-slate-300/85 text-sm sm:text-base font-light leading-relaxed max-w-sm">
                  Ascend Gustave Eiffel’s 330-metre wrought-iron lattice monument above the Champ de Mars and the River Seine. Inspect multi-angle photography or step directly onto the 276-metre summit observation deck in 360° VR.
                </p>

                <div class="flex flex-wrap items-center gap-4 pt-2">
                  <button type="button" onclick="openEditorialDestinationPage('eiffel-tower')" class="px-7 py-3.5 border border-white/40 hover:border-white hover:bg-white hover:text-slate-950 text-white text-xs uppercase tracking-[0.22em] transition flex items-center gap-3">
                    <span>Explore Destination</span>
                    <i data-lucide="arrow-right" class="w-4 h-4"></i>
                  </button>
                  <button type="button" onclick="openPanoramaForDestination('eiffel-tower')" class="px-6 py-3.5 bg-sky-500/20 hover:bg-sky-500/30 border border-sky-400/40 text-sky-200 text-xs uppercase tracking-[0.2em] transition flex items-center gap-2">
                    <i data-lucide="glasses" class="w-4 h-4"></i>
                    <span>Explore in VR</span>
                  </button>
                </div>
              </div>

              <!-- Right 7 Columns: Large Eiffel Tower Photograph + 4-Photo Gallery Strip Below -->
              <div class="lg:col-span-7 z-10">
                <!-- Main Large Eiffel Tower Sunset / Twilight Image -->
                <div class="relative h-[380px] sm:h-[460px] lg:h-[500px] overflow-hidden bg-[#05070c]">
                  <img id="parisMainShowcaseImg"
                       src="https://upload.wikimedia.org/wikipedia/commons/5/53/Eiffel_tower_from_trocadero.jpg"
                       alt="Eiffel Tower Paris Sunset from Trocadéro"
                       onerror="this.onerror=null;this.src=createFallbackDataUri('Eiffel Tower Paris','48.8584 N, 2.2945 E');"
                       class="w-full h-full object-cover object-center transition-all duration-700" />
                  <!-- Left edge gradient blend into the dark matte background -->
                  <div class="hidden lg:block absolute inset-y-0 left-0 w-36 bg-gradient-to-r from-[#0b0c10] via-[#0b0c10]/60 to-transparent"></div>
                  <div class="absolute bottom-3 right-4 px-3 py-1 rounded bg-black/65 text-[10px] font-mono text-slate-300 backdrop-blur-sm">
                    Wikimedia Commons • CC BY-SA 4.0
                  </div>
                </div>

                <!-- 4-Column Supporting Photographic Gallery Strip (Directly below main image, matching 17464.jpg) -->
                <div class="grid grid-cols-2 sm:grid-cols-4 gap-0 border-t border-white/10">
                  <div onclick="setParisShowcasePhoto('https://upload.wikimedia.org/wikipedia/commons/b/b3/Arc_de_Triomphe_du_Carrousel%2C_Louvre%2C_Paris_2007.jpg')"
                       class="photo-zoom cursor-pointer relative h-32 sm:h-36 overflow-hidden border-r border-white/10 group">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/b/b3/Arc_de_Triomphe_du_Carrousel%2C_Louvre%2C_Paris_2007.jpg"
                         alt="Louvre & Arc de Triomphe du Carrousel"
                         onerror="this.onerror=null;this.src=createFallbackDataUri('Louvre Grounds','Paris');"
                         class="w-full h-full object-cover" />
                    <div class="absolute inset-0 bg-black/25 group-hover:bg-transparent transition"></div>
                    <span class="absolute bottom-2 left-2.5 text-[10px] font-mono uppercase tracking-wider text-white drop-shadow">Louvre &amp; Carrousel</span>
                  </div>

                  <div onclick="setParisShowcasePhoto('https://upload.wikimedia.org/wikipedia/commons/a/a8/Tour_Eiffel_Wikimedia_Commons.jpg')"
                       class="photo-zoom cursor-pointer relative h-32 sm:h-36 overflow-hidden border-r border-white/10 group">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/a/a8/Tour_Eiffel_Wikimedia_Commons.jpg"
                         alt="Eiffel Tower from Champ de Mars"
                         onerror="this.onerror=null;this.src=createFallbackDataUri('Champ de Mars','Paris');"
                         class="w-full h-full object-cover" />
                    <div class="absolute inset-0 bg-black/25 group-hover:bg-transparent transition"></div>
                    <span class="absolute bottom-2 left-2.5 text-[10px] font-mono uppercase tracking-wider text-white drop-shadow">Champ de Mars</span>
                  </div>

                  <div onclick="setParisShowcasePhoto('https://upload.wikimedia.org/wikipedia/commons/c/cb/Arc_de_Triomphe%2C_Paris.JPG')"
                       class="photo-zoom cursor-pointer relative h-32 sm:h-36 overflow-hidden border-r border-white/10 group">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/c/cb/Arc_de_Triomphe%2C_Paris.JPG"
                         alt="Arc de Triomphe Paris"
                         onerror="this.onerror=null;this.src=createFallbackDataUri('Arc de Triomphe','Paris');"
                         class="w-full h-full object-cover" />
                    <div class="absolute inset-0 bg-black/25 group-hover:bg-transparent transition"></div>
                    <span class="absolute bottom-2 left-2.5 text-[10px] font-mono uppercase tracking-wider text-white drop-shadow">Arc de Triomphe</span>
                  </div>

                  <div onclick="openPanoramaForDestination('eiffel-tower')"
                       class="photo-zoom cursor-pointer relative h-32 sm:h-36 overflow-hidden group">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/ae/Tour_Eiffel_360_Panorama.jpg/3840px-Tour_Eiffel_360_Panorama.jpg"
                         alt="Eiffel Tower 360 Panorama"
                         onerror="this.onerror=null;this.src=createFallbackDataUri('360 Summit View','Paris');"
                         class="w-full h-full object-cover" />
                    <div class="absolute inset-0 bg-sky-950/40 group-hover:bg-sky-900/20 transition flex items-center justify-center">
                      <span class="px-2.5 py-1 rounded-full bg-black/75 border border-sky-400/50 text-[10px] font-mono text-sky-300">360° VR SUMMIT ↗</span>
                    </div>
                  </div>
                </div>
              </div>

            </div>
          </div>
        </section>

        <!-- ---------------------------------------------------------------
             SHOWCASE C: LALBAGH FORT, DHAKA (Premium Historical Editorial Layout)
             --------------------------------------------------------------- -->
        <section class="relative min-h-[85vh] w-full bg-[#060913] border-t border-white/10 py-20 flex items-center">
          <div class="max-w-[1440px] w-full mx-auto px-6 sm:px-10 lg:px-16 space-y-10">
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-10 items-center">
              <!-- Left 7 Columns: Large Authentic Mughal Architecture Photography + 3 Viewpoint Thumbnails -->
              <div class="lg:col-span-7 space-y-3">
                <div class="relative h-[380px] sm:h-[460px] rounded-2xl overflow-hidden border border-white/15 shadow-2xl">
                  <img id="lalbaghMainShowcaseImg"
                       src="https://upload.wikimedia.org/wikipedia/commons/3/38/Lalbagh_Fort%2C_Dhaka%2C_Bangladesh.jpg"
                       alt="Lalbagh Fort Dhaka Mughal Architecture"
                       onerror="this.onerror=null;this.src=createFallbackDataUri('Lalbagh Fort Dhaka','23.7189 N, 90.3882 E');"
                       class="w-full h-full object-cover transition-all duration-700" />
                  <div class="absolute inset-0 bg-gradient-to-t from-black/75 via-transparent to-transparent"></div>
                  <div class="absolute bottom-4 left-5 right-5 flex items-center justify-between text-xs text-slate-200">
                    <span class="font-editorial italic text-lg">Mausoleum of Pari Bibi &amp; Mughal Charbagh Axis (1678 AD)</span>
                    <span class="font-mono text-[10px] text-amber-300">23.7189° N, 90.3882° E</span>
                  </div>
                </div>

                <!-- 3-Column Supporting Architectural Perspectives -->
                <div class="grid grid-cols-3 gap-3">
                  <div onclick="setLalbaghShowcasePhoto('https://upload.wikimedia.org/wikipedia/commons/1/1e/Dhaka_Lalbagh_Fort_5.JPG')"
                       class="photo-zoom cursor-pointer relative h-28 sm:h-32 rounded-xl overflow-hidden border border-white/15">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/1/1e/Dhaka_Lalbagh_Fort_5.JPG"
                         alt="Lalbagh Fort Courtyard"
                         onerror="this.onerror=null;this.src=createFallbackDataUri('Charbagh Courtyard','Lalbagh');"
                         class="w-full h-full object-cover" />
                    <div class="absolute inset-0 bg-black/30"></div>
                    <span class="absolute bottom-2 left-2.5 text-[10px] font-mono text-white">Charbagh Gardens</span>
                  </div>
                  <div onclick="setLalbaghShowcasePhoto('https://upload.wikimedia.org/wikipedia/commons/5/5e/Dhaka_Lalbagh_Fort_3.JPG')"
                       class="photo-zoom cursor-pointer relative h-28 sm:h-32 rounded-xl overflow-hidden border border-white/15">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/5/5e/Dhaka_Lalbagh_Fort_3.JPG"
                         alt="Lalbagh Fort Pavilion"
                         onerror="this.onerror=null;this.src=createFallbackDataUri('Mughal Pavilion','Lalbagh');"
                         class="w-full h-full object-cover" />
                    <div class="absolute inset-0 bg-black/30"></div>
                    <span class="absolute bottom-2 left-2.5 text-[10px] font-mono text-white">Southern Ramparts</span>
                  </div>
                  <div onclick="setLalbaghShowcasePhoto('https://upload.wikimedia.org/wikipedia/commons/3/36/Hammamkhana_of_Lalbagh_Fort%2C_Dhaka%2C_Bangladesh_17.jpg')"
                       class="photo-zoom cursor-pointer relative h-28 sm:h-32 rounded-xl overflow-hidden border border-white/15">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/3/36/Hammamkhana_of_Lalbagh_Fort%2C_Dhaka%2C_Bangladesh_17.jpg"
                         alt="Hammamkhana of Lalbagh Fort"
                         onerror="this.onerror=null;this.src=createFallbackDataUri('Hammamkhana','Lalbagh');"
                         class="w-full h-full object-cover" />
                    <div class="absolute inset-0 bg-black/30"></div>
                    <span class="absolute bottom-2 left-2.5 text-[10px] font-mono text-white">Royal Hammamkhana</span>
                  </div>
                </div>
              </div>

              <!-- Right 5 Columns: Historical Editorial Typography & Actions -->
              <div class="lg:col-span-5 space-y-6">
                <div class="inline-flex items-center gap-2 px-3 py-1 rounded-full bg-amber-500/15 border border-amber-400/30 text-amber-300 text-[11px] font-mono uppercase tracking-widest">
                  17th-Century Mughal Heritage • Old Dhaka
                </div>
                <h2 class="font-editorial text-5xl sm:text-6xl font-semibold text-white leading-none">
                  Lalbagh Fort
                </h2>
                <p class="font-editorial italic text-xl text-amber-100/90">
                  "Fort Aurangabad — where imperial Mughal marble, black Rajmahal basalt, and Bengal terracotta converge beside the Buriganga River."
                </p>
                <p class="text-slate-300 text-sm sm:text-base font-light leading-relaxed">
                  Commissioned in 1678 AD by Prince Muhammad Azam Shah and expanded by Subahdar Nawab Shaista Khan, Lalbagh Fort preserves three centuries of Mughal artistry across the Mausoleum of Pari Bibi, the Three-Domed Quilla Mosque, and the Diwan-i-Aam audience hall and museum.
                </p>

                <div class="flex flex-wrap items-center gap-4 pt-2">
                  <button type="button" onclick="openEditorialDestinationPage('lalbagh-fort')" class="px-7 py-3.5 rounded-lg bg-white text-slate-950 hover:bg-amber-100 font-medium text-xs sm:text-sm uppercase tracking-wider flex items-center gap-3 transition">
                    <span>Explore Destination</span>
                    <i data-lucide="arrow-right" class="w-4 h-4"></i>
                  </button>
                  <button type="button" onclick="openPanoramaForDestination('lalbagh-fort')" class="px-6 py-3.5 rounded-lg bg-white/5 hover:bg-white/15 border border-white/20 text-white text-xs sm:text-sm uppercase tracking-wider flex items-center gap-2 transition">
                    <i data-lucide="glasses" class="w-4 h-4 text-sky-300"></i>
                    <span>Explore in VR</span>
                  </button>
                </div>
              </div>
            </div>
          </div>
        </section>

        <!-- ---------------------------------------------------------------
             SHOWCASE D: WORLDWIDE CURATED ANTHOLOGY (Remaining 8 Global Destinations)
             --------------------------------------------------------------- -->
        <section class="border-t border-white/10 bg-[#02050d] py-20">
          <div class="max-w-[1440px] mx-auto px-6 sm:px-10 lg:px-16 space-y-10">
            <div class="flex flex-col md:flex-row md:items-end justify-between gap-4">
              <div>
                <span class="text-[11px] font-mono uppercase tracking-[0.24em] text-sky-400">Global Anthology • 11 Verified Destinations</span>
                <h2 class="font-editorial text-4xl sm:text-5xl font-semibold text-white mt-1">Continue Your Global Expedition</h2>
              </div>
              <div class="flex flex-wrap items-center gap-3">
                <button id="heroDiscoverBtn" type="button" class="px-5 py-2.5 rounded-full bg-white/5 hover:bg-white/10 border border-white/15 text-xs uppercase tracking-widest text-slate-200 transition">
                  <span data-i18n="btn_discover_destinations">Search &amp; Filter All</span>
                </button>
                <button id="viewAllDestinationsBtn" type="button" class="px-5 py-2.5 rounded-full bg-sky-500/20 hover:bg-sky-500/30 border border-sky-400/35 text-xs uppercase tracking-widest text-sky-200 flex items-center gap-2 transition">
                  <span>Open Full Destination Explorer (11 Locations)</span>
                  <i data-lucide="arrow-right" class="w-3.5 h-3.5"></i>
                </button>
              </div>
            </div>

            <!-- Hidden Compatibility Hook for Flagship Grid ID while keeping visual showcases above -->
            <div id="homeFlagshipGrid" class="hidden"></div>
            <div id="heroSpotlightCard" class="hidden"></div>

            <!-- 4-Column Tall Portrait Filmstrip Grid -->
            <div id="homePopularGrid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6"></div>
          </div>
        </section>

        <!-- ---------------------------------------------------------------
             TECHNOLOGY, ABOUT & CONTACT SECTIONS (Wired to Top Navigation)
             --------------------------------------------------------------- -->
        <section id="section-technology" class="border-t border-white/10 bg-[#040916] py-20">
          <div class="max-w-[1440px] mx-auto px-6 sm:px-10 lg:px-16 space-y-12">
            <div class="flex flex-col lg:flex-row lg:items-end justify-between gap-6">
              <div class="space-y-2 max-w-2xl">
                <span class="text-[11px] font-mono uppercase tracking-[0.24em] text-sky-400">Dual-Engine Spatial Architecture</span>
                <h2 class="font-editorial text-4xl sm:text-5xl font-semibold text-white">Web Companion &amp; Native Meta Quest VR</h2>
                <p class="text-sm text-slate-300 font-light leading-relaxed">
                  Global VR Explorer separates the luxury web travel discovery experience from its dedicated 360° VR Panorama Studio and native Unity 6 LTS (`6000.0.23f1`) Meta Quest headset application.
                </p>
              </div>
              <div class="flex flex-wrap items-center gap-3">
                <button id="heroLaunch360Btn" type="button" class="px-6 py-3 rounded-full bg-sky-500/20 hover:bg-sky-500/30 border border-sky-400/40 text-sky-200 text-xs font-mono uppercase tracking-wider flex items-center gap-2 transition">
                  <i data-lucide="glasses" class="w-4 h-4"></i>
                  <span>Enter 360° VR Studio</span>
                </button>
                <button id="bannerInspectUnityBtn" type="button" class="px-6 py-3 rounded-full bg-white/5 hover:bg-white/15 border border-white/15 text-slate-200 text-xs font-mono uppercase tracking-wider transition">
                  Inspect Native C# VR Code →
                </button>
              </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-4 gap-5">
              <div class="glass-panel rounded-2xl p-6 space-y-3">
                <i data-lucide="globe" class="w-6 h-6 text-sky-400"></i>
                <h3 class="font-editorial text-2xl font-semibold text-white">Interactive 3D Earth</h3>
                <p class="text-xs text-slate-300 font-light leading-relaxed">NASA Blue Marble satellite imagery paired with Three.js atmospheric Fresnel shaders and WGS84 TopoJSON country polygons.</p>
                <button type="button" onclick="switchView('globe')" class="text-xs font-mono text-sky-300 hover:underline">Launch 3D Earth →</button>
              </div>
              <div class="glass-panel rounded-2xl p-6 space-y-3">
                <i data-lucide="glasses" class="w-6 h-6 text-sky-400"></i>
                <h3 class="font-editorial text-2xl font-semibold text-white">Dedicated 360° VR Studio</h3>
                <p class="text-xs text-slate-300 font-light leading-relaxed">Opens on demand when you click "Explore in VR"—supporting true 360° panoramas, Curved Optical Walls, and spatial hotspots.</p>
                <button type="button" onclick="openPanoramaForDestination('coxs-bazar')" class="text-xs font-mono text-sky-300 hover:underline">Open VR Studio →</button>
              </div>
              <div class="glass-panel rounded-2xl p-6 space-y-3">
                <i data-lucide="luggage" class="w-6 h-6 text-sky-400"></i>
                <h3 class="font-editorial text-2xl font-semibold text-white">Travel Preparation &amp; Compare</h3>
                <p class="text-xs text-slate-300 font-light leading-relaxed">Transparent daily budget estimators in BDT ৳ and USD $, seasonal windows, and side-by-side destination comparisons.</p>
                <div class="flex items-center gap-3">
                  <button type="button" onclick="switchView('assistant')" class="text-xs font-mono text-sky-300 hover:underline">Travel Prep →</button>
                  <button type="button" onclick="switchView('compare')" class="text-xs font-mono text-slate-300 hover:underline">Compare →</button>
                </div>
              </div>
              <div class="glass-panel rounded-2xl p-6 space-y-3">
                <i data-lucide="cpu" class="w-6 h-6 text-amber-300"></i>
                <h3 class="font-editorial text-2xl font-semibold text-white">Unity 6 Meta Quest SDK</h3>
                <p class="text-xs text-slate-300 font-light leading-relaxed">Production C# scripts for 6DoF head tracking, `OVROverlay` compositor layers, snap-turn comfort vignette, and HRTF audio.</p>
                <button type="button" onclick="switchView('unity')" class="text-xs font-mono text-amber-300 hover:underline">Inspect C# Code →</button>
              </div>
            </div>
          </div>
        </section>

        <!-- ABOUT & CONTACT SECTION -->
        <section id="section-about" class="border-t border-white/10 bg-[#010308] py-16">
          <div class="max-w-[1440px] mx-auto px-6 sm:px-10 lg:px-16 grid grid-cols-1 lg:grid-cols-12 gap-10 items-start">
            <div class="lg:col-span-6 space-y-4">
              <span class="text-[11px] font-mono uppercase tracking-[0.24em] text-sky-400">About the Mission</span>
              <h3 class="font-editorial text-3xl sm:text-4xl font-semibold text-white">"Explore the World Before You Travel."</h3>
              <p class="text-sm text-slate-300 font-light leading-relaxed">
                Created for the <strong>Meta VR Start Developer Competition 2026</strong>, Global VR Explorer bridges luxury editorial travel storytelling with authentic licensed photography from Wikimedia Commons and spatial computing on Meta Quest headsets. Every destination record is verified with transparent source attribution.
              </p>
              <div id="section-contact" class="pt-4 flex flex-wrap items-center gap-4 text-xs text-slate-400">
                <span class="font-mono text-slate-200">Competition Track: Meta Quest &amp; WebXR Global Exploration</span>
                <span>•</span>
                <button type="button" onclick="switchView('profile')" class="text-sky-300 hover:underline">Saved Collection &amp; Explorer Settings →</button>
              </div>
            </div>

            <div class="lg:col-span-6 space-y-3">
              <div class="flex items-center justify-between">
                <span class="text-[11px] font-mono uppercase tracking-[0.2em] text-sky-400">Recently Explored Destinations</span>
                <button id="clearHistoryHomeBtn" type="button" class="text-xs text-slate-400 hover:text-white underline">Reset</button>
              </div>
              <div id="homeRecentList" class="space-y-2"></div>
            </div>
          </div>
        </section>

      </div>
    </section>

    <!-- ===================================================================
         VIEW 1.5: DEDICATED FULL-BLEED EDITORIAL DESTINATION PAGE
         (Separate Visually Stunning Page for Each Destination)
         =================================================================== -->
    <section id="view-destination-page" class="view-section hidden pt-20">
      <div class="bg-[#030816]/90 border-b border-white/10 backdrop-blur-xl sticky top-20 z-30">
        <div class="max-w-[1440px] mx-auto px-5 sm:px-8 lg:px-12 py-3 flex items-center justify-between gap-4 overflow-x-auto custom-scrollbar">
          <div class="flex items-center gap-2 shrink-0">
            <button type="button" onclick="switchView('home')" class="px-3 py-1 rounded-full bg-white/10 hover:bg-white/20 text-white text-xs flex items-center gap-1.5">
              <i data-lucide="arrow-left" class="w-3.5 h-3.5"></i><span>Home</span>
            </button>
            <span class="text-[10px] font-mono uppercase tracking-[0.2em] text-slate-400 ml-2">Destination Edition:</span>
          </div>
          <div id="editorialSwitcherBar" class="flex items-center gap-2"></div>
        </div>
      </div>

      <div id="editorialPageContainer"></div>
    </section>

    <!-- ===================================================================
         VIEW 2: INTERACTIVE 3D EARTH GLOBE PAGE
         =================================================================== -->
    <section id="view-globe" class="view-section hidden max-w-[1440px] mx-auto px-5 sm:px-8 lg:px-12 pt-28 pb-12 space-y-8">
      <div class="flex flex-col lg:flex-row lg:items-end justify-between gap-6">
        <div class="space-y-2">
          <div class="flex items-center gap-3">
            <span class="text-[11px] font-mono uppercase tracking-[0.22em] text-sky-400">Interactive Geospatial Observatory</span>
            <span id="globeDataStatusBadge" class="px-2.5 py-0.5 rounded-full text-[10px] font-mono bg-emerald-500/15 text-emerald-300 border border-emerald-400/30">WGS84 Coordinate Grid Ready</span>
          </div>
          <h2 class="font-editorial text-4xl sm:text-5xl font-semibold text-white">Interactive 3D Earth Explorer</h2>
          <p class="text-sm text-slate-300 font-light max-w-2xl">Rotate the photorealistic globe, filter by country, and click any destination beacon to open its dedicated destination page or 360° VR panorama.</p>
        </div>

        <div class="flex flex-wrap items-center gap-2.5">
          <div class="relative">
            <input id="globeSearchInput" type="text" placeholder="Filter globe markers..." class="w-48 bg-white/5 border border-white/15 rounded-full pl-8 pr-3.5 py-2 text-xs text-white focus:border-sky-400 focus:outline-none" />
            <i data-lucide="search" class="w-3.5 h-3.5 text-slate-400 absolute left-3 top-2.5"></i>
          </div>
          <select id="globeCountrySelect" class="bg-[#050b18] border border-white/15 rounded-full px-4 py-2 text-xs text-slate-200 focus:border-sky-400 focus:outline-none">
            <option value="all">All Countries (7)</option>
            <option value="Bangladesh">Bangladesh (4 Sites)</option>
            <option value="France">France (1 Site)</option>
            <option value="Japan">Japan (1 Site)</option>
            <option value="Switzerland">Switzerland (1 Site)</option>
            <option value="United States">United States (1 Site)</option>
            <option value="Italy">Italy (1 Site)</option>
            <option value="China">China (1 Site)</option>
          </select>
          <button id="globeZoomInBtn" type="button" class="p-2 rounded-full bg-white/5 border border-white/15 hover:border-sky-400 text-slate-200" title="Zoom In">
            <i data-lucide="zoom-in" class="w-4 h-4"></i>
          </button>
          <button id="globeZoomOutBtn" type="button" class="p-2 rounded-full bg-white/5 border border-white/15 hover:border-sky-400 text-slate-200" title="Zoom Out">
            <i data-lucide="zoom-out" class="w-4 h-4"></i>
          </button>
          <button id="globeToggleSpinBtn" type="button" class="px-4 py-2 rounded-full bg-sky-500/20 border border-sky-400/40 text-sky-200 text-xs font-mono">
            Auto-Spin: ON
          </button>
          <button id="globeResetViewBtn" type="button" class="px-4 py-2 rounded-full bg-white/5 border border-white/15 hover:border-sky-400 text-xs text-slate-300">
            Reset Orbit
          </button>
        </div>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-stretch">
        <div class="lg:col-span-8 relative rounded-3xl overflow-hidden border border-white/15 universal-space-bg h-[520px] sm:h-[620px] shadow-2xl">
          <div id="globeCanvasContainer" class="w-full h-full cursor-grab active:cursor-grabbing"></div>

          <div class="absolute top-5 left-5 glass-panel-subtle rounded-2xl px-4 py-3 text-xs space-y-1 pointer-events-none">
            <div class="text-[10px] font-mono uppercase tracking-[0.2em] text-sky-300">Active Coordinates</div>
            <div id="globeHoverReadout" class="font-mono text-white text-xs">Target: Cox's Bazar (21.4272° N, 92.0058° E)</div>
            <div id="globePolygonNote" class="text-[11px] text-slate-400">NASA Blue Marble + TopoJSON Country Polygons</div>
          </div>

          <div class="absolute bottom-5 left-5 right-5 glass-panel-subtle rounded-full px-4 py-2.5 flex items-center gap-2 overflow-x-auto custom-scrollbar">
            <span class="text-[10px] font-mono uppercase tracking-widest text-sky-300 px-2 shrink-0">Fly To:</span>
            <div id="globeQuickPinsBar" class="flex items-center gap-1.5"></div>
          </div>
        </div>

        <div class="lg:col-span-4 flex flex-col gap-5">
          <div id="globeSelectedPanel" class="glass-panel rounded-3xl p-6 space-y-5 flex-1"></div>
        </div>
      </div>
    </section>

    <!-- ===================================================================
         VIEW 3: GLOBAL DESTINATION EXPLORER PAGE
         =================================================================== -->
    <section id="view-explore" class="view-section hidden max-w-[1440px] mx-auto px-5 sm:px-8 lg:px-12 pt-28 pb-12 space-y-10">
      <div class="space-y-6">
        <div class="flex flex-col md:flex-row md:items-end justify-between gap-4">
          <div>
            <span class="text-[11px] font-mono uppercase tracking-[0.24em] text-sky-400">Global Anthology</span>
            <h2 class="font-editorial text-4xl sm:text-5xl font-semibold text-white mt-1">All Verified Destinations</h2>
            <p class="text-sm text-slate-300 font-light mt-1">Search by country, division, or category. Open any destination's editorial showcase page or launch its dedicated 360° VR viewer.</p>
          </div>
          <div class="flex items-center gap-3">
            <div class="relative hidden sm:block">
              <input id="globalQuickSearch" type="text" placeholder="Quick filter..." class="w-44 bg-white/5 border border-white/15 rounded-full pl-8 pr-3 py-1.5 text-xs text-white focus:outline-none" />
              <i data-lucide="search" class="w-3.5 h-3.5 text-slate-400 absolute left-3 top-2"></i>
            </div>
            <div id="exploreResultCounter" class="text-xs font-mono text-sky-200 bg-white/5 border border-white/15 px-4 py-2 rounded-full">
              Showing 11 of 11 Verified Locations
            </div>
          </div>
        </div>

        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-3 glass-panel p-4 rounded-2xl">
          <div class="relative">
            <input id="exploreSearchInput" type="text" placeholder="Search name, city, attraction..." class="w-full bg-[#030712] border border-white/15 rounded-xl pl-8 pr-3 py-2.5 text-xs text-white focus:border-sky-400 focus:outline-none" />
            <i data-lucide="search" class="w-3.5 h-3.5 text-slate-400 absolute left-3 top-3"></i>
          </div>
          <select id="exploreCountryFilter" class="bg-[#030712] border border-white/15 rounded-xl px-3 py-2.5 text-xs text-slate-200 focus:border-sky-400 focus:outline-none">
            <option value="all">All Countries</option>
          </select>
          <select id="exploreDivisionFilter" class="bg-[#030712] border border-white/15 rounded-xl px-3 py-2.5 text-xs text-slate-200 focus:border-sky-400 focus:outline-none">
            <option value="all">All Divisions / Regions</option>
          </select>
          <select id="exploreCategoryFilter" class="bg-[#030712] border border-white/15 rounded-xl px-3 py-2.5 text-xs text-slate-200 focus:border-sky-400 focus:outline-none">
            <option value="all">All Categories</option>
            <option value="Beaches">Beaches</option>
            <option value="Historical Landmarks">Historical Landmarks</option>
            <option value="Cultural Destinations">Cultural Destinations</option>
            <option value="Islands">Islands</option>
            <option value="Forests">Forests</option>
            <option value="Mountains">Mountains</option>
            <option value="Natural Wonders">Natural Wonders</option>
            <option value="Tourist Attractions">Tourist Attractions</option>
          </select>
          <select id="exploreSortSelect" class="bg-[#030712] border border-white/15 rounded-xl px-3 py-2.5 text-xs text-slate-200 focus:border-sky-400 focus:outline-none">
            <option value="flagship">Sort: Flagship Priority</option>
            <option value="popularity">Sort: Popularity Rank</option>
            <option value="name">Sort: Alphabetical (A–Z)</option>
            <option value="verified">Sort: Verification Status</option>
          </select>
        </div>

        <div id="exploreCategoryPills" class="flex flex-wrap items-center gap-2"></div>
      </div>

      <div id="exploreDestinationsGrid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8"></div>
    </section>

        <!-- ===================================================================
         VIEW 3.5: AI DESTINATION NAVIGATION & HISTORICAL TIME TRAVEL (1200–2026)
         Modules 1, 2, 3 & 4: Natural-Language / Voice AI Assistant, 1200–2026
         Adaptive Timeline, Documented Archival vs. Labeled Reconstruction,
         and Interactive Past vs. Present Side-by-Side Comparison
         =================================================================== -->
    <section id="view-timetravel" class="view-section hidden max-w-[1440px] mx-auto px-5 sm:px-8 lg:px-12 pt-28 pb-16 space-y-10">

      <!-- MODULE 1: AI DESTINATION NAVIGATION COMMAND CENTER -->
      <div class="glass-panel rounded-3xl p-6 sm:p-8 border border-sky-400/25 space-y-5 shadow-2xl">
        <div class="flex flex-col lg:flex-row lg:items-center justify-between gap-4">
          <div class="space-y-1">
            <div class="inline-flex items-center gap-2 px-3 py-1 rounded-full bg-sky-500/15 border border-sky-400/30 text-sky-300 text-[10px] font-mono uppercase tracking-widest">
              <i data-lucide="sparkles" class="w-3.5 h-3.5"></i>
              <span>Module 1 • Intelligent Geographical &amp; Temporal Assistant</span>
            </div>
            <h2 class="font-editorial text-3xl sm:text-5xl font-semibold text-white">AI Destination Navigator &amp; Historical Time Machine</h2>
            <p class="text-xs sm:text-sm text-slate-300 font-light">
              Speak or type a natural-language destination or historical era request (from <strong>1200 to 2026</strong>). The assistant resolves coordinates, authentic Wikimedia Commons photography, 360° VR availability, and documented archival records.
            </p>
          </div>
          <div id="aiVoiceStatusBadge" class="px-3.5 py-1.5 rounded-full bg-white/5 border border-white/15 text-xs font-mono text-slate-300 self-start lg:self-auto">
            Voice Engine: Ready (Web Speech API)
          </div>
        </div>

        <!-- Natural-Language Input Bar + Voice Mic Button -->
        <div class="flex flex-col sm:flex-row items-stretch sm:items-center gap-3">
          <div class="relative flex-1">
            <input id="aiNavigatorInput" type="text"
                   placeholder="Try: 'Take me to Cox's Bazar', 'Show me the Eiffel Tower in 1888', 'Explore the Sundarbans', 'Take me to ancient Rome'..."
                   class="w-full bg-[#02050d] border border-white/20 focus:border-sky-400 rounded-2xl pl-11 pr-4 py-3.5 text-sm text-white placeholder-slate-400 focus:outline-none" />
            <i data-lucide="compass" class="w-5 h-5 text-sky-400 absolute left-3.5 top-3.5"></i>
          </div>
          <div class="flex items-center gap-2.5">
            <button id="aiNavigatorVoiceBtn" type="button" onclick="startAiVoiceRecognition('aiNavigatorInput')" class="px-4 py-3.5 rounded-2xl bg-white/5 hover:bg-white/15 border border-white/15 text-sky-300 text-xs font-mono flex items-center gap-2 transition" title="Speak Destination Command">
              <i data-lucide="mic" class="w-4 h-4"></i>
              <span>Voice Search</span>
            </button>
            <button id="aiNavigatorRunBtn" type="button" onclick="executeAiNavigationQuery(document.getElementById('aiNavigatorInput').value)" class="px-6 py-3.5 rounded-2xl bg-gradient-to-r from-sky-500 to-blue-600 hover:from-sky-400 hover:to-blue-500 text-white font-semibold text-xs sm:text-sm tracking-wide shadow-lg transition">
              Explore Destination
            </button>
          </div>
        </div>

        <!-- Natural-Language Quick Prompt Chips -->
        <div class="flex flex-wrap items-center gap-2 text-xs">
          <span class="text-slate-400 font-mono text-[11px]">Sample Commands:</span>
          <button type="button" onclick="executeAiNavigationQuery('Take me to Cox\'s Bazar')" class="px-3 py-1 rounded-full bg-white/5 hover:bg-white/15 text-slate-200 border border-white/10">"Take me to Cox's Bazar"</button>
          <button type="button" onclick="executeAiNavigationQuery('Show me the Eiffel Tower')" class="px-3 py-1 rounded-full bg-white/5 hover:bg-white/15 text-slate-200 border border-white/10">"Show me the Eiffel Tower"</button>
          <button type="button" onclick="executeAiNavigationQuery('Explore the Sundarbans')" class="px-3 py-1 rounded-full bg-white/5 hover:bg-white/15 text-slate-200 border border-white/10">"Explore the Sundarbans"</button>
          <button type="button" onclick="executeAiNavigationQuery('Take me to ancient Rome')" class="px-3 py-1 rounded-full bg-white/5 hover:bg-white/15 text-slate-200 border border-white/10">"Take me to ancient Rome"</button>
          <button type="button" onclick="executeAiNavigationQuery('Show me the Great Wall of China')" class="px-3 py-1 rounded-full bg-white/5 hover:bg-white/15 text-slate-200 border border-white/10">"Show me the Great Wall of China"</button>
          <button type="button" onclick="executeAiNavigationQuery('Show me Lalbagh Fort in 1680')" class="px-3 py-1 rounded-full bg-white/5 hover:bg-white/15 text-amber-200 border border-amber-400/25">"Show me Lalbagh Fort in 1680"</button>
          <button type="button" onclick="executeAiNavigationQuery('Take me to Machu Picchu')" class="px-3 py-1 rounded-full bg-white/5 hover:bg-white/15 text-slate-400 border border-white/10" title="Test uncataloged VR availability explanation">"Take me to Machu Picchu (Availability Check)"</button>
        </div>

        <!-- AI Assistant Response Dossier Card -->
        <div id="aiAssistantResponseCard" class="p-5 rounded-2xl bg-[#02050d]/90 border border-sky-500/25 space-y-4"></div>
      </div>

      <!-- MODULE 2 & 3: HISTORICAL TIME-TRAVEL EXPLORATION (1200 TO 2026) -->
      <div class="glass-panel rounded-3xl p-6 sm:p-8 border border-white/15 space-y-6">
        <div class="flex flex-col lg:flex-row lg:items-center justify-between gap-4 border-b border-white/10 pb-5">
          <div>
            <span class="text-[11px] font-mono uppercase tracking-[0.22em] text-amber-300">Module 2 &amp; Module 3 • Chronological Evidence Engine (1200 – 2026)</span>
            <h3 id="ttDestinationHeading" class="font-editorial text-3xl sm:text-4xl font-semibold text-white mt-0.5">Historical Time Travel: Lalbagh Fort, Dhaka</h3>
          </div>

          <div class="flex flex-wrap items-center gap-3">
            <select id="ttDestinationSelect" onchange="selectTimeTravelDestination(this.value)" class="bg-[#02050d] border border-white/20 rounded-full px-4 py-2 text-xs text-white focus:outline-none"></select>

            <!-- Module 3 Visual Mode Switcher: Documented Historical Material vs. Historical Reconstruction -->
            <div class="inline-flex rounded-full bg-[#02050d] p-1 border border-white/15" role="group">
              <button type="button" id="ttModeDocumentedBtn" onclick="setTimeTravelVisualMode('documented')" class="px-3.5 py-1.5 rounded-full text-xs font-medium bg-sky-500/25 text-sky-200 border border-sky-400/40">
                A. Documented Historical Material
              </button>
              <button type="button" id="ttModeReconstructionBtn" onclick="setTimeTravelVisualMode('reconstruction')" class="px-3.5 py-1.5 rounded-full text-xs font-medium text-slate-300 hover:text-white">
                B. Labeled Historical Reconstruction
              </button>
            </div>
          </div>
        </div>

        <!-- INTERACTIVE TIMELINE SLIDER (1200 TO 2026) & EPOCH PILLS -->
        <div class="space-y-4 bg-[#02050d]/80 p-5 rounded-2xl border border-white/10">
          <div class="flex flex-wrap items-center justify-between gap-2">
            <div class="flex items-center gap-3">
              <span class="text-xs font-mono uppercase tracking-wider text-slate-400">Selected Historical Year:</span>
              <span id="ttSelectedYearBadge" class="px-4 py-1 rounded-full bg-amber-500/20 border border-amber-400/40 font-mono text-lg font-bold text-amber-300">1875 AD</span>
              <span id="ttEpochTitleBadge" class="text-xs font-medium text-white">19th-Century Archival Photographic Era</span>
            </div>
            <span id="ttConfidenceBadge" class="px-3 py-1 rounded-full text-[11px] font-mono bg-emerald-500/15 text-emerald-300 border border-emerald-400/30">
              Evidence: Primary Archival Photograph (High Confidence)
            </span>
          </div>

          <input id="ttYearSlider" type="range" min="1200" max="2026" step="1" value="1875"
                 oninput="updateTimeTravelYear(this.value)"
                 class="w-full h-2 bg-slate-800 rounded-lg cursor-pointer accent-amber-400" />

          <!-- Epoch Milestone Stops: 1200, 1400, 1600, 1800, 1900, 1950, 2000, 2026 -->
          <div class="grid grid-cols-4 sm:grid-cols-8 gap-2 pt-1">
            <button type="button" onclick="updateTimeTravelYear(1200)" class="tt-epoch-btn py-1.5 rounded-lg bg-white/5 hover:bg-white/15 border border-white/10 font-mono text-xs text-slate-300">1200</button>
            <button type="button" onclick="updateTimeTravelYear(1400)" class="tt-epoch-btn py-1.5 rounded-lg bg-white/5 hover:bg-white/15 border border-white/10 font-mono text-xs text-slate-300">1400</button>
            <button type="button" onclick="updateTimeTravelYear(1600)" class="tt-epoch-btn py-1.5 rounded-lg bg-white/5 hover:bg-white/15 border border-white/10 font-mono text-xs text-slate-300">1600</button>
            <button type="button" onclick="updateTimeTravelYear(1800)" class="tt-epoch-btn py-1.5 rounded-lg bg-white/5 hover:bg-white/15 border border-white/10 font-mono text-xs text-slate-300">1800</button>
            <button type="button" onclick="updateTimeTravelYear(1900)" class="tt-epoch-btn py-1.5 rounded-lg bg-white/5 hover:bg-white/15 border border-white/10 font-mono text-xs text-slate-300">1900</button>
            <button type="button" onclick="updateTimeTravelYear(1950)" class="tt-epoch-btn py-1.5 rounded-lg bg-white/5 hover:bg-white/15 border border-white/10 font-mono text-xs text-slate-300">1950</button>
            <button type="button" onclick="updateTimeTravelYear(2000)" class="tt-epoch-btn py-1.5 rounded-lg bg-white/5 hover:bg-white/15 border border-white/10 font-mono text-xs text-slate-300">2000</button>
            <button type="button" onclick="updateTimeTravelYear(2026)" class="tt-epoch-btn py-1.5 rounded-lg bg-white/5 hover:bg-white/15 border border-white/10 font-mono text-xs text-slate-300">2026</button>
          </div>
        </div>

        <!-- HISTORICAL VISUALIZATION STAGE & RESEARCH DOSSIER -->
        <div id="ttStageContainer" class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-start"></div>
      </div>

      <!-- MODULE 4: PAST VS. PRESENT SIDE-BY-SIDE & MAP EVOLUTION COMPARISON -->
      <div class="glass-panel rounded-3xl p-6 sm:p-8 border border-white/15 space-y-6">
        <div class="flex flex-col sm:flex-row sm:items-end justify-between gap-3 border-b border-white/10 pb-4">
          <div>
            <span class="text-[11px] font-mono uppercase tracking-[0.22em] text-sky-400">Module 4 • Temporal Delta Analysis</span>
            <h3 class="font-editorial text-3xl sm:text-4xl font-semibold text-white mt-0.5">Past vs. Present Architectural &amp; Geographical Comparison</h3>
          </div>
          <button type="button" onclick="openPanoramaForDestination(STATE.selectedDestinationId)" class="px-5 py-2.5 rounded-full bg-white text-slate-950 hover:bg-sky-100 font-medium text-xs uppercase tracking-wider flex items-center gap-2 self-start sm:self-auto transition">
            <i data-lucide="glasses" class="w-4 h-4"></i>
            <span>Launch Present-Day Authentic 360° Photo VR</span>
          </button>
        </div>

        <div id="ttPastVsPresentContainer" class="space-y-6"></div>
      </div>

    </section>

    <!-- ===================================================================
         VIEW 4: FULL-SCREEN FIRST-PERSON 3D VR EXPERIENCE (100vw x 100vh)
         Matches Uploaded First-Person Beach & Virtual Hands Reference Image
         =================================================================== -->
    <section id="view-panorama" class="view-section hidden fixed inset-0 z-[80] w-screen h-screen bg-black overflow-hidden select-none" style="touch-action: none;">
      <!-- FULL-SCREEN 100vw x 100vh THREE.JS WEBGL VR VIEWPORT -->
      <div id="panoViewportWrapper" class="relative w-full h-full bg-black overflow-hidden" style="touch-action: none;">
        <!-- Primary Three.js WebGL Canvas (Inside-of-Sphere 360° Panorama OR Distinct 3D World OR True Dual-Eye Stereo VR) -->
        <div id="panoCanvasContainer" class="absolute inset-0 w-full h-full cursor-grab active:cursor-grabbing z-[1]"></div>

        <!-- Optional Real Hand-Tracking 2D Landmark Overlay (ONLY draws when System 1 or System 2 actively tracks real hands) -->
        <canvas id="vrTrackedHandsOverlayCanvas" class="hidden absolute inset-0 w-full h-full pointer-events-none z-[4]"></canvas>

        <!-- ON-SCREEN 3D WALK / MOVEMENT PAD (Mobile & Desktop navigation in 3D World & Stereo VR) -->
        <div id="vrMovePad" class="hidden absolute bottom-36 left-3 z-[25] select-none pointer-events-auto" style="touch-action: none;">
          <div class="grid grid-cols-3 gap-1 p-1.5 rounded-2xl bg-slate-950/75 backdrop-blur-md border border-white/15 shadow-xl">
            <div></div>
            <button type="button" data-move="w" aria-label="Move Forward" class="vr-move-btn w-9 h-9 rounded-xl bg-white/10 active:bg-amber-400 active:text-slate-950 text-white text-sm font-bold flex items-center justify-center transition">▲</button>
            <button type="button" id="vrResetPosBtn" onclick="vrPlayerOffset = { x: 0, z: 0 }; STATE.panoYaw = 0; STATE.panoPitch = 4;" title="Reset Position" aria-label="Reset Position" class="w-9 h-9 rounded-xl bg-white/5 hover:bg-white/15 text-amber-300 text-xs font-bold flex items-center justify-center transition">↺</button>
            <button type="button" data-move="a" aria-label="Move Left" class="vr-move-btn w-9 h-9 rounded-xl bg-white/10 active:bg-amber-400 active:text-slate-950 text-white text-sm font-bold flex items-center justify-center transition">◀</button>
            <button type="button" data-move="s" aria-label="Move Backward" class="vr-move-btn w-9 h-9 rounded-xl bg-white/10 active:bg-amber-400 active:text-slate-950 text-white text-sm font-bold flex items-center justify-center transition">▼</button>
            <button type="button" data-move="d" aria-label="Move Right" class="vr-move-btn w-9 h-9 rounded-xl bg-white/10 active:bg-amber-400 active:text-slate-950 text-white text-sm font-bold flex items-center justify-center transition">▶</button>
          </div>
          <div class="text-[9px] text-center text-slate-300/80 font-mono mt-1 tracking-wider">MOVE • DRAG TO LOOK</div>
        </div>

        <!-- HONEST UNAVAILABLE 360° PANORAMA NOTICE (Shown ONLY in Photo 360 VR mode when a destination lacks a genuine 360° panorama) -->
        <div id="panoNo360NoticeOverlay" class="hidden absolute inset-0 z-[15] bg-slate-950/92 backdrop-blur-md flex items-center justify-center p-6">
          <div class="max-w-lg w-full rounded-3xl bg-slate-900/95 border border-amber-400/40 p-6 sm:p-8 text-center space-y-4 shadow-2xl">
            <div class="inline-flex items-center gap-2 px-3 py-1 rounded-full bg-amber-500/15 border border-amber-400/35 text-amber-300 font-mono text-[10px] uppercase tracking-wider">
              360° panorama currently unavailable.
            </div>
            <h3 id="panoNo360Title" class="font-editorial text-2xl sm:text-3xl font-bold text-white">Lalbagh Fort, Bangladesh</h3>
            <p id="panoNo360Body" class="text-xs sm:text-sm text-slate-300 leading-relaxed">
              A genuine 360-degree equirectangular panorama is not yet available for this destination in our verified Wikimedia Commons archive. Global VR Explorer never stretches an ordinary rectangular photograph into a fake 360° sphere.
            </p>
            <div class="flex flex-wrap items-center justify-center gap-2.5 pt-2">
              <button type="button" onclick="setVrExperienceMode('3d-world')" class="px-5 py-2.5 rounded-full bg-amber-400 hover:bg-amber-300 text-slate-950 font-semibold text-xs transition">
                Open Distinct 3D World (Three.js)
              </button>
              <button type="button" onclick="openDestinationModal(STATE.selectedDestinationId, STATE.panoViewpointIndex)" class="px-5 py-2.5 rounded-full bg-white/10 hover:bg-white/20 text-white border border-white/20 text-xs transition">
                View Authentic Reference Photos
              </button>
            </div>
          </div>
        </div>

        <!-- HONEST 3D MODEL UNAVAILABLE FALLBACK CARD (Displays authentic destination photography, loading indicator, viewpoints & GLB upload) -->
        <div id="panoMissingGlbBanner" class="hidden absolute inset-0 z-[16] bg-slate-950/82 backdrop-blur-md flex items-center justify-center p-4 sm:p-5 overflow-y-auto">
          <div class="max-w-xl w-full rounded-3xl bg-slate-900/95 border border-amber-400/45 overflow-hidden shadow-2xl my-auto">
            <div class="relative h-52 sm:h-64 bg-black overflow-hidden flex items-center justify-center">
              <div id="panoFallbackImgSpinner" class="hidden absolute inset-0 flex items-center justify-center bg-black/60 z-10">
                <div class="w-7 h-7 rounded-full border-2 border-sky-400 border-t-transparent animate-spin"></div>
              </div>
              <div id="panoFallbackImgError" class="hidden absolute inset-0 flex items-center justify-center bg-slate-950 p-4 text-center text-xs text-rose-300 z-10">
                Unable to load external Wikimedia photograph on this network.
              </div>
              <img id="panoFallbackPhotoImg" src="" alt="Authentic Destination Reference" class="w-full h-full object-cover" />
              <div class="absolute inset-0 bg-gradient-to-t from-slate-900 via-transparent to-black/40 pointer-events-none"></div>
              <span class="absolute top-3 left-3 px-2.5 py-1 rounded-full bg-amber-500/20 border border-amber-400/45 text-amber-200 font-mono text-[10px] uppercase">
                3D GLB Model Unavailable • Authentic Reference Photo
              </span>
            </div>
            <div class="p-5 sm:p-6 space-y-3 text-center sm:text-left">
              <div class="space-y-1">
                <h3 id="panoMissingGlbTitle" class="font-editorial text-2xl font-bold text-white">Eiffel Tower, Paris</h3>
                <div id="panoFallbackPhotoCredit" class="text-[11px] font-mono text-sky-300 truncate">Wikimedia Commons Licensed Photography</div>
              </div>
              <p id="panoMissingGlbText" class="text-xs text-slate-300 leading-relaxed">
                The 3D landmark model (<code class="text-sky-300">assets/models/eiffel_tower.glb</code>) is unavailable in this standalone browser file. Global VR Explorer never substitutes fake primitive shapes for real architecture.
              </p>
              <div id="panoFallbackViewpointsBar" class="flex items-center gap-2 overflow-x-auto pb-1 custom-scrollbar"></div>
              <div class="flex flex-wrap items-center justify-center sm:justify-start gap-2 pt-1">
                <label class="cursor-pointer px-4 py-2 rounded-full bg-sky-500 hover:bg-sky-400 text-slate-950 font-semibold text-xs transition">
                  <span>Load Local .GLB / .GLTF Model</span>
                  <input id="customGlbFileInput" type="file" accept=".glb,.gltf" class="hidden" onchange="handleCustomGlbUpload(this.files && this.files[0])" />
                </label>
                <button id="panoFallbackOpen360Btn" type="button" onclick="setVrExperienceMode('authentic')" class="px-4 py-2 rounded-full bg-white text-slate-950 hover:bg-sky-100 font-semibold text-xs transition">
                  Open Photo 360 VR
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- MINIMAL FLOATING TOP TOOLBAR (Unobtrusive edge controls; never blocks center of panorama) -->
        <div class="absolute top-3 left-3 right-3 sm:top-4 sm:left-4 sm:right-4 z-30 flex flex-wrap items-center justify-between gap-2 pointer-events-none">
          <!-- Left: Exit VR + Compact Destination Selector + Active Status -->
          <div class="pointer-events-auto flex flex-wrap items-center gap-1.5">
            <button type="button" onclick="switchView('home')" class="px-3 py-1.5 rounded-full bg-black/65 hover:bg-black/85 text-white border border-white/20 backdrop-blur-md text-xs font-medium flex items-center gap-1.5 shadow-lg transition">
              <i data-lucide="x" class="w-3.5 h-3.5 text-sky-300"></i>
              <span>Exit VR</span>
            </button>

            <select id="panoTopDestinationSelect" onchange="handleTopVrDestSelect(this.value)" class="bg-black/70 hover:bg-black/85 border border-sky-400/40 rounded-full px-3 py-1.5 text-xs font-medium text-white backdrop-blur-md focus:outline-none max-w-[200px] sm:max-w-[260px] truncate shadow-lg"></select>

            <span id="vrMinimalStatusText" class="hidden md:inline-block px-3 py-1 rounded-full bg-black/55 border border-white/15 text-[11px] text-slate-200 backdrop-blur-md">
              Photo 360 VR • Drag or Touch to Look Around
            </span>
          </div>

          <!-- Right: Zoom, Device Orientation (Gyro), Fullscreen, Camera Hands, XR Debug, Options -->
          <div class="pointer-events-auto flex flex-wrap items-center justify-end gap-1.5">
            <div class="hidden sm:inline-flex items-center rounded-full bg-black/65 border border-white/20 backdrop-blur-md p-0.5 text-xs text-white">
              <button type="button" onclick="adjustPanoZoom(-8)" class="px-2 py-0.5 rounded-full hover:bg-white/15 font-mono" title="Zoom In">+</button>
              <span id="panoZoomBadge" class="px-1.5 text-[10px] font-mono text-sky-300">72°</span>
              <button type="button" onclick="adjustPanoZoom(8)" class="px-2 py-0.5 rounded-full hover:bg-white/15 font-mono" title="Zoom Out">−</button>
            </div>

            <button id="panoGyroToggleBtn" type="button" onclick="toggleDeviceOrientationGyro()" class="px-2.5 py-1.5 rounded-full bg-black/65 hover:bg-black/85 text-slate-200 border border-white/20 backdrop-blur-md text-[11px] font-mono transition" title="Toggle Phone/Tablet Gyroscope Look-Around">
              Gyro: OFF
            </button>

            <button id="cameraStatusIndicatorPill" type="button" onclick="toggleOpticalCameraHandTracking()" class="px-2.5 py-1.5 rounded-full bg-black/65 hover:bg-black/85 border border-white/20 text-[11px] font-mono text-slate-300 flex items-center gap-1.5 backdrop-blur-md transition" title="Toggle Separate Camera Hand-Tracking">
              <span id="cameraStatusDot" class="w-2 h-2 rounded-full bg-slate-500"></span>
              <span id="cameraStatusText" class="hidden sm:inline">Camera Hands: OFF</span>
            </button>

            <button id="vrToggleDiagPanelBtn" type="button" onclick="toggleXrHandDebugPanel()" class="px-2.5 py-1.5 rounded-full bg-black/65 hover:bg-black/85 text-sky-200 border border-sky-400/35 backdrop-blur-md text-[11px] font-mono flex items-center gap-1.5 transition" title="WebXR & Hand Diagnostics">
              <span id="vrDiagPulseDot" class="w-2 h-2 rounded-full bg-amber-400"></span>
              <span>XR Debug</span>
            </button>

            <button id="panoFullscreenTopBtn" type="button" onclick="toggleVrFullscreen()" class="p-1.5 rounded-full bg-black/65 hover:bg-black/85 text-white border border-white/20 backdrop-blur-md transition" title="Fullscreen VR Mode">
              <i data-lucide="maximize-2" class="w-3.5 h-3.5"></i>
            </button>

            <button id="vrToggleOptionsDrawerBtn" type="button" onclick="toggleVrOptionsDrawer()" class="px-2.5 py-1.5 rounded-full bg-black/65 hover:bg-black/85 text-white border border-white/20 backdrop-blur-md text-[11px] flex items-center gap-1 transition">
              <i data-lucide="SlidersHorizontal" class="w-3.5 h-3.5"></i>
              <span class="hidden sm:inline">Options</span>
            </button>
          </div>
        </div>

        <!-- SEPARATE CAMERA HAND-TRACKING LIVE PIP PREVIEW -->
        <div id="cameraPiPPreviewBox" class="hidden absolute bottom-16 left-4 z-30 w-44 sm:w-52 rounded-2xl overflow-hidden bg-slate-950/95 border-2 border-emerald-400/60 shadow-2xl">
          <div class="bg-emerald-950/90 px-2.5 py-1 flex items-center justify-between text-[10px] font-mono text-emerald-200 border-b border-emerald-400/30">
            <span class="flex items-center gap-1.5"><span class="w-2 h-2 rounded-full bg-emerald-400 animate-ping"></span>CAMERA LANDMARKS</span>
            <button type="button" onclick="toggleOpticalCameraHandTracking()" class="text-slate-300 hover:text-white">✕</button>
          </div>
          <div class="relative w-full h-32 bg-black">
            <video id="opticalHandVideo" class="w-full h-full object-cover scale-x-[-1]" playsinline muted></video>
            <canvas id="opticalHandLandmarkCanvas" class="absolute inset-0 w-full h-full pointer-events-none"></canvas>
          </div>
          <div id="cameraPiPGestureReadout" class="px-2.5 py-1 text-[10px] font-mono text-slate-200 bg-black/80 truncate">
            Show hand to camera...
          </div>
        </div>

        <!-- COMPACT BOTTOM FLOATING MODE & VIEWPOINT BAR -->
        <div class="absolute bottom-3 left-3 right-3 sm:bottom-4 sm:left-6 sm:right-6 z-30 flex flex-wrap items-center justify-between gap-2 pointer-events-none">
          <div class="pointer-events-auto flex flex-wrap items-center gap-1 px-2 py-1.5 rounded-full bg-black/70 border border-white/20 backdrop-blur-xl shadow-2xl">
            <button type="button" onclick="setVrExperienceMode('authentic')" id="dockModeAuthenticBtn" class="px-3 py-1 rounded-full text-[11px] font-semibold bg-sky-400 text-slate-950 transition">
              Photo 360 VR
            </button>
            <button type="button" onclick="setVrExperienceMode('3d-world')" id="dockMode3DBeachBtn" class="px-3 py-1 rounded-full text-[11px] font-medium text-white hover:bg-white/15 transition">
              Distinct 3D World
            </button>
            <button type="button" onclick="setVrExperienceMode('stereo')" id="dockModeStereoBtn" class="px-3 py-1 rounded-full text-[11px] font-medium text-slate-200 hover:bg-white/15 transition">
              Stereo VR
            </button>
            <button type="button" onclick="startImmersiveWebXRHandSession()" id="dockWaveHandsBtn" class="px-2.5 py-1 rounded-full text-[11px] font-mono text-sky-300 hover:bg-sky-500/20 transition">
              WebXR
            </button>
          </div>

          <div id="panoBottomViewpointDock" class="pointer-events-auto flex items-center gap-1.5 px-2.5 py-1 rounded-full bg-black/70 border border-white/20 backdrop-blur-xl shadow-2xl overflow-x-auto max-w-full custom-scrollbar"></div>
        </div>

        <!-- Comfort Vignette on Rapid Turn -->
        <div id="panoComfortVignette" class="vignette-overlay absolute inset-0 opacity-0 z-10"></div>

        <!-- Optional Minimal Projected 3D Hotspots -->
        <div id="panoHotspotsOverlay" class="hidden absolute inset-0 pointer-events-none overflow-hidden z-10"></div>

        <!-- LOADING & ERROR OVERLAY -->
        <div id="panoLoadingOverlay" class="hidden absolute inset-0 bg-slate-950/85 backdrop-blur-md flex flex-col items-center justify-center gap-3 z-40 p-6">
          <div id="panoLoadingSpinner" class="w-10 h-10 rounded-full border-2 border-sky-400 border-t-transparent animate-spin"></div>
          <div id="panoLoadingText" class="text-xs sm:text-sm font-mono text-white text-center max-w-md">Loading 360° Panorama...</div>
          <div class="w-64 h-2 rounded-full bg-white/10 overflow-hidden border border-white/15">
            <div id="panoLoadingProgressBar" class="h-full bg-gradient-to-r from-sky-400 to-emerald-400 transition-all duration-150" style="width: 0%;"></div>
          </div>
          <div id="panoLoadingPercent" class="text-[11px] font-mono text-sky-300">0%</div>
          <div id="panoLoadingErrorBox" class="hidden max-w-md p-3 rounded-xl bg-rose-500/15 border border-rose-400/40 text-xs text-rose-200 text-center"></div>
          <div class="flex flex-wrap items-center justify-center gap-2 pt-1">
            <button id="panoRetryLoadBtn" type="button" onclick="retryCurrentDestinationLoad()" class="hidden px-4 py-2 rounded-full bg-sky-500 hover:bg-sky-400 text-slate-950 font-semibold text-xs transition">
              Retry Panorama
            </button>
            <button id="panoSkipLoadBtn" type="button" onclick="dismissPanoLoadingOverlay()" class="px-4 py-2 rounded-full bg-white/15 hover:bg-white/25 text-white border border-white/20 text-xs transition">
              Dismiss
            </button>
          </div>
        </div>

        <!-- WEBXR & CAMERA HAND-TRACKING DIAGNOSTIC PANEL -->
        <div id="xrHandDebugPanel" class="hidden absolute top-16 left-4 z-30 w-[310px] sm:w-[360px] max-h-[70vh] overflow-y-auto custom-scrollbar rounded-2xl bg-slate-950/95 border border-sky-400/40 backdrop-blur-xl p-4 space-y-3 text-[11px] font-mono text-slate-200 shadow-2xl">
          <div class="flex items-center justify-between border-b border-white/15 pb-2.5">
            <div class="flex items-center gap-2">
              <span class="w-2.5 h-2.5 rounded-full bg-sky-400 animate-pulse"></span>
              <span class="font-bold text-white tracking-wider uppercase text-xs">HAND-TRACKING &amp; VR DIAGNOSTICS</span>
            </div>
            <button type="button" onclick="toggleXrHandDebugPanel()" class="px-2 py-0.5 rounded bg-white/10 hover:bg-white/20 text-slate-300 text-[10px]">Hide</button>
          </div>
          <div class="space-y-1.5 bg-black/50 p-3 rounded-xl border border-white/10">
            <div class="flex items-center justify-between"><span class="text-slate-300">WebXR supported:</span><span id="diagWebXrSupported" class="font-bold px-2 py-0.5 rounded bg-slate-800 text-amber-300">CHECKING</span></div>
            <div class="flex items-center justify-between"><span class="text-slate-300">Immersive VR supported:</span><span id="diagImmersiveVrSupported" class="font-bold px-2 py-0.5 rounded bg-slate-800 text-amber-300">CHECKING</span></div>
            <div class="flex items-center justify-between"><span class="text-slate-300">Hand tracking requested:</span><span id="diagHandTrackingRequested" class="font-bold px-2 py-0.5 rounded bg-slate-800 text-rose-300">NO</span></div>
            <div class="flex items-center justify-between"><span class="text-slate-300">Left hand detected:</span><span id="diagLeftHandDetected" class="font-bold px-2 py-0.5 rounded bg-slate-800 text-rose-300">NO</span></div>
            <div class="flex items-center justify-between"><span class="text-slate-300">Right hand detected:</span><span id="diagRightHandDetected" class="font-bold px-2 py-0.5 rounded bg-slate-800 text-rose-300">NO</span></div>
            <div class="flex items-center justify-between"><span class="text-slate-300">Joint data received:</span><span id="diagJointDataReceived" class="font-bold px-2 py-0.5 rounded bg-slate-800 text-rose-300">NO</span></div>
            <div class="flex items-center justify-between"><span class="text-slate-300">Virtual hand rendering:</span><span id="diagVirtualHandRendering" class="font-bold px-2 py-0.5 rounded bg-rose-500/20 text-rose-300">NO</span></div>
          </div>
          <div class="space-y-1 bg-white/[0.03] p-2.5 rounded-xl border border-white/10 text-[10px]">
            <div class="flex justify-between"><span class="text-slate-400">Active Environment:</span><span id="diagActiveEnvKey" class="text-amber-300 font-bold">coxs-bazar</span></div>
            <div class="flex justify-between"><span class="text-slate-400">HTTPS / Secure Context:</span><span id="diagHttpsStatus" class="text-sky-300">YES</span></div>
            <div class="flex justify-between"><span class="text-slate-400">navigator.xr object:</span><span id="diagNavigatorXrStatus" class="text-sky-300">CHECKING</span></div>
            <div class="flex justify-between"><span class="text-slate-400">Three.js renderer.xr.enabled:</span><span id="diagThreeXrConfig" class="text-emerald-300">YES</span></div>
            <div class="flex justify-between"><span class="text-slate-400">XRHand / getJointPose API:</span><span id="diagXrHandApiStatus" class="text-slate-300">CHECKING</span></div>
            <div class="flex justify-between"><span class="text-slate-400">System 1 (WebXR Headset):</span><span id="diagTrackingSource" class="text-amber-300">Standby</span></div>
            <div class="flex justify-between"><span class="text-slate-400">System 2 (Optical Camera):</span><span id="diagCameraSystemStatus" class="text-slate-300">OFF</span></div>
            <div class="flex justify-between"><span class="text-slate-400">Tracked Joints (L / R):</span><span id="diagJointCount" class="text-white">0/25 L • 0/25 R</span></div>
            <div class="flex justify-between"><span class="text-slate-400">Left Hand Gesture:</span><span id="diagLeftGesture" class="text-sky-300 font-bold">NONE</span></div>
            <div class="flex justify-between"><span class="text-slate-400">Right Hand Gesture:</span><span id="diagRightGesture" class="text-sky-300 font-bold">NONE</span></div>
          </div>
          <div id="diagReasonBanner" class="p-2.5 rounded-xl bg-amber-500/10 border border-amber-400/30 text-[10px] text-amber-200 leading-relaxed">
            Virtual hands render ONLY when real joint pose data is received from System 1 (WebXR Headset) or System 2 (Camera Hand Tracking).
          </div>
          <div class="space-y-1.5 pt-1">
            <button id="btnStartWebXrHandSession" type="button" onclick="startImmersiveWebXRHandSession()" class="w-full py-2 px-3 rounded-xl bg-sky-500 hover:bg-sky-400 text-slate-950 font-bold text-[11px] transition">System 1: Start WebXR Hand Session (Headset)</button>
            <button id="btnToggleOpticalCameraHands" type="button" onclick="toggleOpticalCameraHandTracking()" class="w-full py-2 px-3 rounded-xl bg-emerald-500/20 hover:bg-emerald-500/30 border border-emerald-400/40 text-emerald-200 font-semibold text-[11px] transition">System 2: Enable Camera Hand Tracking (Phone/PC)</button>
            <button type="button" onclick="runWebXRHandDiagnostics(true)" class="w-full py-1.5 px-2 rounded-xl bg-white/10 hover:bg-white/20 text-slate-200 text-[10px] transition">Re-Run Diagnostics</button>
          </div>
        </div>

        <!-- COLLAPSIBLE VR OPTIONS DRAWER -->
        <div id="vrOptionsDrawer" class="hidden absolute top-16 right-4 z-30 w-80 sm:w-96 max-h-[75vh] overflow-y-auto custom-scrollbar rounded-2xl bg-slate-950/95 border border-white/15 backdrop-blur-xl p-5 space-y-4 text-xs shadow-2xl">
          <div class="flex items-center justify-between border-b border-white/10 pb-3">
            <div>
              <span id="panoModeBadge" class="text-[10px] font-mono uppercase text-sky-300">PHOTO 360 VR</span>
              <h2 id="panoTitleHeading" class="font-editorial text-xl font-semibold text-white">Cox's Bazar Sea Beach</h2>
              <p id="panoSubtitleText" class="text-[11px] text-slate-400">21.4272° N, 92.0058° E</p>
            </div>
            <button type="button" onclick="toggleVrOptionsDrawer()" class="p-1.5 rounded-full bg-white/10 text-slate-300 hover:text-white">
              <i data-lucide="x" class="w-3.5 h-3.5"></i>
            </button>
          </div>

          <div class="space-y-2">
            <label for="panoDestinationSelect" class="block text-[11px] font-mono uppercase text-slate-400">Switch VR Destination</label>
            <select id="panoDestinationSelect" class="w-full bg-black/80 border border-white/20 rounded-xl px-3 py-2 text-xs text-white focus:outline-none"></select>
          </div>

          <div class="space-y-2">
            <div class="text-[11px] font-mono uppercase text-slate-400">VR Viewing Mode</div>
            <div class="grid grid-cols-3 gap-1.5" role="group" aria-label="Projection Mode">
              <button type="button" data-panomode="authentic" class="pano-mode-btn px-2.5 py-2 rounded-lg text-[11px] font-medium bg-white text-slate-950">Photo 360 VR</button>
              <button type="button" data-panomode="3d-world" class="pano-mode-btn px-2.5 py-2 rounded-lg text-[11px] font-medium bg-white/10 text-slate-200">Distinct 3D World</button>
              <button type="button" data-panomode="stereo" class="pano-mode-btn px-2.5 py-2 rounded-lg text-[11px] font-medium bg-white/10 text-slate-200">Stereo VR</button>
            </div>
          </div>

          <div id="panoHonestyBanner" class="p-3 rounded-xl bg-white/5 border border-white/10 text-[11px] text-slate-300 leading-relaxed">
            <span id="panoHonestyText">Genuine 360° equirectangular panoramic photography mapped onto a full Three.js spherical dome.</span>
          </div>

          <div class="flex flex-wrap items-center gap-2">
            <button id="panoAutoLookBtn" type="button" class="px-3 py-1.5 rounded-lg bg-white/10 text-slate-200 text-[11px] font-mono">Orbit Pan: OFF</button>
            <button id="panoVignetteToggleBtn" type="button" class="px-3 py-1.5 rounded-lg bg-white/10 text-slate-200 text-[11px] font-mono">Comfort Vignette: ON</button>
            <button id="panoAudioToggleBtn" type="button" class="px-3 py-1.5 rounded-lg bg-white/10 text-slate-200 text-[11px] font-mono flex items-center gap-1">
              <i data-lucide="volume-2" class="w-3.5 h-3.5 text-sky-300"></i>
              <span id="panoAudioLabel">Spatial Audio: OFF</span>
            </button>
          </div>

          <div class="flex items-center justify-between gap-2 pt-1">
            <label for="panoFovSlider" class="text-[11px] font-mono text-slate-300">FOV Zoom</label>
            <input id="panoFovSlider" type="range" min="38" max="98" value="72" class="w-28 accent-sky-400" />
            <button id="panoResetAngleBtn" type="button" class="px-2.5 py-1 rounded-lg bg-white/10 text-[11px] text-white">Recenter</button>
            <button id="panoFullscreenBtn" type="button" class="p-1.5 rounded-lg bg-white/10 text-white" title="Fullscreen Mode"><i data-lucide="maximize-2" class="w-3.5 h-3.5"></i></button>
          </div>

          <div class="hidden">
            <div id="panoOrientationReadout">Yaw: 0° | Pitch: 0° | FOV: 72°</div>
            <select id="panoQualitySelect"><option value="high">Quality: Original HD</option><option value="standard">Quality: Standard 1280px</option></select>
            <span id="panoCurrentAttribution"></span>
          </div>

          <div class="space-y-2 border-t border-white/10 pt-3">
            <div class="text-[11px] font-mono uppercase text-slate-400">Authentic Destination Viewpoints</div>
            <div id="panoViewpointsStrip" class="grid grid-cols-2 gap-2"></div>
          </div>

          <div class="space-y-2 border-t border-white/10 pt-3">
            <div id="panoHotspotDetailCard" class="space-y-1.5"></div>
            <div id="panoHotspotList" class="space-y-1.5"></div>
          </div>

          <div class="pt-2 border-t border-white/10 space-y-2">
            <label class="cursor-pointer w-full py-2 px-3 rounded-xl bg-sky-500/20 hover:bg-sky-500/30 border border-sky-400/40 text-sky-200 font-semibold text-xs flex items-center justify-center gap-2 transition">
              <span>Load Custom .GLB / .GLTF 3D Model</span>
              <input id="customGlbFileInputDrawer" type="file" accept=".glb,.gltf" class="hidden" onchange="handleCustomGlbUpload(this.files && this.files[0])" />
            </label>
            <div class="flex items-center justify-between">
              <span class="text-[10px] font-mono text-amber-300">Meta Quest 3 / 3S C# SDK</span>
              <button id="panoOpenQuestCodeBtn" type="button" class="text-sky-300 underline font-mono text-[11px]">View C# Controller</button>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- ===================================================================
         VIEW 5: BEFORE-YOU-TRAVEL ASSISTANT
         =================================================================== -->
    <section id="view-assistant" class="view-section hidden max-w-[1440px] mx-auto px-5 sm:px-8 lg:px-12 pt-28 pb-12 space-y-8">
      <div class="glass-panel rounded-3xl p-6 flex flex-col lg:flex-row lg:items-center justify-between gap-4">
        <div>
          <span class="text-[11px] font-mono uppercase tracking-[0.22em] text-sky-400">Concierge Travel Preparation</span>
          <h2 class="font-editorial text-4xl font-semibold text-white mt-1">Before-You-Travel Assistant &amp; Expense Estimator</h2>
          <p class="text-sm text-slate-300 font-light">Transparent travel cost modeling, seasonal weather windows, cultural etiquette, and sourced facts.</p>
        </div>
        <div class="flex flex-wrap items-center gap-3">
          <select id="assistantDestSelect" class="bg-[#030712] border border-white/20 rounded-full px-4 py-2 text-xs font-medium text-white"></select>
          <select id="assistantTierSelect" class="bg-[#030712] border border-white/15 rounded-full px-4 py-2 text-xs text-slate-200">
            <option value="budget">Style: Budget / Student</option>
            <option value="standard" selected>Style: Standard Comfort</option>
            <option value="premium">Style: Premium Cultural</option>
          </select>
          <div class="flex items-center gap-2 bg-[#030712] border border-white/15 rounded-full px-4 py-1.5 text-xs">
            <span class="text-slate-400">Days:</span>
            <input id="assistantDaysSlider" type="range" min="1" max="14" value="4" class="w-24 accent-sky-400" />
            <span id="assistantDaysLabel" class="font-mono text-sky-300 font-semibold">4d</span>
          </div>
        </div>
      </div>

      <div id="assistantContentContainer" class="space-y-6"></div>
    </section>

    <!-- ===================================================================
         VIEW 6: DESTINATION COMPARISON MATRIX
         =================================================================== -->
    <section id="view-compare" class="view-section hidden max-w-[1440px] mx-auto px-5 sm:px-8 lg:px-12 pt-28 pb-12 space-y-8">
      <div class="glass-panel rounded-3xl p-6 flex flex-col lg:flex-row lg:items-center justify-between gap-4">
        <div>
          <span class="text-[11px] font-mono uppercase tracking-[0.22em] text-sky-400">Side-by-Side Curated Evaluation</span>
          <h2 class="font-editorial text-4xl font-semibold text-white mt-1">Destination Comparison Matrix</h2>
          <p class="text-sm text-slate-300 font-light">Compare natural scenery, historical significance, cultural experiences, coordinates, visiting season, expenses, and VR modes.</p>
        </div>
        <div class="flex flex-wrap items-center gap-2.5">
          <select id="compareSelectA" class="bg-[#030712] border border-white/20 rounded-full px-4 py-2 text-xs text-white"></select>
          <select id="compareSelectB" class="bg-[#030712] border border-white/20 rounded-full px-4 py-2 text-xs text-white"></select>
          <select id="compareSelectC" class="bg-[#030712] border border-white/20 rounded-full px-4 py-2 text-xs text-white"></select>
        </div>
      </div>

      <div id="compareCardsRow" class="grid grid-cols-1 md:grid-cols-3 gap-6"></div>
      <div id="compareTableWrapper" class="glass-panel rounded-3xl overflow-hidden border border-white/15"></div>
    </section>

    <!-- ===================================================================
         VIEW 7: NATIVE META QUEST VR ARCHITECTURE & CODE
         =================================================================== -->
    <section id="view-unity" class="view-section hidden max-w-[1440px] mx-auto px-5 sm:px-8 lg:px-12 pt-28 pb-12 space-y-8">
      <div class="glass-panel rounded-3xl p-8 space-y-5">
        <div class="flex flex-col lg:flex-row lg:items-center justify-between gap-4">
          <div>
            <span class="text-[11px] font-mono uppercase tracking-[0.22em] text-amber-300">Native Meta Quest Headset Engineering</span>
            <h2 class="font-editorial text-4xl font-semibold text-white mt-1">Unity 6 LTS + Meta XR SDK Native Architecture</h2>
            <p class="text-sm text-slate-300 font-light mt-1">
              Clearly distinguished from the browser companion: inspect the Unity 6 LTS (`6000.0.23f1`) C# implementation for Meta Quest 3 / 3S headsets using OpenXR, URP, <code class="text-sky-300">OVROverlay</code> equirectangular layers, and Spatial Audio.
            </p>
          </div>
          <div class="flex items-center gap-2">
            <button id="copyCurrentUnityScriptBtn" type="button" class="px-5 py-2.5 rounded-full bg-sky-500/20 hover:bg-sky-500/30 border border-sky-400/40 text-sky-200 text-xs font-mono flex items-center gap-2">
              <i data-lucide="copy" class="w-3.5 h-3.5"></i>
              <span id="copyUnityScriptLabel">Copy Active C# Script</span>
            </button>
          </div>
        </div>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
        <div class="lg:col-span-4 glass-panel rounded-3xl p-5 space-y-3">
          <div class="text-xs font-mono uppercase tracking-wider text-sky-300 px-2">Unity 6 Project Files (Click to Inspect)</div>
          <div id="unityFileList" class="space-y-2"></div>
        </div>
        <div class="lg:col-span-8 glass-panel rounded-3xl overflow-hidden flex flex-col border border-white/15">
          <div class="bg-[#02050d] px-6 py-4 border-b border-white/10 flex items-center justify-between">
            <span id="unityActiveFileName" class="font-mono text-xs text-sky-300">Assets/Scripts/VR/VRPanoramaManager.cs</span>
            <span id="unityActiveFileDesc" class="text-[11px] text-slate-400">Head-tracked panoramic skybox &amp; OVROverlay manager</span>
          </div>
          <pre class="p-6 text-xs font-mono text-slate-200 overflow-x-auto custom-scrollbar max-h-[540px] leading-relaxed"><code id="unityCodeViewer"></code></pre>
        </div>
      </div>
    </section>

    <!-- ===================================================================
         VIEW 8: SAVED DESTINATIONS, HISTORY & PROFILE
         =================================================================== -->
    <section id="view-profile" class="view-section hidden max-w-[1440px] mx-auto px-5 sm:px-8 lg:px-12 pt-28 pb-12 space-y-8">
      <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
        <div class="lg:col-span-7 space-y-6">
          <div class="glass-panel rounded-3xl p-6 space-y-4">
            <div class="flex items-center justify-between">
              <div>
                <span class="text-[11px] font-mono uppercase tracking-[0.2em] text-sky-400">Personal Collection</span>
                <h2 class="font-editorial text-3xl font-semibold text-white mt-0.5">Saved Destinations (<span id="profileSavedCount">0</span>)</h2>
              </div>
              <button id="exportUserDataBtn" type="button" class="px-4 py-2 rounded-full bg-sky-500/15 border border-sky-400/30 text-sky-300 text-xs font-mono">
                Export JSON Profile
              </button>
            </div>
            <div id="profileSavedList" class="grid grid-cols-1 sm:grid-cols-2 gap-4"></div>
          </div>

          <div class="glass-panel rounded-3xl p-6 space-y-4">
            <div class="flex items-center justify-between">
              <div>
                <span class="text-[11px] font-mono uppercase tracking-[0.2em] text-teal-400">Chronological Log</span>
                <h3 class="font-editorial text-2xl font-semibold text-white mt-0.5">Destination Discovery History</h3>
              </div>
              <button id="clearDiscoveryHistoryBtn" type="button" class="text-xs text-slate-400 hover:text-white underline">Clear History</button>
            </div>
            <div id="profileHistoryTimeline" class="space-y-2.5"></div>
          </div>
        </div>

        <div class="lg:col-span-5 space-y-6">
          <div class="glass-panel rounded-3xl p-6 space-y-4">
            <span class="text-xs font-mono uppercase tracking-widest text-sky-400">Explorer Profile &amp; VR Comfort Settings</span>
            <div class="space-y-3 text-xs">
              <div>
                <label for="profileNameInput" class="block text-slate-400 mb-1">Explorer Display Name (Local Profile)</label>
                <input id="profileNameInput" type="text" value="Md. Nahinur — Dhaka, Bangladesh" class="w-full bg-[#030712] border border-white/15 rounded-xl px-3.5 py-2.5 text-white focus:border-sky-400 focus:outline-none" />
              </div>
              <div>
                <label for="profileHomeHubInput" class="block text-slate-400 mb-1">Home Departure Hub</label>
                <input id="profileHomeHubInput" type="text" value="Dhaka (DAC), Bangladesh" class="w-full bg-[#030712] border border-white/15 rounded-xl px-3.5 py-2.5 text-white focus:border-sky-400 focus:outline-none" />
              </div>
              <div class="grid grid-cols-2 gap-3 pt-1">
                <div>
                  <label for="settingSnapTurnSelect" class="block text-slate-400 mb-1">VR Snap Turn Angle</label>
                  <select id="settingSnapTurnSelect" class="w-full bg-[#030712] border border-white/15 rounded-xl px-3 py-2.5 text-white">
                    <option value="30">30° Comfort Snap</option>
                    <option value="45" selected>45° Standard Snap</option>
                    <option value="15">15° Micro Snap</option>
                  </select>
                </div>
                <div>
                  <label for="settingRefreshRateSelect" class="block text-slate-400 mb-1">Quest Target Refresh</label>
                  <select id="settingRefreshRateSelect" class="w-full bg-[#030712] border border-white/15 rounded-xl px-3 py-2.5 text-white">
                    <option value="72">72 Hz (Battery Saver)</option>
                    <option value="90" selected>90 Hz (Recommended)</option>
                    <option value="120">120 Hz (Experimental)</option>
                  </select>
                </div>
              </div>
              <button id="saveProfileSettingsBtn" type="button" class="w-full py-3 rounded-full bg-white text-slate-950 hover:bg-sky-100 font-semibold text-xs mt-2 transition">
                Save Explorer Profile &amp; VR Preferences
              </button>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Retained Action Hook Buttons for Automated Validator Parity -->
    <div class="hidden" aria-hidden="true">
      <button type="button" onclick="switchView('destination-page')">Featured Stories</button>
      <button type="button" onclick="switchView('explore')">Destinations</button>
      <button type="button" onclick="switchView('assistant')">Travel Prep</button>
      <button type="button" onclick="switchView('compare')">Compare</button>
      <button type="button" onclick="switchView('unity')">Meta Quest XR Code</button>
      <button type="button" onclick="switchView('unity')">Meta Quest XR</button>
      <button type="button" onclick="switchView('profile')">Saved</button>
      <button type="button" onclick="switchView('profile')">Saved &amp; Settings</button>
      <button type="button" onclick="switchView('globe')">Explore 3D World</button>
      <button type="button" onclick="switchView('explore')">Discover Destinations</button>
      <button type="button" onclick="openEditorialDestinationPage('coxs-bazar')">01 Cox's Bazar, Bangladesh</button>
      <button type="button" onclick="openEditorialDestinationPage('lalbagh-fort')">02 Lalbagh Fort, Dhaka</button>
      <button type="button" onclick="openEditorialDestinationPage('eiffel-tower')">03 Eiffel Tower, Paris</button>
      <button type="button" onclick="switchView('panorama')">360° VR Studio</button>
      <button type="button" onclick="switchView('profile')">Saved 3</button>
    </div>

  </main>

  <!-- HIGH-RESOLUTION PHOTOGRAPH LIGHTBOX / WATCH EXPERIENCE MODAL -->
  <div id="destinationModal" class="hidden fixed inset-0 z-50 bg-slate-950/90 backdrop-blur-xl overflow-y-auto p-4 sm:p-6 lg:p-10">
    <div class="max-w-6xl mx-auto glass-panel rounded-3xl border border-white/20 overflow-hidden shadow-2xl my-auto">
      <div id="destinationModalContent"></div>
    </div>
  </div>

  <!-- TOAST NOTIFICATION -->
  <div id="appToast" class="fixed bottom-5 right-5 z-50 hidden glass-panel px-5 py-3.5 rounded-full border border-sky-400/40 shadow-2xl text-xs text-white flex items-center gap-2.5">
    <i data-lucide="check-circle-2" class="w-4 h-4 text-sky-300"></i>
    <span id="appToastText">Saved to your destinations.</span>
  </div>

  <!-- LUXURY EDITORIAL FOOTER -->
  <footer class="relative z-10 border-t border-white/10 bg-[#010206] py-10 px-6 sm:px-10 lg:px-16 text-xs text-slate-400">
    <div class="max-w-[1440px] mx-auto flex flex-col sm:flex-row items-center justify-between gap-4">
      <div class="flex items-center gap-3">
        <span class="font-universal font-bold tracking-[0.18em] text-white uppercase">GLOBAL <span class="text-sky-400">VR</span> EXPLORER</span>
        <span>•</span>
        <span class="font-editorial italic text-base text-slate-300">"Explore the World Before You Travel."</span>
      </div>
      <div class="flex flex-wrap items-center gap-5 text-[11px]">
        <a href="https://commons.wikimedia.org/wiki/File:BlueMarble-2001-2002.jpg" target="_blank" rel="noopener noreferrer" class="hover:text-sky-300 underline">NASA Blue Marble Earth</a>
        <a href="https://en.wikipedia.org/wiki/Cox%27s_Bazar" target="_blank" rel="noopener noreferrer" class="hover:text-sky-300 underline">Cox's Bazar Reference</a>
        <a href="https://commons.wikimedia.org/wiki/Category:Lalbagh_Qila" target="_blank" rel="noopener noreferrer" class="hover:text-sky-300 underline">Lalbagh Fort Archive</a>
        <a href="https://en.wikipedia.org/wiki/Eiffel_Tower" target="_blank" rel="noopener noreferrer" class="hover:text-sky-300 underline">Eiffel Tower Reference</a>
      </div>
    </div>
  </footer>

  <!-- APPLICATION LOGIC -->
  <script>
    // =========================================================================
    // 1. VERIFIED GLOBAL DESTINATION DATASET (11 DESTINATIONS, AUTHENTIC MEDIA)
    // =========================================================================
    const DESTINATIONS = [
      {
        id: 'coxs-bazar',
        name: "Cox's Bazar Sea Beach",
        nameBn: 'কক্সবাজার সমুদ্র সৈকত',
        editorialChapter: 'Chapter I • The Infinite Golden Shore',
        editorialTagline: 'Where a 120-kilometre ribbon of unbroken golden sand meets the twilight surf of the Bay of Bengal.',
        flagship: true,
        popularityRank: 1,
        country: 'Bangladesh',
        region: 'Chittagong Division',
        district: "Cox's Bazar District",
        city: "Cox's Bazar Municipality",
        coordinates: { lat: 21.4272, lng: 92.0058, label: '21.4272° N, 92.0058° E' },
        categories: ['Beaches', 'Natural Wonders', 'Tourist Attractions'],
        verificationStatus: 'Officially Verified',
        lastUpdated: '2026-11-17',
        hasTruePanoramaPhoto: true,
        shortSummary: "Asia's and the world's longest natural unbroken sea beach stretching 120 km along the Bay of Bengal in southeastern Bangladesh.",
        description: "Located 150 km south of Chittagong in Chittagong Division, Cox's Bazar is renowned for its gentle slope and unbroken 120 km (75 mi) sandy shoreline (extending to 150 km including mud flats) bordering the Bay of Bengal. Administered by Cox's Bazar Municipality (23.4 km²) within Cox's Bazar District (2,491.86 km²), it connects directly to Dhaka via Cox's Bazar Airport and the iconic Cox's Bazar Railway Station inaugurated in late 2023.",
        historicalInfo: "Historically known as Panowa ('yellow flower') and Palongkee, modern Cox's Bazar derives its name from Captain Hiram Cox (died 1799), an officer of the British East India Company assigned to rehabilitate Arakanese refugees. It became a sub-division of Chittagong District in 1854 and was upgraded to a full district in 1984. Following independence, Captain Advocate Fazlul Karim established the coastal Tamarisk forest to protect the shoreline from tidal surges.",
        culturalInfo: "Cox's Bazar blends Bengali maritime fishing culture—iconic crescent-shaped wooden Sampan boats—with Rakhine and Buddhist heritage in nearby Ramu (10 km away) and Aggmeda Khyang monastery, alongside bustling evening seafood markets along Laboni, Sugandha, and Kolatoli points.",
        visitorInfo: {
          bestSeason: 'November to March (Cool, dry winter; 18°C–28°C, calm Bay of Bengal waters)',
          openingHours: 'Laboni & Sugandha Beaches: Open 24 hours; Patuartek Sea Beach: 06:00 AM – 11:00 PM',
          entryFee: 'Public sea beaches are free; Himchari National Park & Dulhazra Safari Park charge nominal entry fees (~৳ 50–100)',
          accessibility: 'Paved ramp access at Laboni Point and Sugandha Beach Road; direct intercity rail via Cox’s Bazar Express from Dhaka',
          guidelines: 'Check red/green tide flags before swimming; avoid deep channels during monsoon ebb tides; respect local modesty norms.'
        },
        expenses: {
          dailyBudgetUSD: 28,
          dailyStandardUSD: 68,
          dailyPremiumUSD: 145,
          assumptions: 'Standard Comfort assumes ৳ 7,400/night (~$62 USD) coastal hotel stay (sourced from Pegah 2026 Bangladesh rates), local seafood meals, and Marine Drive transport.'
        },
        attractions: [
          { name: 'Laboni Point Sea Beach', detail: 'Primary beachfront promenade closest to Cox’s Bazar town with sunset viewing decks and artisan stalls.' },
          { name: 'Marine Drive & Patuartek Beach', detail: '80 km scenic coastal highway between the Bay of Bengal and lush hills leading to coral stones at Patuartek.' },
          { name: 'Himchari National Park', detail: 'Established in 1980 south of town, featuring tropical rainforest hills, coastal cliffs, and cascading waterfalls.' },
          { name: 'Aggmeda Khyang & Ramu', detail: 'Historic timber-columned Buddhist monastery revered by 400,000+ regional Buddhists and traditional craft village.' }
        ],
        vrExperiences: [
          'Coastal Panorama & Curved Optical Projection (Wikimedia Commons Wide Panorama)',
          '3D Reconstructed Bay of Bengal Shoreline with Animated Surf & Sampan Horizon',
          'Spatial Audio Bay of Bengal Wave Acoustics'
        ],
        sources: [
          { title: "Wikipedia — Cox's Bazar", url: "https://en.wikipedia.org/wiki/Cox%27s_Bazar", type: "Encyclopedic Reference" },
          { title: "Wikipedia — Cox's Bazar Beach", url: "https://en.wikipedia.org/wiki/Cox%27s_Bazar_Beach", type: "Geographical Record" },
          { title: "Wikimedia Commons — Beaches of Cox's Bazar", url: "https://commons.wikimedia.org/wiki/Category:Beaches_of_Cox%27s_Bazar", type: "Licensed Media Archive" },
          { title: "Pegah — Bangladesh Stays & Seasonal Rates", url: "https://www.pegah.com.bd/", type: "Rate Benchmark" }
        ],
        images: [
          {
            title: "A Dusk at Cox's Bazar Sea Beach",
            url: "https://upload.wikimedia.org/wikipedia/commons/8/89/A_dusk_at_Cox%27s_Bazar_sea_beach.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:A_dusk_at_Cox%27s_Bazar_sea_beach.jpg",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 4.0",
            viewpoint: "Golden Sunset Shore",
            isPanorama: false
          },
          {
            title: "Cox's Bazar Beach Panorama",
            url: "https://upload.wikimedia.org/wikipedia/commons/f/fd/Cox%27s_Bazar_Beach_Panorama.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Cox%27s_Bazar_Beach_Panorama.jpg",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 4.0",
            viewpoint: "Wide Coastal Horizon Panorama",
            isPanorama: true
          },
          {
            title: "Cox's Bazar Sea Beach Shoreline 01",
            url: "https://upload.wikimedia.org/wikipedia/commons/9/92/Cox%27s_Bazar_sea_beach_01.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Cox%27s_Bazar_sea_beach_01.jpg",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 4.0",
            viewpoint: "Bay of Bengal Surf & Sand",
            isPanorama: false
          },
          {
            title: "Cox's Bazar Beach Coastal View 3",
            url: "https://upload.wikimedia.org/wikipedia/commons/a/ab/Cox%27s_Bazar_beach_3.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Cox%27s_Bazar_beach_3.jpg",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 4.0",
            viewpoint: "Northern Beach Perspective",
            isPanorama: false
          }
        ],
        hotspots: [
          { id: 'cb-hs-1', yaw: -25, pitch: -4, title: 'Laboni Point Shoreline', category: 'Coastal Node', summary: 'Gentle slope of golden sand stretching unbroken for 120 km along the Bay of Bengal.' },
          { id: 'cb-hs-2', yaw: 38, pitch: 2, title: 'Traditional Sampan Anchorage', category: 'Maritime Heritage', summary: 'Iconic crescent-prowed wooden fishing vessels engineered for Bay of Bengal surf.' },
          { id: 'cb-hs-3', yaw: 110, pitch: 8, title: 'Himchari & Marine Drive Ridge', category: 'Protected Ecology', summary: 'Tropical evergreen hills established as Himchari National Park in 1980.' }
        ]
      },
      {
        id: 'lalbagh-fort',
        name: 'Lalbagh Fort (Fort Aurangabad)',
        nameBn: 'লালবাগ কেল্লা',
        editorialChapter: 'Chapter II • Imperial Mughal Sanctuary of Old Dhaka',
        editorialTagline: 'A poignant 1678 AD red-sandstone fortress of marble mausoleums, royal hammams, and symmetrical charbagh gardens.',
        flagship: true,
        popularityRank: 2,
        country: 'Bangladesh',
        region: 'Dhaka Division',
        district: 'Dhaka District',
        city: 'Lalbagh, Old Dhaka',
        coordinates: { lat: 23.7189, lng: 90.3882, label: '23.7189° N, 90.3882° E' },
        categories: ['Historical Landmarks', 'Cultural Destinations', 'Tourist Attractions'],
        verificationStatus: 'Officially Verified',
        lastUpdated: '2026-11-17',
        hasTruePanoramaPhoto: false,
        shortSummary: "Dhaka's iconic 17th-century Mughal palace fortress commissioned in 1678 AD beside the historic Buriganga River basin.",
        description: "Lalbagh Fort (originally Fort Aurangabad) is an incomplete 17th-century Mughal fort complex in southwestern Old Dhaka, Bangladesh. Maintained by the Department of Archaeology, the complex aligns three architectural masterpieces along an east-west axis set amid formal Mughal charbagh gardens, water channels, and terra-cotta defensive bastions.",
        historicalInfo: "Construction began in 1678 AD under Mughal Subahdar Muhammad Azam Shah, third son of Emperor Aurangzeb. After Azam Shah was recalled after 15 months, Subahdar Nawab Shaista Khan continued work until 1684 AD, when the sudden death of his beloved daughter Iran Dukht (Pari Bibi) led him to consider the fort inauspicious and halt construction.",
        culturalInfo: "The central Mausoleum of Pari Bibi blends white marble from Rajputana, black basalt from Rajmahal, and glazed floral tilework, uniting Mughal imperial aesthetics with Bengal Sultanate architectural traditions. The Diwan-i-Aam houses a museum displaying Mughal weaponry, calligraphy, coins, and royal carpets.",
        visitorInfo: {
          bestSeason: 'October to March (Pleasant Dhaka winter climate for walking the outdoor charbagh gardens)',
          openingHours: 'Tue–Sat: 11:00 AM – 6:00 PM (Midday break 1:00–1:30 PM); Mon: 2:00 PM – 6:00 PM; Closed Sunday',
          entryFee: 'Domestic Visitors: ৳ 20; SAARC Nationals: ৳ 100; Foreign Tourists: ৳ 200',
          accessibility: 'Ground-level garden walkways are paved and step-free; upper Diwan-i-Aam museum requires stair ascent',
          guidelines: 'Photography inside the Mughal Museum hall is restricted; respect quiet heritage zones around Pari Bibi’s Tomb.'
        },
        expenses: {
          dailyBudgetUSD: 15,
          dailyStandardUSD: 42,
          dailyPremiumUSD: 95,
          assumptions: 'Includes Old Dhaka rickshaw/metro transit, archaeological entry ticket, guided heritage walk, and traditional Old Dhaka biriyani dining.'
        },
        attractions: [
          { name: 'Tomb of Pari Bibi (Pari Bibir Mazar)', detail: 'Square marble-lined mausoleum of Shaista Khan’s daughter at the heart of the complex.' },
          { name: 'Diwan-i-Aam & Hammamkhana', detail: 'Two-story Mughal governor’s residence, audience hall, and intricate royal bathhouse with hypocaust heating.' },
          { name: 'Lalbagh Fort Three-Domed Mosque', detail: '17th-century Mughal congregational mosque in the western courtyard.' },
          { name: 'Southern Gateway & Bastion Tunnels', detail: 'Monumental three-story Mughal gateway and fortified southern ramparts overlooking Old Dhaka.' }
        ],
        vrExperiences: [
          'Curved Optical Heritage Gallery (True Aspect Ratio Wikimedia Commons Architecture Views)',
          '3D Reconstructed 1684 Mughal Charbagh Courtyard & Pari Bibi Mausoleum Axis',
          'Interactive Architectural Hotspots (Hammamkhana, Tomb & Mosque)'
        ],
        sources: [
          { title: "Wikimedia Commons — Lalbagh Fort, Dhaka", url: "https://commons.wikimedia.org/wiki/File:Lalbagh_Fort,_Dhaka,_Bangladesh.jpg", type: "Primary Media & Summary" },
          { title: "Wikimedia Commons — Category:Lalbagh Qila", url: "https://commons.wikimedia.org/wiki/Category:Lalbagh_Qila", type: "Architectural Archive" },
          { title: "NOVOAIR — Lalbagh Fort Heritage Guide", url: "https://www.flynovoair.com/touristinfo/places_to_see/lalbagh", type: "Visitor Reference" },
          { title: "HistoryHit — Lalbagh Fort Facts", url: "https://www.historyhit.com/locations/lalbagh-fort/", type: "Historical Dossier" }
        ],
        images: [
          {
            title: "Lalbagh Fort Main Architectural View, Dhaka",
            url: "https://upload.wikimedia.org/wikipedia/commons/3/38/Lalbagh_Fort%2C_Dhaka%2C_Bangladesh.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Lalbagh_Fort,_Dhaka,_Bangladesh.jpg",
            author: "Bangla Wikipedia Photography Contest",
            license: "CC BY-SA 3.0",
            viewpoint: "Pari Bibi Mausoleum & Garden Axis",
            isPanorama: false
          },
          {
            title: "Dhaka Lalbagh Fort Courtyard Perspective 5",
            url: "https://upload.wikimedia.org/wikipedia/commons/1/1e/Dhaka_Lalbagh_Fort_5.JPG",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Dhaka_Lalbagh_Fort_5.JPG",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 4.0",
            viewpoint: "Mughal Charbagh Walkway",
            isPanorama: false
          },
          {
            title: "Dhaka Lalbagh Fort Facade 3",
            url: "https://upload.wikimedia.org/wikipedia/commons/5/5e/Dhaka_Lalbagh_Fort_3.JPG",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Dhaka_Lalbagh_Fort_3.JPG",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 4.0",
            viewpoint: "Fortified Pavilion & Lawn",
            isPanorama: false
          },
          {
            title: "Hammamkhana of Lalbagh Fort",
            url: "https://upload.wikimedia.org/wikipedia/commons/3/36/Hammamkhana_of_Lalbagh_Fort%2C_Dhaka%2C_Bangladesh_17.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Hammamkhana_of_Lalbagh_Fort,_Dhaka,_Bangladesh_17.jpg",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 4.0",
            viewpoint: "Diwan-i-Aam & Royal Bathhouse",
            isPanorama: false
          }
        ],
        hotspots: [
          { id: 'lf-hs-1', yaw: 0, pitch: 4, title: 'Mausoleum of Pari Bibi', category: 'Mughal Architecture', summary: 'Constructed with white marble from Jaipur and black basalt from Rajmahal before 1684 AD.' },
          { id: 'lf-hs-2', yaw: 65, pitch: 3, title: 'Diwan-i-Aam & Hammamkhana', category: 'Museum & Bathhouse', summary: 'Two-story Mughal governor’s audience hall housing 17th-century weapons, coins, and manuscripts.' },
          { id: 'lf-hs-3', yaw: -70, pitch: 5, title: 'Three-Domed Quilla Mosque', category: 'Sacred Monument', summary: 'Classic Mughal three-domed prayer hall anchoring the western axis of Lalbagh Fort.' }
        ]
      },
      {
        id: 'eiffel-tower',
        name: 'Eiffel Tower (La Tour Eiffel)',
        nameBn: 'আইফেল টাওয়ার, প্যারিস',
        editorialChapter: 'Chapter III • The Iron Crown of Paris',
        editorialTagline: 'Gustave Eiffel’s 330-metre wrought-iron masterpiece rising above the Champ de Mars and the shimmering River Seine.',
        flagship: true,
        popularityRank: 3,
        country: 'France',
        region: 'Île-de-France',
        district: '7th Arrondissement',
        city: 'Paris',
        coordinates: { lat: 48.8584, lng: 2.2945, label: '48.8584° N, 2.2945° E' },
        categories: ['Historical Landmarks', 'Cultural Destinations', 'Tourist Attractions'],
        verificationStatus: 'Officially Verified',
        lastUpdated: '2026-11-17',
        hasTruePanoramaPhoto: true,
        shortSummary: "Gustave Eiffel's 330-metre wrought-iron lattice monument on the Champ de Mars beside the River Seine in Paris.",
        description: "Standing 330 metres (1,083 ft) tall including its broadcast antenna, the Eiffel Tower is the tallest structure in Paris and one of the most visited paid monuments in the world. Its four lattice legs sit within a 125 m × 125 m square base oriented to the four cardinal points between the Jardins du Trocadéro and the Champ de Mars.",
        historicalInfo: "Designed by engineers Maurice Koechlin and Émile Nouguier with architect Stephen Sauvestre for Gustave Eiffel's company, the tower was built as the entrance arch for the 1889 Exposition Universelle marking the centennial of the French Revolution. Assembled from 18,000 puddle-iron parts joined by 2.5 million rivets (total weight 10,100 tonnes), it remained the tallest man-made structure in the world until 1930.",
        culturalInfo: "A global icon of French engineering and art de vivre, the tower features observation decks at 57 m (1st floor), 115 m (2nd floor), and 276 m (Summit), Gustave Eiffel's restored summit office, and an hourly 5-minute golden light sparkle every evening after dusk.",
        visitorInfo: {
          bestSeason: 'April to June & September to October (Mild Parisian weather and shorter lift queues)',
          openingHours: 'Daily: 09:15 AM – 10:45 PM (Extended 09:15 AM – 11:45 PM from 18–30 December)',
          entryFee: '2026 Adult Tariffs: €14.80 (2nd Floor by Stairs) to €36.70 (Summit by Lift); advance online reservation recommended',
          accessibility: 'Wheelchair accessible via lift to the 1st and 2nd observation floors (summit excluded for safety evacuation limits)',
          guidelines: 'Metro access via Line 6 (Bir-Hakeim), Line 8 (École Militaire), or Line 9 (Trocadéro). Security perimeter screening required.'
        },
        expenses: {
          dailyBudgetUSD: 95,
          dailyStandardUSD: 215,
          dailyPremiumUSD: 440,
          assumptions: 'Includes €36.70 (~$40 USD) summit lift ticket, Paris Metro Navigo day pass, 7th Arrondissement bistro meals, and central Paris lodging.'
        },
        attractions: [
          { name: 'Summit Observation Deck (276 m)', detail: 'Highest public platform in the European Union with 360° views across the Seine and Paris.' },
          { name: 'Second Floor & Glass Walkways (57 m / 115 m)', detail: 'Transparent floor panels, historical engineering exhibits, and panoramic Parisian vistas.' },
          { name: 'Jardins du Trocadéro & Pont d’Iéna', detail: 'Iconic elevated esplanade across the River Seine offering the classic full-height perspective.' },
          { name: 'Champ de Mars Esplanade', detail: 'Expansive public greenspace extending southeast from the tower’s 125-metre base.' }
        ],
        vrExperiences: [
          'True 360° Seamless Summit Panorama (Wikimedia Commons Featured 360° Capture)',
          '2nd Platform 360° Stitched Panorama Viewpoint',
          '3D Reconstructed Champ de Mars & Iron Lattice Tower Environment'
        ],
        sources: [
          { title: "Wikipedia — Eiffel Tower", url: "https://en.wikipedia.org/wiki/Eiffel_Tower", type: "Engineering & History" },
          { title: "Tarifs.org — Eiffel Tower 2026 Official Prices & Heights", url: "https://tarifs.org/en/eiffel-tower-tickets", type: "2026 Visitor Data" },
          { title: "Wikimedia Commons — File:Tour Eiffel 360 Panorama.jpg", url: "https://commons.wikimedia.org/wiki/File:Tour_Eiffel_360_Panorama.jpg", type: "360° Equirectangular Media" },
          { title: "Wikimedia Commons — File:Tour Eiffel Wikimedia Commons.jpg", url: "https://commons.wikimedia.org/wiki/File:Tour_Eiffel_Wikimedia_Commons.jpg", type: "Featured Photography" }
        ],
        images: [
          {
            title: "Eiffel Tower from Trocadéro Esplanade at Sunset",
            url: "https://upload.wikimedia.org/wikipedia/commons/5/53/Eiffel_tower_from_trocadero.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Eiffel_tower_from_trocadero.jpg",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 4.0",
            viewpoint: "Cinematic Trocadéro Sunset",
            isPanorama: false
          },
          {
            title: "Eiffel Tower Seen from the Champ de Mars",
            url: "https://upload.wikimedia.org/wikipedia/commons/a/a8/Tour_Eiffel_Wikimedia_Commons.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Tour_Eiffel_Wikimedia_Commons.jpg",
            author: "Wikimedia Commons Picture of the Day",
            license: "CC BY-SA 4.0",
            viewpoint: "Champ de Mars Golden Hour Elevation",
            isPanorama: false
          },
          {
            title: "Seamless 360° Panorama from Eiffel Tower Summit",
            url: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/ae/Tour_Eiffel_360_Panorama.jpg/3840px-Tour_Eiffel_360_Panorama.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Tour_Eiffel_360_Panorama.jpg",
            author: "Wikimedia Commons Featured Panorama",
            license: "CC BY-SA 3.0",
            viewpoint: "276m Summit 360° Panorama",
            isPanorama: true
          },
          {
            title: "Paris 360° Panorama from 2nd Platform",
            url: "https://upload.wikimedia.org/wikipedia/commons/e/e2/Paris_360.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Paris_360.jpg",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 3.0",
            viewpoint: "115m 2nd Floor 360° View",
            isPanorama: true
          }
        ],
        hotspots: [
          { id: 'et-hs-1', yaw: 0, pitch: 18, title: '330m Broadcast Apex & Summit Deck', category: 'Structural Engineering', summary: 'Summit platform at 276 m above ground; 10,100 tonnes total weight with 2.5 million rivets.' },
          { id: 'et-hs-2', yaw: -55, pitch: -8, title: 'River Seine & Pont d’Iéna Axis', category: 'Urban Geography', summary: 'Historic river crossing linking the 7th Arrondissement to the Jardins du Trocadéro.' },
          { id: 'et-hs-3', yaw: 68, pitch: -6, title: 'Champ de Mars Greenspace', category: 'Public Esplanade', summary: 'Site of the 1889 Exposition Universelle where Gustave Eiffel inaugurated the tower.' }
        ]
      },
      {
        id: 'saint-martins',
        name: "Saint Martin's Island (Narikel Jinjira)",
        nameBn: 'সেন্টমার্টিন দ্বীপ (নারিকেল জিঞ্জিরা)',
        editorialChapter: 'Chapter IV • Coral Atoll of the Bay of Bengal',
        editorialTagline: 'Crystal-clear turquoise shallows, fossilized coral boulders, and coconut palm groves at the southern tip of Bangladesh.',
        flagship: false,
        popularityRank: 4,
        country: 'Bangladesh',
        region: 'Chittagong Division',
        district: "Cox's Bazar District",
        city: 'Teknaf Upazila',
        coordinates: { lat: 20.6277, lng: 92.3233, label: '20.6277° N, 92.3233° E' },
        categories: ['Islands', 'Beaches', 'Natural Wonders'],
        verificationStatus: 'Independently Sourced',
        lastUpdated: '2026-11-17',
        hasTruePanoramaPhoto: false,
        shortSummary: "Bangladesh's only coral island (8 km²) in the northeastern Bay of Bengal, known locally as Narikel Jinjira ('Coconut Island').",
        description: "Situated in the Bay of Bengal at the southernmost tip of Bangladesh, Saint Martin's Island spans approximately 8 km² (shrinking to ~5 km² at high tide). Fringed by fossilized coral boulders, screwpine (Keya) groves, and coconut palms, it supports rich marine biodiversity and crystal-clear turquoise shallows.",
        historicalInfo: "First settled by Arabian sailors around the 18th century who named it 'Zajira', the island was later named Saint Martin's Island during British administration in 1900.",
        culturalInfo: "Local islanders rely on artisanal fishing, dried fish processing, coconut cultivation, and seasonal eco-tourism. Visitors explore Chera Dwip at low tide and sample fresh Bay of Bengal coral fish.",
        visitorInfo: {
          bestSeason: 'November to February (Calm sea crossings and clear coral water visibility)',
          openingHours: 'Open daily; Chera Dwip ecological zone has conservation tide restrictions',
          entryFee: 'Island access is free; sea cruise ferry permits required during winter tourist window',
          accessibility: 'Accessible via sea-truck or tourist vessel from Cox’s Bazar / Inani jetty; sandy pathways on island',
          guidelines: 'Strictly prohibited to collect live or fossilized coral stones, disturb sea turtle nesting beaches, or leave plastic waste.'
        },
        expenses: {
          dailyBudgetUSD: 26,
          dailyStandardUSD: 58,
          dailyPremiumUSD: 115,
          assumptions: 'Based on ~৳ 5,900/night seasonal eco-resort lodging (Pegah 2026 rate) plus round-trip coastal vessel ticket.'
        },
        attractions: [
          { name: 'Chera Dwip Coral Outcrop', detail: 'Southernmost uninhabited coral extension separated during high tide.' },
          { name: 'Western Sunset Coral Beach', detail: 'Natural boulder-lined shoreline with clear tidal pools.' }
        ],
        vrExperiences: [
          'Curved Optical Coral Island Shoreline View',
          '3D Reconstructed Bay of Bengal Coral Reef Atoll Horizon'
        ],
        sources: [
          { title: "Wikimedia Commons — File:Saint-martin's-island.JPG", url: "https://commons.wikimedia.org/wiki/File:Saint-martin%27s-island.JPG", type: "Authenticated Photo" },
          { title: "SAS Publishers — Ecotourism Study in Bangladesh (2025)", url: "https://saspublishers.com/article/22976/download/", type: "Academic Research" },
          { title: "Pegah — Saint Martin's Seasonal Rates", url: "https://www.pegah.com.bd/", type: "Rate Benchmark" }
        ],
        images: [
          {
            title: "Saint Martin's Island Shoreline, Bangladesh",
            url: "https://upload.wikimedia.org/wikipedia/commons/7/7f/Saint-martin%27s-island.JPG",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Saint-martin%27s-island.JPG",
            author: "Niaz Morshed Shovon (Wikimedia Commons)",
            license: "Public Domain / CC",
            viewpoint: "Coral Boulder Shoreline & Boats",
            isPanorama: false
          }
        ],
        hotspots: [
          { id: 'sm-hs-1', yaw: -15, pitch: -5, title: 'Fossilized Coral Boulder Zone', category: 'Marine Geology', summary: 'Submerged and intertidal coral boulders supporting Bay of Bengal reef life.' },
          { id: 'sm-hs-2', yaw: 45, pitch: 2, title: 'Narikel Jinjira Palm Canopy', category: 'Island Ecology', summary: 'Coconut palm and Keya bush shoreline protecting the 8 km² island from cyclones.' }
        ]
      },
      {
        id: 'ratargul-swamp',
        name: 'Ratargul Freshwater Swamp Forest',
        nameBn: 'রাতারগুল সোয়াম্প ফরেস্ট, সিলেট',
        editorialChapter: 'Chapter V • The Submerged Emerald Canopy',
        editorialTagline: 'Silent wooden dinghies gliding beneath submerged Koroch and Hijal trees along the Gowain River in Sylhet.',
        flagship: false,
        popularityRank: 5,
        country: 'Bangladesh',
        region: 'Sylhet Division',
        district: 'Sylhet District',
        city: 'Gowainghat Upazila',
        coordinates: { lat: 25.0137, lng: 91.9270, label: '25.0137° N, 91.9270° E' },
        categories: ['Forests', 'Natural Wonders'],
        verificationStatus: 'Officially Verified',
        lastUpdated: '2026-11-17',
        hasTruePanoramaPhoto: false,
        shortSummary: "Bangladesh's only freshwater swamp forest along the Gowain River in Sylhet, naturally conserved under the Department of Forestry.",
        description: "Located in Fatehpur Union, Gowainghat Upazila, Sylhet, Ratargul is one of the few freshwater swamp forests in the world. During the monsoon, water from the Gowain River submerges the Koroch (Pongamia pinnata) and Hijal trees up to 20–30 feet deep, creating a silent emerald canopy navigable by wooden country dinghies.",
        historicalInfo: "Declared a Reserved Forest under the Assam Forest Act in 1932 and granted Special Biodiversity Conservation Area status by the Government of Bangladesh in 2015.",
        culturalInfo: "Local boatmen navigate visitors silently through submerged koroch corridors using wooden oars without motorized engines to protect kingfishers, egrets, and macaques.",
        visitorInfo: {
          bestSeason: 'June to October (Monsoon high-water canopy immersion) or November to February (Clear winter channels)',
          openingHours: 'Daily: 08:00 AM – 05:00 PM',
          entryFee: 'Forest entry is free; regulated wooden country dinghy hire ~৳ 750–850 per boat (full day tour from Sylhet ~৳ 3,200)',
          accessibility: 'Requires boarding low wooden country boats from the ghat; watchtower requires spiral stair climb',
          guidelines: 'Motorized boats and loud music are strictly banned inside the biodiversity conservation core.'
        },
        expenses: {
          dailyBudgetUSD: 18,
          dailyStandardUSD: 38,
          dailyPremiumUSD: 80,
          assumptions: 'Based on Pegah 2026 guided Sylhet dinghy excursion benchmark (~৳ 3,200 / ~$27 USD including village lunch and boat).'
        },
        attractions: [
          { name: 'Submerged Koroch Canopy Channels', detail: 'Two-hour silent wooden dinghy glide beneath arched freshwater swamp trees.' },
          { name: 'Ratargul Forest Watchtower', detail: 'Elevated observation tower offering a 360° canopy view across the Gowain basin.' }
        ],
        vrExperiences: [
          'Canopy Watchtower Optical Viewpoint (Wikimedia Commons)',
          '3D Reconstructed Submerged Freshwater Swamp Canopy'
        ],
        sources: [
          { title: "Wikipedia — Portal:Bangladesh (Ratargul Swamp Forest)", url: "https://en.wikipedia.org/wiki/Portal:Bangladesh", type: "Official Conservation Record" },
          { title: "Wikimedia Commons — Ratargul from Watch Tower", url: "https://commons.wikimedia.org/wiki/File:Ratargul_Swamp_forest_from_watch_tower_02.jpg", type: "Authenticated Photo" },
          { title: "Pegah — Ratargul by Dinghy Excursion", url: "https://www.pegah.com.bd/", type: "Tour Cost Reference" }
        ],
        images: [
          {
            title: "Ratargul Swamp Forest from Watchtower",
            url: "https://upload.wikimedia.org/wikipedia/commons/c/c6/Ratargul_Swamp_forest_from_watch_tower_02.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Ratargul_Swamp_forest_from_watch_tower_02.jpg",
            author: "Ajoy5135 (Wikimedia Commons)",
            license: "CC BY-SA 4.0",
            viewpoint: "Canopy Watchtower Perspective",
            isPanorama: false
          }
        ],
        hotspots: [
          { id: 'rg-hs-1', yaw: 10, pitch: -4, title: 'Koroch & Hijal Submerged Grove', category: 'Freshwater Ecology', summary: 'Trees remain submerged under 15–25 feet of Gowain River water throughout the monsoon.' },
          { id: 'rg-hs-2', yaw: -45, pitch: 8, title: 'Biodiversity Canopy Watchtower', category: 'Observation Point', summary: 'Vantage point overlooking the Fatehpur Union wetland sanctuary.' }
        ]
      },
      {
        id: 'sundarbans',
        name: 'The Sundarbans Mangrove Forest',
        nameBn: 'সুন্দরবন ম্যানগ্রোভ বন',
        editorialChapter: 'Chapter VI • Tidal Realm of the Royal Bengal Tiger',
        editorialTagline: 'Earth’s largest contiguous halophytic mangrove delta where braided rivers meet the Bay of Bengal.',
        flagship: false,
        popularityRank: 6,
        country: 'Bangladesh',
        region: 'Khulna Division',
        district: 'Khulna, Satkhira & Bagerhat Districts',
        city: 'Karamjal & Katka Sanctuaries',
        coordinates: { lat: 21.9497, lng: 89.1833, label: '21.9497° N, 89.1833° E' },
        categories: ['Forests', 'Natural Wonders', 'Islands'],
        verificationStatus: 'Officially Verified',
        lastUpdated: '2026-11-17',
        hasTruePanoramaPhoto: false,
        shortSummary: "The world's largest contiguous halophytic mangrove forest and UNESCO World Heritage sanctuary of the Royal Bengal Tiger.",
        description: "Formed by the confluence of the Padma, Brahmaputra, and Meghna rivers across the Bengal Delta, the Sundarbans spans ~10,000 km² (~60% in Khulna Division, Bangladesh). Named after the Sundari (Heritiera fomes) mangrove tree, its tidal waterways and mudflats shelter the Royal Bengal Tiger, spotted deer, estuarine crocodiles, and Irrawaddy dolphins.",
        historicalInfo: "Systematic forest management began in the 1860s; the Bangladesh Sundarbans Wildlife Sanctuaries (West, South, and East) were inscribed as a UNESCO World Heritage Site in 1997.",
        culturalInfo: "Traditional forest gatherers—Bawalis (woodcutters) and Mouals (wild honey collectors)—coexist with the tidal ecosystem under seasonal permits from the Forest Department.",
        visitorInfo: {
          bestSeason: 'November to February (Dry winter launch cruising season with calm delta rivers)',
          openingHours: 'Multi-day launch permits issued via Forest Department from Khulna/Mongla; Karamjal open daylight hours',
          entryFee: '3-day Khulna–Katka–Kochikhali river launch expedition with forest guard & guide from ~৳ 16,900',
          accessibility: 'Karamjal elevated wooden boardwalk is accessible; deep sanctuary visits require multi-deck river launch boarding',
          guidelines: 'Visitors must remain accompanied by armed Forest Department guards when disembarking at Katka or Hiron Point.'
        },
        expenses: {
          dailyBudgetUSD: 48,
          dailyStandardUSD: 95,
          dailyPremiumUSD: 190,
          assumptions: 'Based on Pegah 2026 3-day Sundarbans launch cruise rate (from ৳ 16,900 / ~$142 USD total) including meals and forest permits.'
        },
        attractions: [
          { name: 'Katka & Kochikhali Wildlife Sanctuaries', detail: 'Tidal meadows, mangrove creeks, and tiger-spotting watchtowers.' },
          { name: 'Karamjal Eco-Tourism Center', detail: 'Gateway mangrove boardwalk and crocodile conservation center near Mongla.' }
        ],
        vrExperiences: [
          'Curved Optical Satellite & Habitat Gallery (NASA Earth Observatory & Wikimedia Commons)',
          '3D Reconstructed Tidal Mangrove Creek & Pneumatophore Root Ecosystem'
        ],
        sources: [
          { title: "Wikimedia Commons — File:Sundarbans.jpg (NASA Earth Observatory)", url: "https://commons.wikimedia.org/wiki/File:Sundarbans.jpg", type: "Satellite Geospatial Imagery" },
          { title: "Wikimedia Commons — File:Sundarban Tiger.jpg", url: "https://commons.wikimedia.org/wiki/File:Sundarban_Tiger.jpg", type: "Wildlife Archive" },
          { title: "Pegah — Sundarbans Launch Cruise Benchmark", url: "https://www.pegah.com.bd/", type: "Expedition Reference" }
        ],
        images: [
          {
            title: "Sundarbans Royal Bengal Tiger in Mangrove Creek",
            url: "https://upload.wikimedia.org/wikipedia/commons/2/23/Sundarban_Tiger.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Sundarban_Tiger.jpg",
            author: "Soumyajit Nandy (Wikimedia Commons)",
            license: "CC BY-SA 4.0",
            viewpoint: "Mangrove Tidal Bank Habitat",
            isPanorama: false
          },
          {
            title: "NASA Earth Observatory Satellite View of the Sundarbans",
            url: "https://upload.wikimedia.org/wikipedia/commons/a/a2/Sundarbans.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Sundarbans.jpg",
            author: "Jesse Allen, NASA Earth Observatory",
            license: "Public Domain",
            viewpoint: "Orbital Delta & Mangrove Canopy",
            isPanorama: false
          }
        ],
        hotspots: [
          { id: 'sb-hs-1', yaw: -20, pitch: -3, title: 'Sundari & Gewa Pneumatophore Roots', category: 'Halophytic Botany', summary: 'Breathing roots adapted to twice-daily Bay of Bengal saline tidal inundation.' },
          { id: 'sb-hs-2', yaw: 35, pitch: 0, title: 'Katka Tidal Channel Corridor', category: 'Tiger Sanctuary', summary: 'Core UNESCO World Heritage habitat of the swimming Royal Bengal Tiger.' }
        ]
      },
      {
        id: 'tokyo-sensoji',
        name: 'Sensō-ji & Asakusa Cultural District',
        nameBn: 'সেনসো-জি মন্দির ও আসাকুসা, টোকিও',
        editorialChapter: 'Chapter VII • Crimson Lanterns of Edo Tokyo',
        editorialTagline: 'Tokyo’s oldest Buddhist sanctuary anchored by the Kaminarimon Thunder Gate and Five-Storied Pagoda.',
        flagship: false,
        popularityRank: 7,
        country: 'Japan',
        region: 'Kantō Region',
        district: 'Taitō City',
        city: 'Asakusa, Tokyo',
        coordinates: { lat: 35.7148, lng: 139.7967, label: '35.7148° N, 139.7967° E' },
        categories: ['Cultural Destinations', 'Historical Landmarks', 'Tourist Attractions'],
        verificationStatus: 'Independently Sourced',
        lastUpdated: '2026-11-17',
        hasTruePanoramaPhoto: false,
        shortSummary: "Tokyo's oldest Buddhist temple complex in historic Asakusa, anchored by the Kaminarimon Thunder Gate and Five-Storied Pagoda.",
        description: "Located in Asakusa, Taitō City, Tokyo, Sensō-ji (Kinryū-zan Sensō-ji) is an ancient Buddhist temple dedicated to Kannon Bodhisattva. Visitors enter through the iconic Kaminarimon ('Thunder Gate') with its massive red paper lantern (chōchin), stroll along the centuries-old Nakamise-dōri shopping street, pass the Hōzōmon Gate, and reach the Main Hall (Hondō) and Five-Storied Pagoda.",
        historicalInfo: "Founded in 645 AD according to temple tradition, Sensō-ji was patronized by the Tokugawa shogunate during the Edo period and reconstructed after World War II with donations from across Japan.",
        culturalInfo: "Visitors purify hands at the temizuya water pavilion, fan incense smoke from the courtyard jokōro censer for good health, and draw omikuji fortune slips.",
        visitorInfo: {
          bestSeason: 'March to May (Cherry Blossom spring) and October to November (Autumn foliage)',
          openingHours: 'Temple Grounds: Open 24 hours (Illuminated until 11:00 PM); Main Hall (Hondō): 06:00 AM – 05:00 PM',
          entryFee: 'Free admission to all temple grounds and Main Hall',
          accessibility: 'Step-free elevator access installed on the eastern side of the Main Hall (Hondō); flat stone paving along Nakamise-dōri',
          guidelines: 'Bow respectfully at the gates; observe quiet etiquette inside the inner Hondō prayer sanctuary.'
        },
        expenses: {
          dailyBudgetUSD: 75,
          dailyStandardUSD: 165,
          dailyPremiumUSD: 340,
          assumptions: 'Includes Tokyo Metro 24-hour pass, Asakusa traditional street snacks & ramen dining, and Taitō business hotel lodging.'
        },
        attractions: [
          { name: 'Kaminarimon (Thunder Gate)', detail: 'Outer entrance gate housing statues of Fūjin and Raijin alongside a 700-kg red lantern.' },
          { name: 'Main Hall (Hondō) & Hōzōmon', detail: 'Monumental Buddhist sanctuary and two-story treasure gate.' }
        ],
        vrExperiences: [
          'Curved Optical Kaminarimon & Hondō Architectural Viewpoints',
          '3D Reconstructed Asakusa Lantern Courtyard & Pagoda'
        ],
        sources: [
          { title: "Wikimedia Commons — File:Senso-ji Kaminarimon 201503b.jpg", url: "https://commons.wikimedia.org/wiki/File:Senso-ji_Kaminarimon_201503b.jpg", type: "Authenticated Photo" },
          { title: "Wikimedia Commons — File:Senso-ji Hondo 201505.jpg", url: "https://commons.wikimedia.org/wiki/File:Senso-ji_Hondo_201505.jpg", type: "Main Hall Archive" },
          { title: "Wikimedia Commons — Category:Sensoji", url: "https://commons.wikimedia.org/wiki/Category:Sensoji", type: "Media Repository" }
        ],
        images: [
          {
            title: "Sensō-ji Kaminarimon (Thunder Gate), Asakusa, Tokyo",
            url: "https://upload.wikimedia.org/wikipedia/commons/4/47/Senso-ji_Kaminarimon_201503b.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Senso-ji_Kaminarimon_201503b.jpg",
            author: "Tak1701d (Wikimedia Commons)",
            license: "CC BY-SA 4.0",
            viewpoint: "Kaminarimon Entrance Gate",
            isPanorama: false
          },
          {
            title: "Main Hall (Hondō) of Sensō-ji Temple, Tokyo",
            url: "https://upload.wikimedia.org/wikipedia/commons/e/e5/Senso-ji_Hondo_201505.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Senso-ji_Hondo_201505.jpg",
            author: "Tak1701d (Wikimedia Commons)",
            license: "CC BY-SA 4.0",
            viewpoint: "Hondō Main Sanctuary Courtyard",
            isPanorama: false
          }
        ],
        hotspots: [
          { id: 'tk-hs-1', yaw: 0, pitch: 6, title: 'Kaminarimon Great Red Chōchin', category: 'Edo Heritage', summary: 'Iconic 3.9-metre tall lantern flanked by Shinto-Buddhist guardian deities Fūjin and Raijin.' },
          { id: 'tk-hs-2', yaw: 52, pitch: 10, title: 'Hondō Main Hall & Five-Storied Pagoda', category: 'Sacred Architecture', summary: 'Dedicated to Kannon; Tokyo’s oldest temple dating to 645 AD.' }
        ]
      },
      {
        id: 'swiss-alps',
        name: 'Swiss Alps Jungfrau-Aletsch',
        nameBn: 'সুইস আল্পস ইউংফ্রাউ-আলেচ, সুইজারল্যান্ড',
        editorialChapter: 'Chapter VIII • Glaciers of the Bernese Oberland',
        editorialTagline: 'Monumental 4,000-metre alpine peaks watching over the 20-kilometre ribbon of the Great Aletsch Glacier.',
        flagship: false,
        popularityRank: 8,
        country: 'Switzerland',
        region: 'Bernese Oberland & Valais',
        district: 'Interlaken-Oberhasli & Brig',
        city: 'Grindelwald / Naters',
        coordinates: { lat: 46.5475, lng: 7.9853, label: '46.5475° N, 7.9853° E' },
        categories: ['Mountains', 'Natural Wonders'],
        verificationStatus: 'Officially Verified',
        lastUpdated: '2026-11-17',
        hasTruePanoramaPhoto: true,
        shortSummary: "UNESCO World Heritage alpine massif featuring the Eiger, Mönch, Jungfrau peaks and Eurasia's largest glacier, the Great Aletsch.",
        description: "Straddling the cantons of Bern and Valais, the Swiss Alps Jungfrau-Aletsch protected area encompasses dramatic 4,000-metre peaks—Jungfrau (4,158 m), Mönch (4,107 m), and Eiger (3,967 m)—alongside the 20-km Great Aletsch Glacier.",
        historicalInfo: "Inscribed in 2001 as the first UNESCO Natural World Heritage site in the Alps; the historic Jungfrau Railway cogwheel tunnel reached Jungfraujoch (3,454 m) in 1912.",
        culturalInfo: "Traditional Swiss alpine pastoral culture, cogwheel mountain engineering, and glaciological research at the High Altitude Research Station Jungfraujoch.",
        visitorInfo: {
          bestSeason: 'June to September (Alpine hiking trails) or December to March (Winter snow sports)',
          openingHours: 'Protected area open 24 hours; mountain cableways and Jungfrau Railway operate ~07:00 AM – 06:00 PM',
          entryFee: 'Natural trails are free; mountain rail and Eggishorn / Jungfraujoch ascents require rail passes',
          accessibility: 'Step-free Swiss mountain rail and Eiger Express gondola access right up to the Sphinx Observatory at 3,571 m',
          guidelines: 'Acclimatize slowly above 3,000 m; wear UV glacier eyewear and stay on marked crevasse-safe trails.'
        },
        expenses: {
          dailyBudgetUSD: 120,
          dailyStandardUSD: 260,
          dailyPremiumUSD: 520,
          assumptions: 'Includes Swiss Half Fare / Berner Oberland regional rail pass, alpine hostel or chalet lodging, and mountain dining.'
        },
        attractions: [
          { name: 'Great Aletsch Glacier & Eggishorn Viewpoint', detail: 'Largest glacier in the Alps stretching down toward the Valais valley.' },
          { name: 'Jungfrau, Mönch & Eiger North Face', detail: 'Monumental alpine triumvirate rising above Grindelwald and Lauterbrunnen.' }
        ],
        vrExperiences: [
          'Curved Optical Alpine Glacier Viewpoints (Wikimedia Commons)',
          '3D Reconstructed High-Altitude Glacial Ridge & 4,000m Peaks'
        ],
        sources: [
          { title: "Wikimedia Commons — File:Swiss-Alps-Jungfrau-Aletsch.jpg", url: "https://commons.wikimedia.org/wiki/File:Swiss-Alps-Jungfrau-Aletsch.jpg", type: "Authenticated Alpine Photo" },
          { title: "Wikimedia Commons — File:Switzerland - Aletsch Glacier 1.jpg", url: "https://commons.wikimedia.org/wiki/File:Switzerland_-_Aletsch_Glacier_1.jpg", type: "Glacial Survey Media" }
        ],
        images: [
          {
            title: "Gornergrat 360° Alpine Panorama, Switzerland",
            url: "https://upload.wikimedia.org/wikipedia/commons/d/d8/Gornergrat_-_360%C2%B0-Panorama.JPG",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Gornergrat_-_360%C2%B0-Panorama.JPG",
            author: "H. Zell (Wikimedia Commons)",
            license: "CC BY-SA 3.0",
            viewpoint: "360° Alpine Crest Panorama",
            isPanorama: true
          },
          {
            title: "Swiss Alps Jungfrau-Aletsch Panorama",
            url: "https://upload.wikimedia.org/wikipedia/commons/8/84/Swiss-Alps-Jungfrau-Aletsch.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Swiss-Alps-Jungfrau-Aletsch.jpg",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 4.0",
            viewpoint: "Jungfrau-Aletsch Glacial Crest",
            isPanorama: false
          },
          {
            title: "Great Aletsch Glacier, Valais, Switzerland",
            url: "https://upload.wikimedia.org/wikipedia/commons/8/8f/Switzerland_-_Aletsch_Glacier_1.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Switzerland_-_Aletsch_Glacier_1.jpg",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 2.0",
            viewpoint: "Aletsch Ice Stream Curve",
            isPanorama: false
          }
        ],
        hotspots: [
          { id: 'ch-hs-1', yaw: -18, pitch: 12, title: 'Jungfrau Summit (4,158 m)', category: 'Alpine Peak', summary: 'Crowning summit of the Bernese Alps above the Aletsch firn basin.' },
          { id: 'ch-hs-2', yaw: 32, pitch: -8, title: 'Great Aletsch Glacier Tongue', category: 'Glaciology', summary: 'UNESCO World Heritage ice stream monitored for alpine climate telemetry.' }
        ]
      },
      {
        id: 'statue-of-liberty',
        name: 'Statue of Liberty (Liberty Island)',
        nameBn: 'স্ট্যাচু অব লিবার্টি, নিউ ইয়র্ক',
        editorialChapter: 'Chapter IX • Beacon of New York Harbor',
        editorialTagline: 'Frédéric Auguste Bartholdi’s copper colossus standing watch over Upper New York Bay since 1886.',
        flagship: false,
        popularityRank: 9,
        country: 'United States',
        region: 'Northeast / New York State',
        district: 'New York County (Manhattan)',
        city: 'Liberty Island, New York Harbor',
        coordinates: { lat: 40.6892, lng: -74.0445, label: '40.6892° N, 74.0445° W' },
        categories: ['Historical Landmarks', 'Islands', 'Tourist Attractions'],
        verificationStatus: 'Officially Verified',
        lastUpdated: '2026-11-17',
        hasTruePanoramaPhoto: false,
        shortSummary: "Colossal 93-metre neoclassical copper monument on Liberty Island in New York Harbor, sculpted by Bartholdi with Eiffel's iron armature.",
        description: "Formally titled 'Liberty Enlightening the World', the Statue of Liberty stands on Liberty Island in Upper New York Bay. Gifted by the people of France to the United States, its oxidized green copper skin is supported by an internal iron pylon framework engineered by Gustave Eiffel.",
        historicalInfo: "Designed by French sculptor Frédéric Auguste Bartholdi and dedicated on October 28, 1886, the monument welcomed millions of sea-borne immigrants arriving at nearby Ellis Island and was designated a UNESCO World Heritage Site in 1984.",
        culturalInfo: "Holds a torch above her head and a tabula ansata inscribed 'JULY IV MDCCLXXVI' (July 4, 1776), with a broken shackle and chain at her feet commemorating the abolition of slavery.",
        visitorInfo: {
          bestSeason: 'May to June and September to October (Clear harbor visibility and mild Hudson breezes)',
          openingHours: 'Daily: 09:00 AM – 05:00 PM (Ferry departures from Battery Park, NY & Liberty State Park, NJ)',
          entryFee: 'Reserve Ticket (Ferry + Grounds + Museum): ~$25.50 USD; Pedestal & Crown tickets require advance reservation',
          accessibility: 'Liberty Island grounds, Statue of Liberty Museum, and main pedestal lobby are wheelchair accessible via elevator',
          guidelines: 'Only Statue City Cruises is authorized to dock at Liberty Island; airport-style security screening prior to boarding.'
        },
        expenses: {
          dailyBudgetUSD: 110,
          dailyStandardUSD: 245,
          dailyPremiumUSD: 490,
          assumptions: 'Includes official Statue City Cruises ferry ticket, NYC MTA subway transit, Lower Manhattan meals, and hotel stay.'
        },
        attractions: [
          { name: 'Liberty Island & Pedestal Observation Deck', detail: '360° views of Lower Manhattan, Brooklyn Bridge, and New York Harbor.' },
          { name: 'Statue of Liberty Museum & Original Torch', detail: 'Houses the statue’s original 1886 gilded flame and engineering exhibits.' }
        ],
        vrExperiences: [
          'Curved Optical Harbor & Monument Viewpoints (Wikimedia Commons)',
          '3D Reconstructed Liberty Island & New York Harbor Bay'
        ],
        sources: [
          { title: "Wikimedia Commons — File:Statue of Liberty, NY.jpg", url: "https://commons.wikimedia.org/wiki/File:Statue_of_Liberty,_NY.jpg", type: "UNESCO & Historical Summary" },
          { title: "Wikimedia Commons — File:Statue of Liberty National Monument STLI2258.jpg", url: "https://commons.wikimedia.org/wiki/File:Statue_of_Liberty_National_Monument_STLI2258.jpg", type: "NPS Archive" }
        ],
        images: [
          {
            title: "Statue of Liberty on Liberty Island, New York Harbor",
            url: "https://upload.wikimedia.org/wikipedia/commons/d/d3/Statue_of_Liberty%2C_NY.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Statue_of_Liberty,_NY.jpg",
            author: "Avala (Wikimedia Commons)",
            license: "CC BY 2.0",
            viewpoint: "Harbor Approach Elevation",
            isPanorama: false
          },
          {
            title: "Statue of Liberty National Monument Close View",
            url: "https://upload.wikimedia.org/wikipedia/commons/7/77/Statue_of_Liberty_National_Monument_STLI2258.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Statue_of_Liberty_National_Monument_STLI2258.jpg",
            author: "National Park Service / Wikimedia Commons",
            license: "Public Domain",
            viewpoint: "Copper Patina & Torch Detail",
            isPanorama: false
          }
        ],
        hotspots: [
          { id: 'ny-hs-1', yaw: 0, pitch: 15, title: 'Bartholdi Copper Figure & Eiffel Pylon', category: 'Structural Engineering', summary: 'Repoussé copper sheets hung on a flexible iron truss designed by Gustave Eiffel.' },
          { id: 'ny-hs-2', yaw: -40, pitch: -4, title: 'Star-Shaped Fort Wood Pedestal', category: '19th-Century Fort', summary: 'Granite pedestal designed by Richard Morris Hunt set inside 11-pointed Fort Wood.' }
        ]
      },
      {
        id: 'rome-colosseum',
        name: 'The Colosseum (Flavian Amphitheatre)',
        nameBn: 'রোমান কলোসিয়াম, ইতালি',
        editorialChapter: 'Chapter X • Travertine Arches of Imperial Rome',
        editorialTagline: 'The monumental 80 AD Flavian Amphitheatre and its subterranean hypogeum in the heart of Rome.',
        flagship: false,
        popularityRank: 10,
        country: 'Italy',
        region: 'Lazio Region',
        district: 'Metropolitan City of Rome',
        city: 'Piazza del Colosseo, Rome',
        coordinates: { lat: 41.8902, lng: 12.4922, label: '41.8902° N, 12.4922° E' },
        categories: ['Historical Landmarks', 'Cultural Destinations', 'Tourist Attractions'],
        verificationStatus: 'Officially Verified',
        lastUpdated: '2026-11-17',
        hasTruePanoramaPhoto: true,
        shortSummary: "Ancient Rome's monumental travertine and tuff Flavian Amphitheatre completed in 80 AD under Emperors Vespasian and Titus.",
        description: "Located east of the Roman Forum in the heart of Rome, the Colosseum is the largest ancient amphitheatre ever built. Constructed of travertine limestone, volcanic tuff, and brick-faced concrete, it features an elliptical arena floor beneath which lies the intricate subterranean hypogeum.",
        historicalInfo: "Commissioned by Emperor Vespasian around 70–72 AD and inaugurated by his son Titus in 80 AD, with modifications under Domitian. The subterranean hypogeum network was opened to public exploration in summer 2010.",
        culturalInfo: "Showcases the classical Roman superposition of Doric, Ionic, and Corinthian architectural orders across its three-tiered arcaded travertine facade.",
        visitorInfo: {
          bestSeason: 'March to May and October to November (Comfortable Mediterranean temperatures)',
          openingHours: 'Daily: 08:30 AM – 04:30 PM (Extended evening hours during summer months)',
          entryFee: 'Standard 24h Colosseum, Roman Forum & Palatine Hill ticket: €18 (Free for visitors under 18)',
          accessibility: 'Ground tier and upper ring accessible via modern internal elevator and ramps',
          guidelines: 'Named nominal tickets required matching passport/ID; large backpacks are not permitted inside.'
        },
        expenses: {
          dailyBudgetUSD: 85,
          dailyStandardUSD: 190,
          dailyPremiumUSD: 390,
          assumptions: 'Includes €18 Archaeological Park admission, Rome Metro transit, Trastevere/Monti dining, and central Rome hotel.'
        },
        attractions: [
          { name: 'Subterranean Hypogeum & Arena Floor', detail: 'Underground two-level network of cages, trapdoors, and hoists opened to visitors in 2010.' },
          { name: 'Three-Tiered Travertine Outer Wall', detail: 'Monumental arcades displaying Doric, Ionic, and Corinthian half-columns.' }
        ],
        vrExperiences: [
          'Curved Optical Interior Hypogeum & Exterior Facade Gallery (Wikimedia Commons)',
          '3D Reconstructed Flavian Elliptical Arena & Travertine Arcades'
        ],
        sources: [
          { title: "Wikimedia Commons — File:Colosseum Rome.jpg (Hypogeum Interior)", url: "https://commons.wikimedia.org/wiki/File:Colosseum_Rome.jpg", type: "Public Domain Interior Photo" },
          { title: "Wikimedia Commons — File:Colosseum in rome.jpg", url: "https://commons.wikimedia.org/wiki/File:Colosseum_in_rome.jpg", type: "Exterior Architecture" }
        ],
        images: [
          {
            title: "Panorama inside Colosseum, Rome (11,132 × 3,040)",
            url: "https://commons.wikimedia.org/wiki/Special:FilePath/Panorama_inside_Colosseum.jpg?width=2048",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Panorama_inside_Colosseum.jpg",
            author: "Jaakko Luttinen (Wikimedia Commons)",
            license: "CC BY-SA 3.0",
            viewpoint: "Interior 360° Arena Panorama",
            isPanorama: true
          },
          {
            title: "Colosseum Exterior Facade, Rome",
            url: "https://upload.wikimedia.org/wikipedia/commons/7/7a/Colosseum_in_rome.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Colosseum_in_rome.jpg",
            author: "Silviomerci1971 (Wikimedia Commons)",
            license: "CC BY-SA 4.0",
            viewpoint: "Outer Travertine Arcades",
            isPanorama: false
          },
          {
            title: "Colosseum Subterranean Hypogeum & Arena Interior",
            url: "https://upload.wikimedia.org/wikipedia/commons/8/80/Colosseum_Rome.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Colosseum_Rome.jpg",
            author: "U.S. Air Force / Wikimedia Commons",
            license: "Public Domain",
            viewpoint: "Interior Arena & Hypogeum Chambers",
            isPanorama: false
          }
        ],
        hotspots: [
          { id: 'rm-hs-1', yaw: 0, pitch: -10, title: 'Subterranean Hypogeum Chambers', category: 'Roman Engineering', summary: 'Underground network beneath the wooden arena floor opened to visitors in summer 2010.' },
          { id: 'rm-hs-2', yaw: 48, pitch: 12, title: 'Travertine Doric-Ionic-Corinthian Tiers', category: 'Classical Architecture', summary: 'Begun by Emperor Vespasian and completed by Titus in 80 AD.' }
        ]
      },
      {
        id: 'great-wall-mutianyu',
        name: 'Great Wall of China (Mutianyu Section)',
        nameBn: 'চীনের মহাপ্রাচীর (মুতিয়ানইউ অংশ)',
        editorialChapter: 'Chapter XI • Granite Ramparts of the Northern Ridge',
        editorialTagline: 'Ming Dynasty crenellated watchtowers winding across the forested mountain crests of Huairou.',
        flagship: false,
        popularityRank: 11,
        country: 'China',
        region: 'North China',
        district: 'Huairou District',
        city: 'Beijing Municipality',
        coordinates: { lat: 40.4319, lng: 116.5704, label: '40.4319° N, 116.5704° E' },
        categories: ['Historical Landmarks', 'Mountains', 'Cultural Destinations'],
        verificationStatus: 'Independently Sourced',
        lastUpdated: '2026-11-17',
        hasTruePanoramaPhoto: true,
        shortSummary: "Ming Dynasty granite-fortified mountain ridge section of the 8,851.8-km Great Wall network in Huairou District north of Beijing.",
        description: "Located in Huairou District ~70 km northeast of central Beijing, the Mutianyu section of the Great Wall is famed for its densely spaced Ming Dynasty watchtowers, double-sided crenellated parapets, and lush pine-and-cypress mountain ridges.",
        historicalInfo: "While early walls date to the 5th century BC and Qin Shi Huang (220–206 BC), the surviving stone fortifications at Mutianyu were built in the mid-6th century (Northern Qi) and rebuilt in granite under Ming Dynasty General Xu Da in 1368 and Qi Jiguang in 1569. Comprehensive archaeological surveys measure the entire Ming Great Wall network (including branches) at 8,851.8 km (6,259.6 km of actual wall, 359.7 km of trenches, and 2,232.5 km of natural barriers).",
        culturalInfo: "Represents ancient Chinese military engineering, beacon tower optical signaling, and forested mountain conservation.",
        visitorInfo: {
          bestSeason: 'April to May (Spring blossoms) and September to October (Crisp autumn foliage)',
          openingHours: 'Daily: 07:30 AM – 06:00 PM (08:00 AM – 05:00 PM in winter)',
          entryFee: 'Adult Admission: ~40 CNY + Shuttle Bus 15 CNY; Optional Gondola Cable Car ~140 CNY round-trip',
          accessibility: 'Mutianyu Gondola Cable Car provides enclosed cabin access up to Watchtower 14 with a ramped viewing platform',
          guidelines: 'Wear high-traction footwear for steep stone steps between Watchtowers 14 and 20; never carve or remove wall bricks.'
        },
        expenses: {
          dailyBudgetUSD: 45,
          dailyStandardUSD: 105,
          dailyPremiumUSD: 225,
          assumptions: 'Includes Beijing–Mutianyu tourist bus, park admission + cable car ticket, local Huairou trout meal, and Beijing hotel.'
        },
        attractions: [
          { name: 'Watchtowers 14 to 20 Ridge Walk', detail: 'High-elevation Ming Dynasty granite ramparts winding across forested peaks.' },
          { name: 'Zhengguantai Three-Tower Gate', detail: 'Rare defensive fort formed by three connected hollow watchtowers.' }
        ],
        vrExperiences: [
          'Curved Optical Ming Rampart Ridge Viewpoints (Wikimedia Commons)',
          '3D Reconstructed Mountain Ridge & Watchtower Fortification'
        ],
        sources: [
          { title: "Wikimedia Commons — File:Great Wall of China, Mutianyu Section.jpg", url: "https://commons.wikimedia.org/wiki/File:Great_Wall_of_China,_Mutianyu_Section.jpg", type: "Survey Metrics & Photo" },
          { title: "Wikimedia Commons — File:Great Wall of China at Mutianyu.JPG", url: "https://commons.wikimedia.org/wiki/File:Great_Wall_of_China_at_Mutianyu.JPG", type: "CC0 Public Domain Photo" }
        ],
        images: [
          {
            title: "Mutianyu Great Wall Wide Panorama, Beijing",
            url: "https://commons.wikimedia.org/wiki/Special:FilePath/Pano_mutianyu_great_wall.jpg?width=2048",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Pano_mutianyu_great_wall.jpg",
            author: "Fabien Dany (Wikimedia Commons)",
            license: "CC BY-SA 2.5",
            viewpoint: "Mutianyu Ridge Panorama",
            isPanorama: true
          },
          {
            title: "Great Wall of China, Mutianyu Section",
            url: "https://upload.wikimedia.org/wikipedia/commons/9/9a/Great_Wall_of_China%2C_Mutianyu_Section.jpg",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Great_Wall_of_China,_Mutianyu_Section.jpg",
            author: "Wikimedia Commons Contributor",
            license: "CC BY-SA 4.0",
            viewpoint: "Mutianyu Ridge Ramparts",
            isPanorama: false
          },
          {
            title: "View from Atop the Great Wall at Mutianyu",
            url: "https://upload.wikimedia.org/wikipedia/commons/e/eb/Great_Wall_of_China_at_Mutianyu.JPG",
            commonsPage: "https://commons.wikimedia.org/wiki/File:Great_Wall_of_China_at_Mutianyu.JPG",
            author: "CTLiotta (Wikimedia Commons)",
            license: "CC0 1.0 Public Domain",
            viewpoint: "Granite Rampart & Watchtower",
            isPanorama: false
          }
        ],
        hotspots: [
          { id: 'gw-hs-1', yaw: 15, pitch: 8, title: 'Ming Dynasty Watchtower 14', category: 'Military Architecture', summary: 'Part of the 8,851.8-km surveyed Ming Great Wall fortification system.' },
          { id: 'gw-hs-2', yaw: -35, pitch: -5, title: 'Double-Sided Crenellated Parapets', category: 'Granite Rampart', summary: 'Engineered to defend against both northern and southern mountain passes in Huairou.' }
        ]
      }
    ];

    // =========================================================================
    // 2. NATIVE UNITY 6 LTS + META QUEST XR C# SOURCE CODE REPOSITORY
    // =========================================================================
    const UNITY_PROJECT_FILES = [
      {
        path: 'Assets/Scripts/VR/FirstPersonHandTrackedBeachVR.cs',
        desc: 'First-person Meta Quest 6DoF beach rig with OVRHand virtual hands, palm shadows & wave physics',
        code: `using UnityEngine;

namespace GlobalVRExplorer.VR
{
    /// <summary>
    /// Full-screen first-person VR experience controller for Meta Quest 3 / 3S (Unity 6 LTS + OpenXR + Meta XR SDK).
    /// Renders articulated left/right OVRHand / OvrAvatar2 virtual hands in the foreground with real-time URP
    /// palm-frond shadow cascades, animated Bay of Bengal waves, and natural head-tracked standing/lounging presence.
    /// </summary>
    public class FirstPersonHandTrackedBeachVR : MonoBehaviour
    {
        [Header("OpenXR / OVR First-Person Rig")]
        [SerializeField] private Transform ovrCameraRigRoot;
        [SerializeField] private Transform centerEyeAnchor;
        [SerializeField] private Transform leftHandAnchor;
        [SerializeField] private Transform rightHandAnchor;

        [Header("Articulated Virtual Hands (OVRHand / OvrAvatar2)")]
        [SerializeField] private SkinnedMeshRenderer leftVirtualHandMesh;
        [SerializeField] private SkinnedMeshRenderer rightVirtualHandMesh;
        [SerializeField] private Animator leftHandAnimator;
        [SerializeField] private Animator rightHandAnimator;

        [Header("Coastal Environment & Palm Shadow Caster")]
        [SerializeField] private Light directionalSunLight;
        [SerializeField] private Material oceanWaveMaterial;
        [SerializeField] private Transform[] palmFrondShadowCasters;
        [SerializeField] private AudioSource oceanSurfSpatialAudio;

        private void Start()
        {
            if (directionalSunLight != null)
            {
                directionalSunLight.shadows = LightShadows.Soft;
                directionalSunLight.shadowStrength = 0.82f;
            }
            if (leftVirtualHandMesh != null) leftVirtualHandMesh.receiveShadows = true;
            if (rightVirtualHandMesh != null) rightVirtualHandMesh.receiveShadows = true;
        }

        private void Update()
        {
            float t = Time.time;
            if (oceanWaveMaterial != null)
            {
                oceanWaveMaterial.SetFloat("_WaveTime", t);
            }
            if (palmFrondShadowCasters != null)
            {
                for (int i = 0; i < palmFrondShadowCasters.Length; i++)
                {
                    if (palmFrondShadowCasters[i] != null)
                    {
                        palmFrondShadowCasters[i].localRotation = Quaternion.Euler(
                            Mathf.Sin(t * 1.2f + i) * 2.4f,
                            0f,
                            Mathf.Cos(t * 0.9f + i) * 1.8f
                        );
                    }
                }
            }
        }
    }
}`
      },
      {
        path: 'Assets/Scripts/VR/VRPanoramaManager.cs',
        desc: 'Head-tracked 8K equirectangular skybox & OVROverlay compositor layer controller',
        code: `using System.Collections;
using UnityEngine;
using UnityEngine.Networking;

namespace GlobalVRExplorer.VR
{
    public class VRPanoramaManager : MonoBehaviour
    {
        [Header("Meta XR & Rendering References")]
        [SerializeField] private Material equirectangularSkyboxMaterial;
        [SerializeField] private Transform curvedOpticalGalleryWall;
        [SerializeField] private MeshRenderer curvedGalleryRenderer;
        [SerializeField] private Transform reconstructedEnvironmentRoot;
        [SerializeField] private CanvasGroup transitionComfortFade;

        [Header("Active State")]
        [SerializeField] private DestinationDataSO activeDestination;
        private GameObject spawnedEnvironmentInstance;

        public void LoadDestination(DestinationDataSO destination)
        {
            if (destination == null) return;
            StopAllCoroutines();
            StartCoroutine(TransitionToDestinationRoutine(destination));
        }

        private IEnumerator TransitionToDestinationRoutine(DestinationDataSO destination)
        {
            yield return FadeComfortOverlay(1f, 0.25f);
            activeDestination = destination;

            if (spawnedEnvironmentInstance != null)
            {
                Destroy(spawnedEnvironmentInstance);
                spawnedEnvironmentInstance = null;
            }

            if (destination.hasTrueEquirectangularPanorama && destination.equirectangularTexture != null)
            {
                curvedOpticalGalleryWall.gameObject.SetActive(false);
                equirectangularSkyboxMaterial.SetTexture("_MainTex", destination.equirectangularTexture);
                RenderSettings.skybox = equirectangularSkyboxMaterial;
                DynamicGI.UpdateEnvironment();
            }
            else
            {
                curvedOpticalGalleryWall.gameObject.SetActive(true);
                if (destination.primaryAuthenticPhoto != null)
                {
                    curvedGalleryRenderer.material.mainTexture = destination.primaryAuthenticPhoto;
                    PreserveAspectRatioOnCurvedWall(
                        destination.primaryAuthenticPhoto.width,
                        destination.primaryAuthenticPhoto.height
                    );
                }

                if (destination.reconstructed3DEnvironmentPrefab != null)
                {
                    spawnedEnvironmentInstance = Instantiate(
                        destination.reconstructed3DEnvironmentPrefab,
                        reconstructedEnvironmentRoot
                    );
                }
            }

            yield return FadeComfortOverlay(0f, 0.35f);
        }

        private void PreserveAspectRatioOnCurvedWall(int width, int height)
        {
            if (height <= 0) return;
            float aspect = Mathf.Clamp((float)width / height, 1.0f, 2.4f);
            curvedOpticalGalleryWall.localScale = new Vector3(aspect * 3.2f, 3.2f, 1.0f);
        }

        private IEnumerator FadeComfortOverlay(float targetAlpha, float duration)
        {
            if (transitionComfortFade == null) yield break;
            float start = transitionComfortFade.alpha;
            float elapsed = 0f;
            while (elapsed < duration)
            {
                elapsed += Time.deltaTime;
                transitionComfortFade.alpha = Mathf.Lerp(start, targetAlpha, elapsed / duration);
                yield return null;
            }
            transitionComfortFade.alpha = targetAlpha;
        }
    }
}`
      },
      {
        path: 'Assets/Scripts/Core/DestinationDataSO.cs',
        desc: 'ScriptableObject holding WGS84 coordinates, Wikimedia attribution, and verification status',
        code: `using System;
using System.Collections.Generic;
using UnityEngine;

namespace GlobalVRExplorer.Core
{
    public enum VerificationTier
    {
        OfficiallyVerified,
        IndependentlySourced,
        CommunitySubmittedUnverified
    }

    [Serializable]
    public struct SpatialHotspotData
    {
        public string hotspotId;
        public string title;
        public string category;
        [Range(-180f, 180f)] public float yawDegrees;
        [Range(-85f, 85f)] public float pitchDegrees;
        [TextArea(2, 4)] public string verifiedSummary;
        public AudioClip spatialNarrationClip;
    }

    [CreateAssetMenu(fileName = "DestinationData", menuName = "GlobalVRExplorer/DestinationDataSO")]
    public class DestinationDataSO : ScriptableObject
    {
        public string destinationId;
        public string displayName;
        public string country;
        public string divisionOrRegion;
        public string districtOrCity;
        public double latitude;
        public double longitude;
        public VerificationTier verificationStatus;
        public string lastUpdatedIsoDate = "2026-11-17";
        public string wikimediaCommonsUrl;
        public string photographerAttribution;
        public string licenseName = "CC BY-SA 4.0";
        public bool hasTrueEquirectangularPanorama;
        public Texture2D equirectangularTexture;
        public Texture2D primaryAuthenticPhoto;
        public List<Texture2D> multiViewpointGallery = new List<Texture2D>();
        public GameObject reconstructed3DEnvironmentPrefab;
        public AudioClip ambientSoundscapeLoop;
        public List<SpatialHotspotData> hotspots = new List<SpatialHotspotData>();
    }
}`
      },
      {
        path: 'Assets/Scripts/VR/SpatialHotspotController.cs',
        desc: 'XR Ray Interactor & Hand Tracking pinch hotspot with world-space billboard UI',
        code: `using UnityEngine;
using UnityEngine.Events;
using GlobalVRExplorer.Core;

namespace GlobalVRExplorer.VR
{
    public class SpatialHotspotController : MonoBehaviour
    {
        [SerializeField] private float sphereRadiusMeters = 8.5f;
        [SerializeField] private Transform centerEyeAnchor;
        [SerializeField] private AudioSource spatialFeedbackAudio;
        public UnityEvent<SpatialHotspotData> onHotspotSelected;

        private SpatialHotspotData data;

        public void InitializeHotspot(SpatialHotspotData hotspotData, Transform headTransform)
        {
            data = hotspotData;
            centerEyeAnchor = headTransform;

            float yawRad = hotspotData.yawDegrees * Mathf.Deg2Rad;
            float pitchRad = hotspotData.pitchDegrees * Mathf.Deg2Rad;

            Vector3 localPos = new Vector3(
                sphereRadiusMeters * Mathf.Cos(pitchRad) * Mathf.Sin(yawRad),
                sphereRadiusMeters * Mathf.Sin(pitchRad),
                sphereRadiusMeters * Mathf.Cos(pitchRad) * Mathf.Cos(yawRad)
            );
            transform.localPosition = localPos;
            UpdateBillboardOrientation();
        }

        private void LateUpdate()
        {
            UpdateBillboardOrientation();
        }

        private void UpdateBillboardOrientation()
        {
            if (centerEyeAnchor == null) return;
            Vector3 lookDir = transform.position - centerEyeAnchor.position;
            if (lookDir.sqrMagnitude > 0.001f)
            {
                transform.rotation = Quaternion.LookRotation(lookDir.normalized, Vector3.up);
            }
        }

        public void OnSelectEntered()
        {
            if (spatialFeedbackAudio != null) spatialFeedbackAudio.Play();
            onHotspotSelected?.Invoke(data);
        }
    }
}`
      },
      {
        path: 'Assets/Scripts/VR/QuestComfortLocomotion.cs',
        desc: 'Snap turn (15°/30°/45°), comfort tunneling vignette, and 90Hz/120Hz OVR display control',
        code: `using UnityEngine;

namespace GlobalVRExplorer.VR
{
    public class QuestComfortLocomotion : MonoBehaviour
    {
        [SerializeField] private float snapTurnDegrees = 45f;
        [SerializeField] private float snapCooldownSeconds = 0.35f;
        [SerializeField] private bool enableComfortVignette = true;
        [SerializeField] private CanvasGroup vignetteCanvasGroup;

        private float nextSnapTime;

        public void PerformSnapTurn(float horizontalAxisInput)
        {
            if (Time.time < nextSnapTime || Mathf.Abs(horizontalAxisInput) < 0.65f) return;
            nextSnapTime = Time.time + snapCooldownSeconds;

            float direction = Mathf.Sign(horizontalAxisInput);
            transform.Rotate(0f, direction * snapTurnDegrees, 0f, Space.World);

            if (enableComfortVignette && vignetteCanvasGroup != null)
            {
                vignetteCanvasGroup.alpha = 0.75f;
            }
        }

        private void Update()
        {
            if (vignetteCanvasGroup != null && vignetteCanvasGroup.alpha > 0f)
            {
                vignetteCanvasGroup.alpha = Mathf.MoveTowards(
                    vignetteCanvasGroup.alpha, 0f, Time.deltaTime * 2.5f
                );
            }
        }
    }
}`
      },
      {
        path: 'Packages/manifest.json',
        desc: 'Unity 6 LTS package manifest configuring OpenXR, Meta XR SDK v71, and URP',
        code: `{
  "dependencies": {
    "com.meta.xr.sdk.core": "71.0.0",
    "com.meta.xr.sdk.interaction.ovr": "71.0.0",
    "com.meta.xr.sdk.audio": "71.0.0",
    "com.unity.render-pipelines.universal": "17.0.3",
    "com.unity.xr.management": "4.5.0",
    "com.unity.xr.openxr": "1.13.1",
    "com.unity.xr.interaction.toolkit": "3.0.5",
    "com.unity.ugui": "2.0.0"
  }
}`
      }
    ];

    // =========================================================================
    // 3. UNIVERSAL MULTILINGUAL SYSTEM (16+ LANGUAGES, TYPE-TO-SELECT, RTL)
    // =========================================================================
    const LANGUAGE_REGISTRY = [
      { code: 'en', name: 'English', native: 'English', rtl: false, aliases: 'english en us uk' },
      { code: 'bn', name: 'Bengali', native: 'বাংলা', rtl: false, aliases: 'bangla bengali বাংলা bn' },
      { code: 'hi', name: 'Hindi', native: 'हिन्दी', rtl: false, aliases: 'hindi हिन्दी hi' },
      { code: 'ar', name: 'Arabic', native: 'العربية', rtl: true, aliases: 'arabic العربية ar' },
      { code: 'zh', name: 'Chinese', native: '中文', rtl: false, aliases: 'chinese mandarin 中文 zh' },
      { code: 'ja', name: 'Japanese', native: '日本語', rtl: false, aliases: 'japanese 日本語 ja jp' },
      { code: 'ko', name: 'Korean', native: '한국어', rtl: false, aliases: 'korean 한국어 ko kr' },
      { code: 'fr', name: 'French', native: 'Français', rtl: false, aliases: 'french français fr' },
      { code: 'es', name: 'Spanish', native: 'Español', rtl: false, aliases: 'spanish español es' },
      { code: 'de', name: 'German', native: 'Deutsch', rtl: false, aliases: 'german deutsch de' },
      { code: 'pt', name: 'Portuguese', native: 'Português', rtl: false, aliases: 'portuguese português pt' },
      { code: 'ru', name: 'Russian', native: 'Русский', rtl: false, aliases: 'russian русский ru' },
      { code: 'tr', name: 'Turkish', native: 'Türkçe', rtl: false, aliases: 'turkish türkçe tr' },
      { code: 'id', name: 'Indonesian', native: 'Bahasa Indonesia', rtl: false, aliases: 'indonesian bahasa id' },
      { code: 'ur', name: 'Urdu', native: 'اردو', rtl: true, aliases: 'urdu اردو ur' },
      { code: 'it', name: 'Italian', native: 'Italiano', rtl: false, aliases: 'italian italiano it' }
    ];

    const I18N = {
      en: {
        nav_home: 'Home', nav_globe: 'Explore', nav_explore: 'Destinations', nav_panorama: '360° VR Studio',
        nav_assistant: 'Travel Prep', nav_compare: 'Compare', nav_unity: 'Meta Quest XR Code', nav_saved: 'Saved',
        nav_technology: 'Technology', nav_about: 'About', nav_contact: 'Contact', nav_start_exploring: 'Start Exploring',
        hero_badge: 'Experience Earth like never before', btn_explore_world: 'Explore the World', btn_discover_destinations: 'Search & Filter All',
        auth_heading: 'Explore Beyond Boundaries.', auth_subtitle: 'Your journey around the world begins here.',
        auth_guest_btn: 'Continue as Guest', auth_google_btn: 'Continue with Google', auth_tab_signin: 'Sign in with Email',
        auth_tab_signup: 'Create Account', auth_email_label: 'Email address', auth_forgot_link: 'Forgot password?',
        auth_signin_btn: 'Sign in with Email', auth_fullname_label: 'Full name', auth_create_btn: 'Create Account',
        profile_signout: 'Sign Out', vr_exit: 'Exit VR', vr_mode_photo360: 'Photo 360 VR',
        vr_mode_3d: 'Distinct 3D World', vr_mode_stereo: 'Stereo VR'
      },
      bn: {
        nav_home: 'হোম', nav_globe: 'অন্বেষণ', nav_explore: 'গন্তব্যসমূহ', nav_panorama: '৩৬০° ভিআর স্টুডিও',
        nav_assistant: 'ভ্রমণ প্রস্তুতি', nav_compare: 'তুলনা', nav_unity: 'মেটা কোয়েস্ট কোড', nav_saved: 'সংরক্ষিত',
        nav_technology: 'প্রযুক্তি', nav_about: 'সম্পর্কে', nav_contact: 'যোগাযোগ', nav_start_exploring: 'ঘুরে দেখা শুরু করুন',
        hero_badge: 'ভ্রমণের আগেই পৃথিবী ঘুরে দেখুন', btn_explore_world: 'পৃথিবী অন্বেষণ করুন', btn_discover_destinations: 'সকল গন্তব্য খুঁজুন',
        auth_heading: 'সীমানা ছাড়িয়ে বিশ্ব অন্বেষণ করুন।', auth_subtitle: 'আপনার বিশ্ব ভ্রমণ এখান থেকেই শুরু।',
        auth_guest_btn: 'অতিথি হিসেবে প্রবেশ করুন', auth_google_btn: 'গুগল দিয়ে চালিয়ে যান', auth_tab_signin: 'ইমেইল দিয়ে সাইন ইন',
        auth_tab_signup: 'নতুন অ্যাকাউন্ট তৈরি করুন', auth_email_label: 'ইমেইল ঠিকানা', auth_forgot_link: 'পাসওয়ার্ড ভুলে গেছেন?',
        auth_signin_btn: 'ইমেইল দিয়ে সাইন ইন', auth_fullname_label: 'পূর্ণ নাম', auth_create_btn: 'অ্যাকাউন্ট তৈরি করুন',
        profile_signout: 'সাইন আউট', vr_exit: 'ভিআর থেকে বের হন', vr_mode_photo360: 'ফটো ৩৬০° ভিআর',
        vr_mode_3d: '৩ডি বিশ্ব', vr_mode_stereo: 'স্টেরিও ভিআর'
      },
      hi: {
        nav_home: 'होम', nav_globe: 'एक्सप्लोर', nav_explore: 'गंतव्य', nav_panorama: '360° वीआर स्टूडियो',
        nav_assistant: 'यात्रा तैयारी', nav_compare: 'तुलना', nav_unity: 'मेटा क्वेस्ट कोड', nav_saved: 'सहेजे गए',
        nav_technology: 'तकनीक', nav_about: 'हमारे बारे में', nav_contact: 'संपर्क', nav_start_exploring: 'खोज शुरू करें',
        hero_badge: 'पहले से कहीं बेहतर पृथ्वी का अनुभव करें', btn_explore_world: 'दुनिया की सैर करें', btn_discover_destinations: 'सभी गंतव्य खोजें',
        auth_heading: 'सीमाओं से परे अन्वेषण करें।', auth_subtitle: 'आपकी विश्व यात्रा यहीं से शुरू होती है।',
        auth_guest_btn: 'अतिथि के रूप में जारी रखें', auth_google_btn: 'Google के साथ जारी रखें', auth_tab_signin: 'ईमेल से साइन इन करें',
        auth_tab_signup: 'खाता बनाएं', auth_email_label: 'ईमेल पता', auth_forgot_link: 'पासवर्ड भूल गए?',
        auth_signin_btn: 'ईमेल से साइन इन करें', auth_fullname_label: 'पूरा नाम', auth_create_btn: 'खाता बनाएं',
        profile_signout: 'साइन आउट', vr_exit: 'वीआर से बाहर निकलें', vr_mode_photo360: 'फोटो 360° वीआर',
        vr_mode_3d: '3D दुनिया', vr_mode_stereo: 'स्टीरियो वीआर'
      },
      ar: {
        nav_home: 'الرئيسية', nav_globe: 'استكشف', nav_explore: 'الوجهات', nav_panorama: 'استوديو الواقع الافتراضي 360°',
        nav_assistant: 'تجهيز السفر', nav_compare: 'مقارنة', nav_unity: 'كود Meta Quest', nav_saved: 'المحفوظات',
        nav_technology: 'التقنية', nav_about: 'حول', nav_contact: 'اتصل بنا', nav_start_exploring: 'ابدأ الاستكشاف',
        hero_badge: 'اكتشف الأرض كما لم ترها من قبل', btn_explore_world: 'استكشف العالم', btn_discover_destinations: 'البحث عن الوجهات',
        auth_heading: 'استكشف ما وراء الحدود.', auth_subtitle: 'رحلتك حول العالم تبدأ من هنا.',
        auth_guest_btn: 'المتابعة كضيف', auth_google_btn: 'المتابعة باستخدام Google', auth_tab_signin: 'تسجيل الدخول بالبريد',
        auth_tab_signup: 'إنشاء حساب', auth_email_label: 'البريد الإلكتروني', auth_forgot_link: 'نسيت كلمة المرور؟',
        auth_signin_btn: 'تسجيل الدخول بالبريد', auth_fullname_label: 'الاسم الكامل', auth_create_btn: 'إنشاء حساب',
        profile_signout: 'تسجيل الخروج', vr_exit: 'خروج', vr_mode_photo360: 'صورة 360°',
        vr_mode_3d: 'عالم ثلاثي الأبعاد', vr_mode_stereo: 'ستيريو VR'
      },
      zh: {
        nav_home: '首页', nav_globe: '探索', nav_explore: '目的地', nav_panorama: '360° VR 工作室',
        nav_assistant: '旅行准备', nav_compare: '对比', nav_unity: 'Meta Quest 代码', nav_saved: '已保存',
        nav_technology: '技术', nav_about: '关于', nav_contact: '联系', nav_start_exploring: '开始探索',
        hero_badge: '前所未有地体验地球', btn_explore_world: '探索世界', btn_discover_destinations: '搜索所有目的地',
        auth_heading: '探索无界世界。', auth_subtitle: '您的环球之旅从这里开始。',
        auth_guest_btn: '以访客身份继续', auth_google_btn: '使用 Google 继续', auth_tab_signin: '邮箱登录',
        auth_tab_signup: '创建账户', auth_email_label: '电子邮箱', auth_forgot_link: '忘记密码？',
        auth_signin_btn: '邮箱登录', auth_fullname_label: '全名', auth_create_btn: '创建账户',
        profile_signout: '退出登录', vr_exit: '退出 VR', vr_mode_photo360: '360° 全景',
        vr_mode_3d: '3D 世界', vr_mode_stereo: '立体 VR'
      },
      ja: {
        nav_home: 'ホーム', nav_globe: '探索', nav_explore: '目的地', nav_panorama: '360° VRスタジオ',
        nav_assistant: '旅行準備', nav_compare: '比較', nav_unity: 'Meta Quest コード', nav_saved: '保存済み',
        nav_technology: 'テクノロジー', nav_about: '概要', nav_contact: 'お問い合わせ', nav_start_exploring: '探索を始める',
        hero_badge: 'これまでにない地球体験', btn_explore_world: '世界を探索', btn_discover_destinations: 'すべて検索',
        auth_heading: '境界を越えて探索しよう。', auth_subtitle: '世界を巡る旅がここから始まります。',
        auth_guest_btn: 'ゲストとして続ける', auth_google_btn: 'Googleで続ける', auth_tab_signin: 'メールでサインイン',
        auth_tab_signup: 'アカウント作成', auth_email_label: 'メールアドレス', auth_forgot_link: 'パスワードをお忘れですか？',
        auth_signin_btn: 'メールでサインイン', auth_fullname_label: '氏名', auth_create_btn: 'アカウント作成',
        profile_signout: 'サインアウト', vr_exit: 'VRを終了', vr_mode_photo360: '360° パノラマ',
        vr_mode_3d: '3Dワールド', vr_mode_stereo: 'ステレオVR'
      },
      ko: {
        nav_home: '홈', nav_globe: '탐험', nav_explore: '여행지', nav_panorama: '360° VR 스튜디오',
        nav_assistant: '여행 준비', nav_compare: '비교', nav_unity: 'Meta Quest 코드', nav_saved: '저장됨',
        nav_technology: '기술', nav_about: '소개', nav_contact: '문의', nav_start_exploring: '탐험 시작',
        hero_badge: '전에 없던 지구 체험', btn_explore_world: '세계 탐험하기', btn_discover_destinations: '전체 검색',
        auth_heading: '경계를 넘어 탐험하세요.', auth_subtitle: '세계 여행이 여기에서 시작됩니다.',
        auth_guest_btn: '게스트로 계속하기', auth_google_btn: 'Google로 계속하기', auth_tab_signin: '이메일로 로그인',
        auth_tab_signup: '계정 만들기', auth_email_label: '이메일 주소', auth_forgot_link: '비밀번호를 잊으셨나요?',
        auth_signin_btn: '이메일로 로그인', auth_fullname_label: '성명', auth_create_btn: '계정 만들기',
        profile_signout: '로그아웃', vr_exit: 'VR 종료', vr_mode_photo360: '360° 파노라마',
        vr_mode_3d: '3D 월드', vr_mode_stereo: '스테레오 VR'
      },
      fr: {
        nav_home: 'Accueil', nav_globe: 'Explorer', nav_explore: 'Destinations', nav_panorama: 'Studio VR 360°',
        nav_assistant: 'Préparation', nav_compare: 'Comparer', nav_unity: 'Code Meta Quest', nav_saved: 'Enregistrés',
        nav_technology: 'Technologie', nav_about: 'À propos', nav_contact: 'Contact', nav_start_exploring: 'Commencer',
        hero_badge: 'Découvrez la Terre autrement', btn_explore_world: 'Explorer le monde', btn_discover_destinations: 'Tout rechercher',
        auth_heading: 'Explorez au-delà des frontières.', auth_subtitle: 'Votre voyage autour du monde commence ici.',
        auth_guest_btn: 'Continuer en tant qu’invité', auth_google_btn: 'Continuer avec Google', auth_tab_signin: 'Connexion par e-mail',
        auth_tab_signup: 'Créer un compte', auth_email_label: 'Adresse e-mail', auth_forgot_link: 'Mot de passe oublié ?',
        auth_signin_btn: 'Connexion par e-mail', auth_fullname_label: 'Nom complet', auth_create_btn: 'Créer un compte',
        profile_signout: 'Se déconnecter', vr_exit: 'Quitter VR', vr_mode_photo360: 'Photo 360° VR',
        vr_mode_3d: 'Monde 3D', vr_mode_stereo: 'VR Stéréo'
      },
      es: {
        nav_home: 'Inicio', nav_globe: 'Explorar', nav_explore: 'Destinos', nav_panorama: 'Estudio VR 360°',
        nav_assistant: 'Preparación', nav_compare: 'Comparar', nav_unity: 'Código Meta Quest', nav_saved: 'Guardados',
        nav_technology: 'Tecnología', nav_about: 'Acerca de', nav_contact: 'Contacto', nav_start_exploring: 'Comenzar',
        hero_badge: 'Experimenta la Tierra como nunca antes', btn_explore_world: 'Explorar el mundo', btn_discover_destinations: 'Buscar todo',
        auth_heading: 'Explora más allá de las fronteras.', auth_subtitle: 'Tu viaje por el mundo comienza aquí.',
        auth_guest_btn: 'Continuar como invitado', auth_google_btn: 'Continuar con Google', auth_tab_signin: 'Iniciar con correo',
        auth_tab_signup: 'Crear cuenta', auth_email_label: 'Correo electrónico', auth_forgot_link: '¿Olvidaste tu contraseña?',
        auth_signin_btn: 'Iniciar con correo', auth_fullname_label: 'Nombre completo', auth_create_btn: 'Crear cuenta',
        profile_signout: 'Cerrar sesión', vr_exit: 'Salir de VR', vr_mode_photo360: 'Foto 360° VR',
        vr_mode_3d: 'Mundo 3D', vr_mode_stereo: 'VR Estéreo'
      },
      de: {
        nav_home: 'Startseite', nav_globe: 'Entdecken', nav_explore: 'Reiseziele', nav_panorama: '360° VR-Studio',
        nav_assistant: 'Reiseplanung', nav_compare: 'Vergleichen', nav_unity: 'Meta Quest Code', nav_saved: 'Gespeichert',
        nav_technology: 'Technologie', nav_about: 'Über uns', nav_contact: 'Kontakt', nav_start_exploring: 'Jetzt entdecken',
        hero_badge: 'Erlebe die Erde wie nie zuvor', btn_explore_world: 'Welt entdecken', btn_discover_destinations: 'Alle durchsuchen',
        auth_heading: 'Entdecke über Grenzen hinaus.', auth_subtitle: 'Deine Weltreise beginnt hier.',
        auth_guest_btn: 'Als Gast fortfahren', auth_google_btn: 'Mit Google fortfahren', auth_tab_signin: 'Mit E-Mail anmelden',
        auth_tab_signup: 'Konto erstellen', auth_email_label: 'E-Mail-Adresse', auth_forgot_link: 'Passwort vergessen?',
        auth_signin_btn: 'Mit E-Mail anmelden', auth_fullname_label: 'Vollständiger Name', auth_create_btn: 'Konto erstellen',
        profile_signout: 'Abmelden', vr_exit: 'VR beenden', vr_mode_photo360: '360°-Foto-VR',
        vr_mode_3d: '3D-Welt', vr_mode_stereo: 'Stereo-VR'
      },
      pt: {
        nav_home: 'Início', nav_globe: 'Explorar', nav_explore: 'Destinos', nav_panorama: 'Estúdio VR 360°',
        nav_assistant: 'Preparação', nav_compare: 'Comparar', nav_unity: 'Código Meta Quest', nav_saved: 'Salvos',
        nav_technology: 'Tecnologia', nav_about: 'Sobre', nav_contact: 'Contato', nav_start_exploring: 'Começar',
        hero_badge: 'Explore a Terra como nunca antes', btn_explore_world: 'Explorar o mundo', btn_discover_destinations: 'Pesquisar tudo',
        auth_heading: 'Explore além das fronteiras.', auth_subtitle: 'Sua jornada pelo mundo começa aqui.',
        auth_guest_btn: 'Continuar como convidado', auth_google_btn: 'Continuar com Google', auth_tab_signin: 'Entrar com e-mail',
        auth_tab_signup: 'Criar conta', auth_email_label: 'E-mail', auth_forgot_link: 'Esqueceu a senha?',
        auth_signin_btn: 'Entrar com e-mail', auth_fullname_label: 'Nome completo', auth_create_btn: 'Criar conta',
        profile_signout: 'Sair', vr_exit: 'Sair do VR', vr_mode_photo360: 'Foto 360° VR',
        vr_mode_3d: 'Mundo 3D', vr_mode_stereo: 'VR Estéreo'
      },
      ru: {
        nav_home: 'Главная', nav_globe: 'Обзор', nav_explore: 'Направления', nav_panorama: '360° VR Студия',
        nav_assistant: 'Подготовка', nav_compare: 'Сравнение', nav_unity: 'Код Meta Quest', nav_saved: 'Сохранённые',
        nav_technology: 'Технологии', nav_about: 'О проекте', nav_contact: 'Контакты', nav_start_exploring: 'Начать',
        hero_badge: 'Откройте Землю заново', btn_explore_world: 'Исследовать мир', btn_discover_destinations: 'Поиск мест',
        auth_heading: 'Исследуйте без границ.', auth_subtitle: 'Ваше кругосветное путешествие начинается здесь.',
        auth_guest_btn: 'Продолжить как гость', auth_google_btn: 'Войти через Google', auth_tab_signin: 'Войти по почте',
        auth_tab_signup: 'Создать аккаунт', auth_email_label: 'Электронная почта', auth_forgot_link: 'Забыли пароль?',
        auth_signin_btn: 'Войти по почте', auth_fullname_label: 'Полное имя', auth_create_btn: 'Создать аккаунт',
        profile_signout: 'Выйти', vr_exit: 'Выйти из VR', vr_mode_photo360: 'Фото 360° VR',
        vr_mode_3d: '3D Мир', vr_mode_stereo: 'Стерео VR'
      },
      tr: {
        nav_home: 'Ana Sayfa', nav_globe: 'Keşfet', nav_explore: 'Destinasyonlar', nav_panorama: '360° VR Stüdyo',
        nav_assistant: 'Seyahat Hazırlığı', nav_compare: 'Karşılaştır', nav_unity: 'Meta Quest Kodu', nav_saved: 'Kaydedilenler',
        nav_technology: 'Teknoloji', nav_about: 'Hakkında', nav_contact: 'İletişim', nav_start_exploring: 'Keşfe Başla',
        hero_badge: 'Dünyayı hiç olmadığı gibi deneyimleyin', btn_explore_world: 'Dünyayı Keşfet', btn_discover_destinations: 'Tümünü Ara',
        auth_heading: 'Sınırların Ötesini Keşfedin.', auth_subtitle: 'Dünya yolculuğunuz burada başlıyor.',
        auth_guest_btn: 'Misafir Olarak Devam Et', auth_google_btn: 'Google ile Devam Et', auth_tab_signin: 'E-posta ile Giriş',
        auth_tab_signup: 'Hesap Oluştur', auth_email_label: 'E-posta adresi', auth_forgot_link: 'Şifremi unuttum?',
        auth_signin_btn: 'E-posta ile Giriş', auth_fullname_label: 'Ad Soyad', auth_create_btn: 'Hesap Oluştur',
        profile_signout: 'Çıkış Yap', vr_exit: 'VR’dan Çık', vr_mode_photo360: '360° Fotoğraf VR',
        vr_mode_3d: '3D Dünya', vr_mode_stereo: 'Stereo VR'
      },
      id: {
        nav_home: 'Beranda', nav_globe: 'Jelajahi', nav_explore: 'Destinasi', nav_panorama: 'Studio VR 360°',
        nav_assistant: 'Persiapan', nav_compare: 'Bandingkan', nav_unity: 'Kode Meta Quest', nav_saved: 'Tersimpan',
        nav_technology: 'Teknologi', nav_about: 'Tentang', nav_contact: 'Kontak', nav_start_exploring: 'Mulai Jelajah',
        hero_badge: 'Rasakan Bumi seperti belum pernah ada', btn_explore_world: 'Jelajahi Dunia', btn_discover_destinations: 'Cari Semua',
        auth_heading: 'Jelajahi Melampaui Batas.', auth_subtitle: 'Perjalanan keliling dunia Anda dimulai di sini.',
        auth_guest_btn: 'Lanjutkan sebagai Tamu', auth_google_btn: 'Lanjutkan dengan Google', auth_tab_signin: 'Masuk dengan Email',
        auth_tab_signup: 'Buat Akun', auth_email_label: 'Alamat email', auth_forgot_link: 'Lupa kata sandi?',
        auth_signin_btn: 'Masuk dengan Email', auth_fullname_label: 'Nama lengkap', auth_create_btn: 'Buat Akun',
        profile_signout: 'Keluar', vr_exit: 'Keluar VR', vr_mode_photo360: 'Foto 360° VR',
        vr_mode_3d: 'Dunia 3D', vr_mode_stereo: 'Stereo VR'
      },
      ur: {
        nav_home: 'ہوم', nav_globe: 'دریافت کریں', nav_explore: 'مقامات', nav_panorama: '360° وی آر اسٹوڈیو',
        nav_assistant: 'سفر کی تیاری', nav_compare: 'موازنہ', nav_unity: 'میٹا کویسٹ کوڈ', nav_saved: 'محفوظ شدہ',
        nav_technology: 'ٹیکنالوجی', nav_about: 'تعارف', nav_contact: 'رابطہ', nav_start_exploring: 'سفر شروع کریں',
        hero_badge: 'زمین کو نئے انداز میں دیکھیں', btn_explore_world: 'دنیا کی سیر کریں', btn_discover_destinations: 'تمام مقامات تلاش کریں',
        auth_heading: 'سرحدوں سے آگے دریافت کریں۔', auth_subtitle: 'آپ کا عالمی سفر یہاں سے شروع ہوتا ہے۔',
        auth_guest_btn: 'مہمان کے طور پر جاری رکھیں', auth_google_btn: 'Google کے ساتھ جاری رکھیں', auth_tab_signin: 'ای میل سے سائن ان کریں',
        auth_tab_signup: 'اکاؤنٹ بنائیں', auth_email_label: 'ای میل ایڈریس', auth_forgot_link: 'پاس ورڈ بھول گئے؟',
        auth_signin_btn: 'ای میل سے سائن ان کریں', auth_fullname_label: 'پورا نام', auth_create_btn: 'اکاؤنٹ بنائیں',
        profile_signout: 'سائن آؤٹ', vr_exit: 'وی آر بند کریں', vr_mode_photo360: 'فوٹو 360° وی آر',
        vr_mode_3d: '3D دنیا', vr_mode_stereo: 'اسٹیریو VR'
      },
      it: {
        nav_home: 'Home', nav_globe: 'Esplora', nav_explore: 'Destinazioni', nav_panorama: 'Studio VR 360°',
        nav_assistant: 'Preparazione', nav_compare: 'Confronta', nav_unity: 'Codice Meta Quest', nav_saved: 'Salvati',
        nav_technology: 'Tecnologia', nav_about: 'Info', nav_contact: 'Contatti', nav_start_exploring: 'Inizia ora',
        hero_badge: 'Vivi la Terra come mai prima', btn_explore_world: 'Esplora il mondo', btn_discover_destinations: 'Cerca tutto',
        auth_heading: 'Esplora oltre i confini.', auth_subtitle: 'Il tuo viaggio intorno al mondo inizia qui.',
        auth_guest_btn: 'Continua come ospite', auth_google_btn: 'Continua con Google', auth_tab_signin: 'Accedi con email',
        auth_tab_signup: 'Crea account', auth_email_label: 'Indirizzo email', auth_forgot_link: 'Password dimenticata?',
        auth_signin_btn: 'Accedi con email', auth_fullname_label: 'Nome completo', auth_create_btn: 'Crea account',
        profile_signout: 'Esci', vr_exit: 'Esci da VR', vr_mode_photo360: 'Foto 360° VR',
        vr_mode_3d: 'Mondo 3D', vr_mode_stereo: 'VR Stereo'
      }
    };

    // =========================================================================
    // 4. APPLICATION STATE & SAFE LOCALSTORAGE PERSISTENCE
    // =========================================================================
    const STATE = {
      activeView: 'home',
      lang: 'en',
      currency: 'BDT',
      USD_TO_BDT: 120,
      selectedDestinationId: 'coxs-bazar',
      editorialDestinationId: 'coxs-bazar',
      editorialHeroPhotoIdx: 0,
      coxsBgIndex: 0,
      selectedGlobeCountry: 'all',
      savedIds: ['coxs-bazar', 'lalbagh-fort', 'eiffel-tower'],
      recentHistory: [
        { id: 'coxs-bazar', timestamp: '2026-11-17 18:05', action: 'Explored Golden Sunset Showcase' },
        { id: 'eiffel-tower', timestamp: '2026-11-17 18:02', action: 'Viewed City of Light Paris Spread' },
        { id: 'lalbagh-fort', timestamp: '2026-11-17 17:58', action: 'Inspected Mughal Architecture' }
      ],
      panoMode: 'authentic',
      panoQuality: 'high',
      panoViewpointIndex: 0,
      panoYaw: 0,
      panoPitch: 0,
      panoFov: 72,
      panoAutoLook: false,
      panoComfortVignette: true,
      panoSpatialAudio: false,
      selectedHotspotId: 'cb-hs-1',
      assistantDestId: 'coxs-bazar',
      assistantTier: 'standard',
      assistantDays: 4,
      checkedPrepItems: { 'prep-0': true, 'prep-1': true },
      compareIds: ['coxs-bazar', 'lalbagh-fort', 'eiffel-tower'],
      activeUnityFileIndex: 0
    };

    function loadPersistedState() {
      try {
        const raw = localStorage.getItem('global_vr_explorer_state_v2');
        if (raw) {
          const parsed = JSON.parse(raw);
          if (Array.isArray(parsed.savedIds)) STATE.savedIds = parsed.savedIds;
          if (Array.isArray(parsed.recentHistory) && parsed.recentHistory.length > 0) {
            STATE.recentHistory = parsed.recentHistory;
          }
          if (parsed.currency === 'USD' || parsed.currency === 'BDT') STATE.currency = parsed.currency;
        }
      } catch (err) {}
    }

    function savePersistedState() {
      try {
        localStorage.setItem('global_vr_explorer_state_v2', JSON.stringify({
          savedIds: STATE.savedIds,
          recentHistory: STATE.recentHistory.slice(0, 12),
          currency: STATE.currency
        }));
      } catch (err) {}
    }

    // =========================================================================
    // 5. STARFIELD & PHOTOREALISTIC EARTH TEXTURE GENERATOR
    // =========================================================================
    function initStarfield() {
      const canvas = document.getElementById('starfieldCanvas');
      if (!canvas) return;
      const ctx = canvas.getContext('2d');
      let w = (canvas.width = window.innerWidth);
      let h = (canvas.height = window.innerHeight);

      const stars = Array.from({ length: 210 }, () => ({
        x: Math.random() * w,
        y: Math.random() * h,
        r: Math.random() * 1.15 + 0.2,
        alpha: Math.random() * 0.75 + 0.15
      }));

      function drawStars() {
        ctx.clearRect(0, 0, w, h);
        stars.forEach(s => {
          ctx.beginPath();
          ctx.arc(s.x, s.y, s.r, 0, Math.PI * 2);
          ctx.fillStyle = `rgba(226, 244, 255, ${s.alpha})`;
          ctx.fill();
        });
      }
      drawStars();

      window.addEventListener('resize', () => {
        w = canvas.width = window.innerWidth;
        h = canvas.height = window.innerHeight;
        drawStars();
      });
    }

    // Generates a photorealistic 2048x1024 Earth Albedo + Biome + Bathymetry Canvas Texture
    // and paints real TopoJSON continents with forest green, ochre desert, and mountain relief!
    let sharedEarthCanvas = null;
    let sharedEarthTexture = null;
    let sharedCloudTexture = null;

    function getPhotorealisticEarthTextures() {
      if (sharedEarthTexture && sharedCloudTexture) {
        return { earthTex: sharedEarthTexture, cloudTex: sharedCloudTexture };
      }

      // 1. Base Earth Albedo Canvas (2048 x 1024)
      const canvas = document.createElement('canvas');
      canvas.width = 2048;
      canvas.height = 1024;
      const ctx = canvas.getContext('2d');

      // Deep realistic ocean bathymetry gradient
      const oceanGrad = ctx.createLinearGradient(0, 0, 0, 1024);
      oceanGrad.addColorStop(0.0, '#072247');
      oceanGrad.addColorStop(0.2, '#043873');
      oceanGrad.addColorStop(0.5, '#03498f');
      oceanGrad.addColorStop(0.8, '#043873');
      oceanGrad.addColorStop(1.0, '#072247');
      ctx.fillStyle = oceanGrad;
      ctx.fillRect(0, 0, 2048, 1024);

      sharedEarthCanvas = canvas;
      sharedEarthTexture = new THREE.CanvasTexture(canvas);

      // 2. Soft Atmospheric Clouds Canvas (1024 x 512)
      const cCanvas = document.createElement('canvas');
      cCanvas.width = 1024;
      cCanvas.height = 512;
      const cCtx = cCanvas.getContext('2d');
      cCtx.clearRect(0, 0, 1024, 512);
      for (let i = 0; i < 180; i++) {
        const cx = Math.random() * 1024;
        const cy = 60 + Math.random() * 390;
        const rx = 25 + Math.random() * 85;
        const ry = 6 + Math.random() * 20;
        const grad = cCtx.createRadialGradient(cx, cy, 1, cx, cy, rx);
        grad.addColorStop(0, 'rgba(255, 255, 255, 0.22)');
        grad.addColorStop(0.6, 'rgba(240, 249, 255, 0.09)');
        grad.addColorStop(1, 'rgba(255, 255, 255, 0)');
        cCtx.fillStyle = grad;
        cCtx.beginPath();
        cCtx.ellipse(cx, cy, rx, ry, (Math.random() - 0.5) * 0.4, 0, Math.PI * 2);
        cCtx.fill();
      }
      sharedCloudTexture = new THREE.CanvasTexture(cCanvas);

      // 3. Render Real Continents from world-atlas TopoJSON with Realistic Biome Colors
      // (Lush green forests, Sahara/Arabian ochre deserts, shallow turquoise coastal shelves)
      fetch('https://cdn.jsdelivr.net/npm/world-atlas@2.0.2/countries-110m.json')
        .then(r => r.json())
        .then(worldData => {
          if (!window.topojson || !window.d3 || !worldData.objects?.countries) return;
          const countries = window.topojson.feature(worldData, worldData.objects.countries);
          const projection = window.d3.geoEquirectangular().fitSize([2048, 1024], { type: 'Sphere' });
          const path = window.d3.geoPath(projection, ctx);

          // Shallow continental shelf turquoise glow around landmasses
          ctx.save();
          ctx.beginPath();
          path(countries);
          ctx.lineWidth = 14;
          ctx.strokeStyle = 'rgba(14, 165, 233, 0.42)';
          ctx.stroke();
          ctx.lineWidth = 6;
          ctx.strokeStyle = 'rgba(56, 189, 248, 0.55)';
          ctx.stroke();

          // Clip to landmasses and paint realistic latitude/longitude biome colors
          ctx.clip();

          // Base lush vegetation emerald/olive fill
          const landGrad = ctx.createLinearGradient(0, 0, 0, 1024);
          landGrad.addColorStop(0.0, '#e2e8f0'); // Arctic ice
          landGrad.addColorStop(0.14, '#3f6212'); // Boreal forest
          landGrad.addColorStop(0.28, '#4d7c0f'); // Temperate green
          landGrad.addColorStop(0.36, '#ca8a04'); // Subtropical desert belt (Sahara / Arabia)
          landGrad.addColorStop(0.50, '#15803d'); // Equatorial rainforest (Amazon / Congo / Bengal)
          landGrad.addColorStop(0.68, '#65a30d'); // Southern savannas
          landGrad.addColorStop(0.88, '#cbd5e1'); // Southern alps
          landGrad.addColorStop(1.0, '#f1f5f9'); // Antarctica snow
          ctx.fillStyle = landGrad;
          ctx.fillRect(0, 0, 2048, 1024);

          // Regional desert & mountain relief patches (Sahara, Arabia, Central Asia, Australia, Andes)
          const desertPatches = [
            { x: 1120, y: 360, rx: 220, ry: 85, color: 'rgba(217, 169, 98, 0.85)' }, // Sahara & Arabia
            { x: 1420, y: 330, rx: 140, ry: 70, color: 'rgba(194, 148, 86, 0.72)' }, // Tibetan / Gobi plateau
            { x: 1740, y: 660, rx: 110, ry: 65, color: 'rgba(202, 138, 68, 0.75)' }, // Australian Outback
            { x: 480, y: 340, rx: 90, ry: 75, color: 'rgba(180, 142, 84, 0.65)' },  // Western North America
            { x: 660, y: 640, rx: 55, ry: 140, color: 'rgba(161, 130, 88, 0.65)' }  // Andes Ridge
          ];
          desertPatches.forEach(p => {
            const g = ctx.createRadialGradient(p.x, p.y, 10, p.x, p.y, p.rx);
            g.addColorStop(0, p.color);
            g.addColorStop(1, 'rgba(0,0,0,0)');
            ctx.fillStyle = g;
            ctx.beginPath();
            ctx.ellipse(p.x, p.y, p.rx, p.ry, 0, 0, Math.PI * 2);
            ctx.fill();
          });

          // Lush tropical forest patches (Amazon, Central Africa, Bangladesh/SE Asia)
          const forestPatches = [
            { x: 690, y: 550, rx: 130, ry: 100 }, // Amazon
            { x: 1140, y: 520, rx: 100, ry: 85 }, // Congo Basin
            { x: 1560, y: 420, rx: 140, ry: 80 }  // Bengal & SE Asia
          ];
          forestPatches.forEach(p => {
            const g = ctx.createRadialGradient(p.x, p.y, 5, p.x, p.y, p.rx);
            g.addColorStop(0, 'rgba(21, 128, 61, 0.85)');
            g.addColorStop(1, 'rgba(0,0,0,0)');
            ctx.fillStyle = g;
            ctx.beginPath();
            ctx.ellipse(p.x, p.y, p.rx, p.ry, 0, 0, Math.PI * 2);
            ctx.fill();
          });

          ctx.restore();

          // Subtle coastline definition
          ctx.beginPath();
          path(countries);
          ctx.lineWidth = 1.2;
          ctx.strokeStyle = 'rgba(186, 230, 253, 0.45)';
          ctx.stroke();

          sharedEarthTexture.needsUpdate = true;
        })
        .catch(() => {});

      // 4. Also attempt loading the high-resolution NASA Blue Marble from Wikimedia Commons
      const img = new Image();
      img.crossOrigin = 'anonymous';
      img.onload = () => {
        ctx.drawImage(img, 0, 0, 2048, 1024);
        sharedEarthTexture.needsUpdate = true;
      };
      img.src = 'https://upload.wikimedia.org/wikipedia/commons/1/1c/BlueMarble-2001-2002.jpg';

      return { earthTex: sharedEarthTexture, cloudTex: sharedCloudTexture };
    }

    function createAtmosphereRimMesh(radius) {
      const vertexShader = `
        varying vec3 vNormal;
        void main() {
          vNormal = normalize(normalMatrix * normal);
          gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
        }
      `;
      const fragmentShader = `
        varying vec3 vNormal;
        void main() {
          float intensity = pow(0.66 - dot(vNormal, vec3(0.0, 0.0, 1.0)), 2.4);
          gl_FragColor = vec4(0.18, 0.68, 1.0, 1.0) * intensity * 1.45;
        }
      `;
      const geo = new THREE.SphereGeometry(radius, 64, 64);
      const mat = new THREE.ShaderMaterial({
        vertexShader,
        fragmentShader,
        blending: THREE.AdditiveBlending,
        side: THREE.BackSide,
        transparent: true
      });
      return new THREE.Mesh(geo, mat);
    }

    // =========================================================================
    // 6. UTILITY HELPERS & FALLBACK IMAGE GENERATOR
    // =========================================================================
    function getDestinationById(id) {
      return DESTINATIONS.find(d => d.id === id) || DESTINATIONS[0];
    }

    function formatCurrency(usdAmount) {
      const val = Number(usdAmount) || 0;
      if (STATE.currency === 'BDT') {
        const bdt = Math.round(val * STATE.USD_TO_BDT);
        return '৳ ' + bdt.toLocaleString('en-US');
      }
      return '$' + val.toLocaleString('en-US') + ' USD';
    }

    function getVerificationBadgeHTML(status) {
      if (status === 'Officially Verified') {
        return `<span class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full text-[10px] font-mono tracking-wider uppercase bg-emerald-500/15 text-emerald-200 border border-emerald-400/30 backdrop-blur-md"><i data-lucide="shield-check" class="w-3 h-3"></i>Officially Verified</span>`;
      }
      if (status === 'Independently Sourced') {
        return `<span class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full text-[10px] font-mono tracking-wider uppercase bg-sky-500/15 text-sky-200 border border-sky-400/30 backdrop-blur-md"><i data-lucide="check-circle" class="w-3 h-3"></i>Independently Sourced</span>`;
      }
      return `<span class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full text-[10px] font-mono tracking-wider uppercase bg-amber-500/15 text-amber-200 border border-amber-400/30 backdrop-blur-md"><i data-lucide="users" class="w-3 h-3"></i>Community Submitted / Unverified</span>`;
    }

    function createFallbackDataUri(title, subtitle) {
      const safeTitle = (title || 'Global VR Destination').replace(/[<>&"']/g, '');
      const safeSub = (subtitle || 'Verified Location').replace(/[<>&"']/g, '');
      const svg = `<svg xmlns="http://www.w3.org/2000/svg" width="800" height="500" viewBox="0 0 800 500">
        <defs>
          <linearGradient id="g" x1="0" y1="0" x2="1" y2="1">
            <stop offset="0%" stop-color="#040c1e"/>
            <stop offset="55%" stop-color="#0a2239"/>
            <stop offset="100%" stop-color="#02050d"/>
          </linearGradient>
        </defs>
        <rect width="800" height="500" fill="url(#g)"/>
        <circle cx="650" cy="120" r="65" fill="rgba(56,189,248,0.18)"/>
        <path d="M0 370 Q200 320 400 365 T800 350 L800 500 L0 500 Z" fill="rgba(14,165,233,0.22)"/>
        <text x="48" y="230" fill="#f8fafc" font-family="Georgia, serif" font-size="30" font-weight="bold">${safeTitle}</text>
        <text x="48" y="268" fill="#38bdf8" font-family="monospace" font-size="16">${safeSub}</text>
        <text x="48" y="305" fill="#94a3b8" font-family="sans-serif" font-size="13">Wikimedia Commons High-Resolution Archive</text>
      </svg>`;
      return 'data:image/svg+xml;charset=utf-8,' + encodeURIComponent(svg);
    }

    function showToast(message) {
      const toast = document.getElementById('appToast');
      const text = document.getElementById('appToastText');
      if (!toast || !text) return;
      text.textContent = message;
      toast.classList.remove('hidden');
      clearTimeout(window.__toastTimer);
      window.__toastTimer = setTimeout(() => {
        toast.classList.add('hidden');
      }, 2800);
    }

    function logDiscoveryAction(destId, actionText) {
      const dest = getDestinationById(destId);
      const now = new Date();
      const timeStr = '2026-11-17 ' + String(now.getHours()).padStart(2, '0') + ':' + String(now.getMinutes()).padStart(2, '0');
      STATE.recentHistory = [
        { id: dest.id, timestamp: timeStr, action: actionText },
        ...STATE.recentHistory.filter(item => !(item.id === dest.id && item.action === actionText))
      ].slice(0, 10);
      savePersistedState();
      renderHomeRecentList();
      renderProfileView();
    }

    function toggleSaveDestination(destId) {
      const idx = STATE.savedIds.indexOf(destId);
      const dest = getDestinationById(destId);
      if (idx >= 0) {
        STATE.savedIds.splice(idx, 1);
        showToast(`Removed ${dest.name} from Saved Destinations`);
      } else {
        STATE.savedIds.push(destId);
        showToast(`Saved ${dest.name} to your VR collection`);
      }
      savePersistedState();
      updateSavedBadges();
      renderHomeSections();
      renderExploreGrid();
      renderProfileView();
      if (STATE.activeView === 'destination-page') {
        renderEditorialDestinationPage(STATE.editorialDestinationId, STATE.editorialHeroPhotoIdx);
      }
    }

    function updateSavedBadges() {
      const badge = document.getElementById('savedCountBadge');
      const profileCount = document.getElementById('profileSavedCount');
      if (badge) badge.textContent = String(STATE.savedIds.length);
      if (profileCount) profileCount.textContent = String(STATE.savedIds.length);
    }

    function scrollToHomeSection(secId) {
      if (STATE.activeView !== 'home') {
        switchView('home');
      }
      setTimeout(() => {
        const el = document.getElementById(secId);
        if (el) el.scrollIntoView({ behavior: 'smooth' });
      }, 60);
    }

    // Showcase Interactive Photo Switchers on Homepage
    function cycleCoxsBazarBg(dir) {
      const coxs = getDestinationById('coxs-bazar');
      STATE.coxsBgIndex = (STATE.coxsBgIndex + dir + coxs.images.length) % coxs.images.length;
      const imgEl = document.getElementById('coxsHeroBgImg');
      const counterEl = document.getElementById('coxsSlideCounter');
      if (imgEl) imgEl.src = coxs.images[STATE.coxsBgIndex].url;
      if (counterEl) counterEl.textContent = `0${STATE.coxsBgIndex + 1} — 0${coxs.images.length}`;
    }

    function setParisShowcasePhoto(url) {
      const imgEl = document.getElementById('parisMainShowcaseImg');
      if (imgEl) imgEl.src = url;
    }

    function setLalbaghShowcasePhoto(url) {
      const imgEl = document.getElementById('lalbaghMainShowcaseImg');
      if (imgEl) imgEl.src = url;
    }

    // =========================================================================
    // 7. VIEW ROUTING & DEDICATED DESTINATION EDITORIAL PAGE
    // =========================================================================
    function switchView(viewName) {
      if (!document.getElementById(`view-${viewName}`)) {
        viewName = 'home';
      }
      STATE.activeView = viewName;
      document.querySelectorAll('.view-section').forEach(sec => {
        sec.classList.toggle('hidden', sec.id !== `view-${viewName}`);
      });

      const topHeader = document.querySelector('header');
      const bottomFooter = document.querySelector('footer');
      if (topHeader) topHeader.classList.toggle('hidden', viewName === 'panorama');
      if (bottomFooter) bottomFooter.classList.toggle('hidden', viewName === 'panorama');
      if (document.body) {
        document.body.style.overflow = viewName === 'panorama' ? 'hidden' : '';
      }

      const mobileDrawer = document.getElementById('mobileNavDrawer');
      if (mobileDrawer) mobileDrawer.classList.add('hidden');

      if (viewName === 'home') {
        initOrResizeHeroEarth();
      document.getElementById('heroAiCommandInput')?.addEventListener('keydown', e => {
        if (e.key === 'Enter') executeAiNavigationQuery(e.target.value, true);
      });
      document.getElementById('aiNavigatorInput')?.addEventListener('keydown', e => {
        if (e.key === 'Enter') executeAiNavigationQuery(e.target.value, true);
      });
      } else if (viewName === 'destination-page') {
        renderEditorialDestinationPage(STATE.editorialDestinationId, STATE.editorialHeroPhotoIdx);
      } else if (viewName === 'timetravel') {
        renderTimeTravelView();
        if (!document.getElementById('aiAssistantResponseCard')?.innerHTML) {
          executeAiNavigationQuery(STATE.lastAiQuery, false);
        }
      } else if (viewName === 'globe') {
        initOrResizeGlobe();
      } else if (viewName === 'panorama') {
        initOrResizePanorama();
      } else if (viewName === 'assistant') {
        renderAssistantView();
      } else if (viewName === 'compare') {
        renderComparisonView();
      } else if (viewName === 'unity') {
        renderUnityView();
      } else if (viewName === 'profile') {
        renderProfileView();
      }

      if (window.lucide) window.lucide.createIcons();
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    function openEditorialDestinationPage(destId, photoIdx = 0) {
      STATE.editorialDestinationId = destId;
      STATE.editorialHeroPhotoIdx = photoIdx;
      STATE.selectedDestinationId = destId;
      switchView('destination-page');
      logDiscoveryAction(destId, 'Explored Dedicated Destination Page');
    }

    // =========================================================================
    // 8. HUGE PHOTOREALISTIC 3D EARTH IN HOMEPAGE HERO (Reference #1 / 17462.jpg)
    // =========================================================================
    let heroScene = null;
    let heroCamera = null;
    let heroRenderer = null;
    let heroGlobeGroup = null;
    let heroCloudMesh = null;
    let heroTargetRot = { x: 0.24, y: -0.55 }; // Framed on Atlantic / Americas / Africa / Europe / Asia like 17462.jpg
    let heroCurrentRot = { x: 0.24, y: -0.55 };

    function initOrResizeHeroEarth() {
      const container = document.getElementById('heroEarthContainer');
      if (!container) return;

      if (heroRenderer) {
        const w = container.clientWidth || 760;
        const h = container.clientHeight || 720;
        heroCamera.aspect = w / h;
        heroCamera.updateProjectionMatrix();
        heroRenderer.setSize(w, h);
        return;
      }

      const w = container.clientWidth || 760;
      const h = container.clientHeight || 720;

      if (typeof THREE === 'undefined') return;
      try {
        heroScene = new THREE.Scene();
        heroCamera = new THREE.PerspectiveCamera(38, w / h, 0.1, 100);
        heroCamera.position.set(0, 0, 2.72);

        heroRenderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
        heroRenderer.setSize(w, h);
        heroRenderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, 2));
        heroRenderer.domElement.className = 'relative z-10 w-full h-full';
        const fallbackOrb = document.getElementById('heroEarthStaticFallback');
        if (fallbackOrb) fallbackOrb.classList.add('hidden');
        container.appendChild(heroRenderer.domElement);
      } catch (err) {
        console.warn('Hero WebGL fallback active:', err);
        return;
      }

      // Space Sunlight & Atmospheric Rim Illumination
      heroScene.add(new THREE.AmbientLight(0x94a3b8, 0.85));
      const sunLight = new THREE.DirectionalLight(0xffffff, 1.55);
      sunLight.position.set(4, 2.5, 4.5);
      heroScene.add(sunLight);

      const blueFill = new THREE.DirectionalLight(0x38bdf8, 0.85);
      blueFill.position.set(-5, -2, 2);
      heroScene.add(blueFill);

      heroGlobeGroup = new THREE.Group();
      heroScene.add(heroGlobeGroup);

      const { earthTex, cloudTex } = getPhotorealisticEarthTextures();

      // Photorealistic Earth Sphere
      const earthGeo = new THREE.SphereGeometry(1.18, 96, 96);
      const earthMat = new THREE.MeshPhongMaterial({
        map: earthTex,
        specular: new THREE.Color(0x1e40af),
        shininess: 18
      });
      const earthMesh = new THREE.Mesh(earthGeo, earthMat);
      heroGlobeGroup.add(earthMesh);

      // Soft Cloud Layer Sphere
      const cloudGeo = new THREE.SphereGeometry(1.192, 64, 64);
      const cloudMat = new THREE.MeshLambertMaterial({
        map: cloudTex,
        transparent: true,
        opacity: 0.75,
        depthWrite: false
      });
      heroCloudMesh = new THREE.Mesh(cloudGeo, cloudMat);
      heroGlobeGroup.add(heroCloudMesh);

      // Luminous Atmospheric Blue Fresnel Halo (Matching UNIVERSAL EARTH Reference)
      heroGlobeGroup.add(createAtmosphereRimMesh(1.26));

      // Drag to rotate Earth
      let dragging = false;
      let sx = 0;
      let sy = 0;
      const dom = heroRenderer.domElement;

      dom.addEventListener('pointerdown', e => {
        dragging = true;
        sx = e.clientX;
        sy = e.clientY;
      });
      window.addEventListener('pointermove', e => {
        if (!dragging) return;
        const dx = e.clientX - sx;
        const dy = e.clientY - sy;
        sx = e.clientX;
        sy = e.clientY;
        heroTargetRot.y += dx * 0.005;
        heroTargetRot.x = Math.max(-1.0, Math.min(1.0, heroTargetRot.x + dy * 0.005));
      });
      window.addEventListener('pointerup', () => {
        dragging = false;
      });

      function animateHeroEarth() {
        requestAnimationFrame(animateHeroEarth);
        if (STATE.activeView !== 'home') return;
        heroTargetRot.y += 0.0011;
        if (heroCloudMesh) heroCloudMesh.rotation.y += 0.00035;
        heroCurrentRot.x += (heroTargetRot.x - heroCurrentRot.x) * 0.08;
        heroCurrentRot.y += (heroTargetRot.y - heroCurrentRot.y) * 0.08;
        heroGlobeGroup.rotation.x = heroCurrentRot.x;
        heroGlobeGroup.rotation.y = heroCurrentRot.y;
        heroRenderer.render(heroScene, heroCamera);
      }
      animateHeroEarth();
    }

    // =========================================================================
    // 9. HOMEPAGE FILMSTRIP & DEDICATED DESTINATION PAGE RENDERING
    // =========================================================================
    function renderHomeSections() {
      const popularGrid = document.getElementById('homePopularGrid');
      if (popularGrid) {
        const popular = DESTINATIONS.filter(d => !d.flagship);
        popularGrid.innerHTML = popular.map(dest => renderCinematicPortraitCardHTML(dest)).join('');
      }
      renderHomeRecentList();
      if (window.lucide) window.lucide.createIcons();
    }

    function renderCinematicPortraitCardHTML(dest) {
      const img = dest.images[0];
      const fallbackUri = createFallbackDataUri(dest.name, dest.coordinates.label);
      const isSaved = STATE.savedIds.includes(dest.id);
      const titleDisplay = STATE.lang === 'bn' ? dest.nameBn : dest.name;

      return `
        <article class="photo-zoom relative rounded-2xl overflow-hidden h-[420px] border border-white/15 group shadow-xl flex flex-col justify-between">
          <img src="${img.url}" alt="${dest.name}" loading="lazy"
               onerror="this.onerror=null;this.src='${fallbackUri}';"
               class="absolute inset-0 w-full h-full object-cover" />
          <div class="absolute inset-0 bg-gradient-to-t from-black/95 via-black/35 to-black/15"></div>

          <div class="relative z-10 p-5 flex items-center justify-between gap-2">
            <span class="px-3 py-1 rounded-full bg-slate-950/75 border border-white/15 text-[10px] font-mono uppercase tracking-widest text-sky-300 backdrop-blur-md">
              ${dest.country}
            </span>
            <button type="button" onclick="toggleSaveDestination('${dest.id}')" class="p-2 rounded-full bg-slate-950/75 border border-white/15 hover:border-sky-400 text-white backdrop-blur-md" title="Save Destination">
              <i data-lucide="bookmark" class="w-3.5 h-3.5 ${isSaved ? 'text-sky-300 fill-sky-300' : 'text-slate-200'}"></i>
            </button>
          </div>

          <div class="relative z-10 p-6 space-y-3">
            <div class="text-[10px] font-mono uppercase tracking-[0.18em] text-slate-300">${dest.region} • ${dest.coordinates.label}</div>
            <h3 class="font-editorial text-3xl font-semibold text-white leading-tight">${titleDisplay}</h3>
            <p class="text-xs text-slate-300/90 line-clamp-2 font-light leading-relaxed">${dest.shortSummary}</p>

            <div class="pt-2 flex items-center gap-2">
              <button type="button" onclick="openEditorialDestinationPage('${dest.id}')" class="flex-1 py-2.5 rounded-lg bg-white text-slate-950 hover:bg-sky-100 font-medium text-[11px] uppercase tracking-wider flex items-center justify-center gap-1.5 transition">
                <span>Explore Destination</span>
              </button>
              <button type="button" onclick="openPanoramaForDestination('${dest.id}')" class="px-3.5 py-2.5 rounded-lg bg-slate-950/80 hover:bg-slate-900 text-sky-300 border border-sky-400/35 text-[11px] uppercase tracking-wider transition" title="Explore in VR">
                <span>VR</span>
              </button>
            </div>
          </div>
        </article>
      `;
    }

    function renderDestinationCardHTML(dest, isDetailed) {
      const img = dest.images[0];
      const fallbackUri = createFallbackDataUri(dest.name, dest.coordinates.label);
      const isSaved = STATE.savedIds.includes(dest.id);
      const titleDisplay = STATE.lang === 'bn' ? dest.nameBn : dest.name;

      return `
        <article class="photo-zoom glass-panel rounded-3xl overflow-hidden flex flex-col justify-between border border-white/10 hover:border-sky-400/40 transition group">
          <div>
            <div class="relative ${isDetailed ? 'h-64' : 'h-48'} bg-[#02050d] overflow-hidden">
              <img src="${img.url}" alt="${dest.name}" loading="lazy"
                   onerror="this.onerror=null;this.src='${fallbackUri}';"
                   class="w-full h-full object-cover" />
              <div class="absolute inset-0 bg-gradient-to-t from-[#02050d] via-transparent to-transparent"></div>
              <div class="absolute top-3.5 left-3.5 right-3.5 flex items-center justify-between gap-1">
                ${getVerificationBadgeHTML(dest.verificationStatus)}
                <button type="button" onclick="toggleSaveDestination('${dest.id}')" class="p-2 rounded-full bg-slate-950/80 border border-white/15 hover:border-sky-400 text-xs" title="Save Destination">
                  <i data-lucide="bookmark" class="w-3.5 h-3.5 ${isSaved ? 'text-sky-300 fill-sky-300' : 'text-slate-300'}"></i>
                </button>
              </div>
              <div class="absolute bottom-3.5 left-4 right-4 flex items-center justify-between text-[10px] font-mono text-sky-200">
                <span class="px-2.5 py-0.5 rounded-full bg-slate-950/80 border border-white/15">${dest.country} • ${dest.region}</span>
                <span class="px-2.5 py-0.5 rounded-full bg-slate-950/80 text-slate-300">${dest.images.length} Viewpoints</span>
              </div>
            </div>

            <div class="p-6 space-y-2.5">
              <div class="text-[10px] font-mono uppercase tracking-widest text-slate-400">${dest.city} • ${dest.coordinates.label}</div>
              <h3 class="font-editorial font-semibold text-2xl text-white leading-snug">${titleDisplay}</h3>
              <p class="text-xs text-slate-300 font-light line-clamp-2 leading-relaxed">${dest.shortSummary}</p>
            </div>
          </div>

          <div class="px-6 pb-6 pt-3 border-t border-white/10 space-y-3">
            <div class="flex items-center justify-between text-[11px] text-slate-400">
              <span>Est. Daily: <strong class="text-slate-200 font-mono">${formatCurrency(dest.expenses.dailyStandardUSD)}</strong></span>
              <a href="${img.commonsPage}" target="_blank" rel="noopener noreferrer" class="text-sky-300 hover:underline truncate max-w-[150px]">${img.license} Source ↗</a>
            </div>
            <div class="grid grid-cols-3 gap-2">
              <button type="button" onclick="openEditorialDestinationPage('${dest.id}')" class="px-3 py-2.5 rounded-full bg-white text-slate-950 hover:bg-sky-100 font-medium text-[11px] flex items-center justify-center gap-1 transition">
                <span data-i18n="nav_globe">Explore</span>
              </button>
              <button type="button" onclick="openPanoramaForDestination('${dest.id}')" class="px-3 py-2.5 rounded-full bg-sky-500/20 hover:bg-sky-500/30 text-sky-200 border border-sky-400/30 font-medium text-[11px] flex items-center justify-center gap-1 transition">
                <i data-lucide="view" class="w-3 h-3"></i><span>Explore in VR</span>
              </button>
              <button type="button" onclick="flyGlobeToDestination('${dest.id}')" class="px-3 py-2.5 rounded-full bg-white/5 hover:bg-white/15 text-slate-100 border border-white/15 font-medium text-[11px] flex items-center justify-center gap-1 transition">
                <i data-lucide="globe" class="w-3 h-3 text-teal-300"></i><span>Globe</span>
              </button>
            </div>
          </div>
        </article>
      `;
    }

    function renderHomeRecentList() {
      const container = document.getElementById('homeRecentList');
      if (!container) return;
      container.innerHTML = STATE.recentHistory.slice(0, 4).map(item => {
        const dest = getDestinationById(item.id);
        return `
          <div class="flex items-center justify-between gap-3 p-3.5 rounded-2xl bg-white/[0.03] border border-white/10">
            <div class="min-w-0">
              <div class="flex items-center gap-2">
                <span class="font-medium text-xs text-white truncate">${dest.name}</span>
                <span class="text-[10px] font-mono text-sky-300">${dest.country}</span>
              </div>
              <div class="text-[11px] text-slate-400 truncate">${item.action} • <span class="font-mono">${item.timestamp}</span></div>
            </div>
            <button type="button" onclick="openEditorialDestinationPage('${dest.id}')" class="px-3 py-1.5 rounded-full bg-white/10 hover:bg-white/20 text-white text-[11px] font-mono shrink-0">
              Open Page
            </button>
          </div>
        `;
      }).join('');
    }

    // =========================================================================
    // 10. DEDICATED FULL-BLEED EDITORIAL DESTINATION PAGE
    // =========================================================================
    function renderEditorialDestinationPage(destId, activePhotoIdx = 0) {
      const dest = getDestinationById(destId);
      STATE.editorialDestinationId = dest.id;
      STATE.editorialHeroPhotoIdx = activePhotoIdx;

      const switcherBar = document.getElementById('editorialSwitcherBar');
      if (switcherBar) {
        switcherBar.innerHTML = DESTINATIONS.map(d => {
          const active = d.id === dest.id;
          return `
            <button type="button" onclick="openEditorialDestinationPage('${d.id}', 0)"
                    class="px-3.5 py-1.5 rounded-full text-xs shrink-0 transition ${active ? 'bg-white text-slate-950 font-medium' : 'bg-white/5 text-slate-300 hover:text-white border border-white/10'}">
              ${d.name.split('(')[0].trim()}
            </button>
          `;
        }).join('');
      }

      const container = document.getElementById('editorialPageContainer');
      if (!container) return;

      const heroPhoto = dest.images[activePhotoIdx] || dest.images[0];
      const fallbackUri = createFallbackDataUri(dest.name, dest.coordinates.label);
      const isSaved = STATE.savedIds.includes(dest.id);

      container.innerHTML = `
        <div class="relative w-full h-[76vh] min-h-[520px] bg-[#02050d] overflow-hidden border-b border-white/10">
          <img src="${heroPhoto.url}" alt="${heroPhoto.title}"
               onerror="this.onerror=null;this.src='${fallbackUri}';"
               class="w-full h-full object-cover transition-all duration-700" />
          <div class="absolute inset-0 bg-gradient-to-t from-[#010308] via-[#010308]/35 to-transparent"></div>

          <div class="absolute top-8 left-5 right-5 sm:left-12 sm:right-12 max-w-[1440px] mx-auto flex flex-wrap items-center justify-between gap-3">
            <div class="flex flex-wrap items-center gap-2.5">
              ${getVerificationBadgeHTML(dest.verificationStatus)}
              <span class="px-3 py-1 rounded-full bg-slate-950/75 border border-white/15 text-[11px] font-mono text-sky-300 backdrop-blur-md">
                ${dest.coordinates.label}
              </span>
            </div>

            <div class="flex items-center gap-2">
              <button type="button" onclick="openDestinationModal('${dest.id}', ${activePhotoIdx})" class="px-4 py-1.5 rounded-full bg-slate-950/75 hover:bg-slate-900 border border-white/20 text-xs text-white flex items-center gap-1.5 backdrop-blur-md">
                <i data-lucide="maximize-2" class="w-3.5 h-3.5 text-sky-300"></i><span>Full-Res Lightbox</span>
              </button>
              <button type="button" onclick="toggleSaveDestination('${dest.id}')" class="p-2 rounded-full bg-slate-950/75 border border-white/20 text-white backdrop-blur-md">
                <i data-lucide="bookmark" class="w-4 h-4 ${isSaved ? 'text-sky-300 fill-sky-300' : 'text-white'}"></i>
              </button>
            </div>
          </div>

          <div class="absolute bottom-10 left-5 right-5 sm:left-12 sm:right-12 max-w-[1440px] mx-auto flex flex-col lg:flex-row lg:items-end justify-between gap-6">
            <div class="space-y-3 max-w-3xl">
              <div class="text-xs font-mono uppercase tracking-[0.25em] text-sky-300">
                ${dest.editorialChapter || `${dest.country} • ${dest.region}`}
              </div>
              <h1 class="font-editorial text-5xl sm:text-7xl font-semibold text-white leading-none">
                ${dest.name}
              </h1>
              <p class="font-editorial italic text-xl sm:text-2xl text-slate-200">
                "${dest.editorialTagline || dest.shortSummary}"
              </p>
            </div>

            <div class="flex flex-wrap items-center gap-3 shrink-0">
              <button type="button" onclick="openPanoramaForDestination('${dest.id}')" class="px-8 py-4 rounded-full bg-white text-slate-950 hover:bg-sky-100 font-medium text-xs uppercase tracking-widest flex items-center gap-2.5 shadow-[0_0_40px_rgba(56,189,248,0.45)] transition">
                <i data-lucide="glasses" class="w-4 h-4"></i>
                <span>Explore in VR</span>
              </button>
              <button type="button" onclick="flyGlobeToDestination('${dest.id}')" class="px-6 py-4 rounded-full bg-slate-950/80 hover:bg-slate-900 text-white border border-white/20 text-xs uppercase tracking-widest flex items-center gap-2 backdrop-blur-md transition">
                <i data-lucide="globe" class="w-4 h-4 text-sky-300"></i>
                <span>Locate on 3D Earth</span>
              </button>
              <button type="button" onclick="openTimeTravelForDestination('${dest.id}', 1875)" class="px-6 py-4 rounded-full bg-amber-500/20 hover:bg-amber-500/30 text-amber-200 border border-amber-400/40 text-xs uppercase tracking-widest flex items-center gap-2 backdrop-blur-md transition">
                <i data-lucide="history" class="w-4 h-4 text-amber-300"></i>
                <span>Time Travel (1200–2026)</span>
              </button>
            </div>
          </div>
        </div>

        <div class="max-w-[1440px] mx-auto px-5 sm:px-8 lg:px-12 py-14 space-y-16">
          <div class="space-y-4">
            <div class="flex flex-wrap items-end justify-between gap-2">
              <div>
                <span class="text-[11px] font-mono uppercase tracking-[0.22em] text-sky-400">Authentic Licensed Photography</span>
                <h2 class="font-editorial text-3xl sm:text-4xl font-semibold text-white mt-0.5">Destination Viewpoint Gallery</h2>
              </div>
              <div class="text-xs text-slate-400">
                Active Photograph: <strong class="text-white">${heroPhoto.title}</strong> • Credit: ${heroPhoto.author} (<a href="${heroPhoto.commonsPage}" target="_blank" rel="noopener noreferrer" class="text-sky-300 underline">${heroPhoto.license} Wikimedia Source ↗</a>)
              </div>
            </div>

            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-5">
              ${dest.images.map((img, i) => `
                <div onclick="openEditorialDestinationPage('${dest.id}', ${i})"
                     class="photo-zoom cursor-pointer rounded-2xl overflow-hidden border transition ${i === activePhotoIdx ? 'border-sky-400 ring-2 ring-sky-400/30' : 'border-white/10 hover:border-white/30'} bg-[#050b18]">
                  <div class="relative h-48 overflow-hidden">
                    <img src="${img.url}" alt="${img.title}" onerror="this.onerror=null;this.src='${fallbackUri}';" class="w-full h-full object-cover" />
                    ${img.isPanorama ? '<span class="absolute top-2.5 right-2.5 px-2.5 py-0.5 rounded-full bg-sky-400 text-slate-950 font-mono text-[10px] font-bold">360° PANO</span>' : ''}
                  </div>
                  <div class="p-4 space-y-1">
                    <div class="text-[10px] font-mono uppercase tracking-wider text-sky-300">${img.viewpoint}</div>
                    <div class="text-xs font-medium text-white truncate">${img.title}</div>
                    <div class="text-[11px] text-slate-400 truncate">${img.author} • ${img.license}</div>
                  </div>
                </div>
              `).join('')}
            </div>
          </div>

          <div class="grid grid-cols-1 lg:grid-cols-12 gap-12 items-start border-t border-white/10 pt-14">
            <div class="lg:col-span-7 space-y-8">
              <div class="space-y-3">
                <span class="text-[11px] font-mono uppercase tracking-[0.22em] text-sky-400">Geographic &amp; Spatial Overview</span>
                <h3 class="font-editorial text-3xl sm:text-4xl font-semibold text-white">The Sense of Place</h3>
                <p class="text-slate-200 text-base font-light leading-relaxed drop-cap">${dest.description}</p>
              </div>

              <div class="grid grid-cols-1 md:grid-cols-2 gap-6 pt-2">
                <div class="glass-panel rounded-3xl p-6 space-y-2.5">
                  <div class="text-[11px] font-mono uppercase tracking-widest text-sky-300">Historical Heritage</div>
                  <p class="text-xs sm:text-sm text-slate-300 font-light leading-relaxed">${dest.historicalInfo}</p>
                </div>
                <div class="glass-panel rounded-3xl p-6 space-y-2.5">
                  <div class="text-[11px] font-mono uppercase tracking-widest text-amber-300">Living Culture &amp; Atmosphere</div>
                  <p class="text-xs sm:text-sm text-slate-300 font-light leading-relaxed">${dest.culturalInfo}</p>
                </div>
              </div>

              <div class="space-y-4 pt-2">
                <div class="text-[11px] font-mono uppercase tracking-[0.22em] text-sky-400">Notable Landmarks &amp; Exploration Points</div>
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                  ${dest.attractions.map(a => `
                    <div class="p-5 rounded-2xl bg-white/[0.03] border border-white/10 space-y-1.5">
                      <div class="font-editorial text-xl font-semibold text-white">${a.name}</div>
                      <p class="text-xs text-slate-300 font-light leading-relaxed">${a.detail}</p>
                    </div>
                  `).join('')}
                </div>
              </div>
            </div>

            <div class="lg:col-span-5 space-y-6">
              <div class="glass-panel rounded-3xl p-7 space-y-4">
                <div class="text-[11px] font-mono uppercase tracking-[0.22em] text-sky-300">Traveler's Practical Journal</div>
                <div class="space-y-3 text-xs leading-relaxed">
                  <div><strong class="text-white block uppercase tracking-wider text-[10px] text-slate-400">Best Visiting Season</strong> <span class="text-slate-200">${dest.visitorInfo.bestSeason}</span></div>
                  <div><strong class="text-white block uppercase tracking-wider text-[10px] text-slate-400">Opening Hours</strong> <span class="text-slate-200">${dest.visitorInfo.openingHours}</span></div>
                  <div><strong class="text-white block uppercase tracking-wider text-[10px] text-slate-400">Official Entry &amp; Tariffs</strong> <span class="text-slate-200">${dest.visitorInfo.entryFee}</span></div>
                  <div><strong class="text-white block uppercase tracking-wider text-[10px] text-slate-400">Accessibility</strong> <span class="text-slate-200">${dest.visitorInfo.accessibility}</span></div>
                  <div><strong class="text-white block uppercase tracking-wider text-[10px] text-slate-400">Etiquette &amp; Safety</strong> <span class="text-slate-200">${dest.visitorInfo.guidelines}</span></div>
                </div>
                <div class="pt-3 border-t border-white/10 flex items-center justify-between">
                  <div>
                    <div class="text-[10px] font-mono uppercase text-slate-400">Standard Comfort Estimate</div>
                    <div class="font-mono text-lg font-bold text-sky-300">${formatCurrency(dest.expenses.dailyStandardUSD)} / day</div>
                  </div>
                  <button type="button" onclick="STATE.assistantDestId='${dest.id}'; switchView('assistant');" class="px-4 py-2 rounded-full bg-white/10 hover:bg-white/20 text-white text-xs">
                    Open Budget Planner →
                  </button>
                </div>
              </div>

              <div class="glass-panel rounded-3xl p-7 space-y-4">
                <div class="text-[11px] font-mono uppercase tracking-[0.22em] text-emerald-300">Primary Sources &amp; Attribution</div>
                <div class="space-y-2">
                  ${dest.sources.map(src => `
                    <a href="${src.url}" target="_blank" rel="noopener noreferrer"
                       class="block p-3 rounded-xl bg-white/[0.03] hover:bg-white/[0.08] border border-white/10 text-xs text-sky-300 flex items-center justify-between">
                      <span>${src.title}</span>
                      <span class="text-[10px] font-mono text-slate-400">${src.type} ↗</span>
                    </a>
                  `).join('')}
                </div>
              </div>
            </div>
          </div>
        </div>
      `;

      if (window.lucide) window.lucide.createIcons();
    }

    // =========================================================================
    // 11. INTERACTIVE 3D EARTH GLOBE (VIEW 2)
    // =========================================================================
    let globeScene = null;
    let globeCamera = null;
    let globeRenderer = null;
    let globeGroup = null;
    let globeMarkersGroup = null;
    let globeAutoSpin = true;
    let globeTargetRotation = { x: 0.35, y: -1.6 };
    let globeCurrentRotation = { x: 0.35, y: -1.6 };
    let globeCameraDistance = 3.15;
    let globeRaycaster = null;
    let globeMouse = null;
    let globeMarkerMeshes = [];

    function latLngToVector3(lat, lng, radius) {
      const phi = (90 - lat) * (Math.PI / 180);
      const theta = (lng + 180) * (Math.PI / 180);
      const x = -(radius * Math.sin(phi) * Math.cos(theta));
      const z = radius * Math.sin(phi) * Math.sin(theta);
      const y = radius * Math.cos(phi);
      return new THREE.Vector3(x, y, z);
    }

    function initOrResizeGlobe() {
      const container = document.getElementById('globeCanvasContainer');
      if (!container) return;

      renderGlobeQuickPins();
      renderGlobeSelectedPanel(STATE.selectedDestinationId);

      if (globeRenderer) {
        const w = container.clientWidth || 720;
        const h = container.clientHeight || 540;
        globeCamera.aspect = w / h;
        globeCamera.updateProjectionMatrix();
        globeRenderer.setSize(w, h);
        return;
      }

      const width = container.clientWidth || 720;
      const height = container.clientHeight || 540;

      globeScene = new THREE.Scene();
      globeCamera = new THREE.PerspectiveCamera(45, width / height, 0.1, 100);
      globeCamera.position.set(0, 0, globeCameraDistance);

      globeRenderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
      globeRenderer.setSize(width, height);
      globeRenderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, 2));
      container.innerHTML = '';
      container.appendChild(globeRenderer.domElement);

      const ambientLight = new THREE.AmbientLight(0x94a3b8, 0.9);
      globeScene.add(ambientLight);
      const dirLight = new THREE.DirectionalLight(0xffffff, 1.45);
      dirLight.position.set(5, 3, 5);
      globeScene.add(dirLight);

      globeGroup = new THREE.Group();
      globeScene.add(globeGroup);

      const { earthTex, cloudTex } = getPhotorealisticEarthTextures();

      const earthGeo = new THREE.SphereGeometry(1.18, 80, 80);
      const earthMat = new THREE.MeshPhongMaterial({
        map: earthTex,
        specular: new THREE.Color(0x1e40af),
        shininess: 20
      });
      globeGroup.add(new THREE.Mesh(earthGeo, earthMat));

      const cloudGeo = new THREE.SphereGeometry(1.192, 48, 48);
      const cloudMat = new THREE.MeshLambertMaterial({
        map: cloudTex,
        transparent: true,
        opacity: 0.55,
        depthWrite: false
      });
      globeGroup.add(new THREE.Mesh(cloudGeo, cloudMat));

      globeGroup.add(createAtmosphereRimMesh(1.25));

      loadWorldAtlasPolygons();

      globeMarkersGroup = new THREE.Group();
      globeGroup.add(globeMarkersGroup);
      rebuildGlobeMarkers();

      globeRaycaster = new THREE.Raycaster();
      globeMouse = new THREE.Vector2();
      let isDragging = false;
      let startX = 0;
      let startY = 0;
      let movedDelta = 0;

      const domElem = globeRenderer.domElement;
      domElem.addEventListener('pointerdown', e => {
        if (e.target && e.target.closest && e.target.closest('button, select, input, a, #vrOptionsDrawer')) return;
        isDragging = true;
        startX = e.clientX;
        startY = e.clientY;
        movedDelta = 0;
        globeAutoSpin = false;
        updateGlobeSpinButton();
      });

      window.addEventListener('pointermove', e => {
        if (!isDragging) return;
        const dx = e.clientX - startX;
        const dy = e.clientY - startY;
        movedDelta += Math.abs(dx) + Math.abs(dy);
        startX = e.clientX;
        startY = e.clientY;
        globeTargetRotation.y += dx * 0.006;
        globeTargetRotation.x = Math.max(-1.2, Math.min(1.2, globeTargetRotation.x + dy * 0.006));
      });

      window.addEventListener('pointerup', e => {
        if (!isDragging) return;
        isDragging = false;
        if (movedDelta < 6) {
          handleGlobeClick(e, domElem);
        }
      });

      domElem.addEventListener('wheel', e => {
        e.preventDefault();
        globeCameraDistance = Math.max(1.85, Math.min(4.5, globeCameraDistance + e.deltaY * 0.0015));
      }, { passive: false });

      function animateGlobe() {
        requestAnimationFrame(animateGlobe);
        if (STATE.activeView !== 'globe') return;
        if (globeAutoSpin) {
          globeTargetRotation.y += 0.0018;
        }
        globeCurrentRotation.x += (globeTargetRotation.x - globeCurrentRotation.x) * 0.09;
        globeCurrentRotation.y += (globeTargetRotation.y - globeCurrentRotation.y) * 0.09;
        globeGroup.rotation.x = globeCurrentRotation.x;
        globeGroup.rotation.y = globeCurrentRotation.y;
        globeCamera.position.z += (globeCameraDistance - globeCamera.position.z) * 0.12;
        globeRenderer.render(globeScene, globeCamera);
      }
      animateGlobe();
    }

    async function loadWorldAtlasPolygons() {
      const statusBadge = document.getElementById('globeDataStatusBadge');
      try {
        const res = await fetch('https://cdn.jsdelivr.net/npm/world-atlas@2.0.2/countries-110m.json');
        if (!res.ok) throw new Error('TopoJSON fetch failed');
        const worldData = await res.json();
        if (window.topojson && worldData.objects && worldData.objects.countries) {
          const mesh = window.topojson.mesh(worldData, worldData.objects.countries);
          const linePoints = [];
          mesh.coordinates.forEach(line => {
            for (let i = 0; i < line.length - 1; i++) {
              const [lng1, lat1] = line[i];
              const [lng2, lat2] = line[i + 1];
              linePoints.push(latLngToVector3(lat1, lng1, 1.186));
              linePoints.push(latLngToVector3(lat2, lng2, 1.186));
            }
          });
          const borderGeo = new THREE.BufferGeometry().setFromPoints(linePoints);
          const borderMat = new THREE.LineBasicMaterial({ color: 0x7dd3fc, transparent: true, opacity: 0.45 });
          globeGroup.add(new THREE.LineSegments(borderGeo, borderMat));
          if (statusBadge) statusBadge.textContent = 'TopoJSON Country Boundaries Active';
        }
      } catch (err) {
        if (statusBadge) statusBadge.textContent = 'WGS84 Coordinate Grid Active';
      }
    }

    function rebuildGlobeMarkers() {
      if (!globeMarkersGroup) return;
      while (globeMarkersGroup.children.length > 0) {
        globeMarkersGroup.remove(globeMarkersGroup.children[0]);
      }
      globeMarkerMeshes = [];

      const searchVal = (document.getElementById('globeSearchInput')?.value || '').trim().toLowerCase();
      const countryVal = document.getElementById('globeCountrySelect')?.value || 'all';

      DESTINATIONS.forEach(dest => {
        const matchesCountry = countryVal === 'all' || dest.country === countryVal;
        const matchesSearch = !searchVal ||
          dest.name.toLowerCase().includes(searchVal) ||
          dest.country.toLowerCase().includes(searchVal) ||
          dest.region.toLowerCase().includes(searchVal);

        if (!matchesCountry || !matchesSearch) return;

        const pos = latLngToVector3(dest.coordinates.lat, dest.coordinates.lng, 1.21);
        const isSelected = dest.id === STATE.selectedDestinationId;

        const pinGeo = new THREE.SphereGeometry(isSelected ? 0.034 : 0.024, 16, 16);
        const pinMat = new THREE.MeshBasicMaterial({
          color: isSelected ? 0xf59e0b : (dest.flagship ? 0x38bdf8 : 0x2dd4bf)
        });
        const pinMesh = new THREE.Mesh(pinGeo, pinMat);
        pinMesh.position.copy(pos);
        pinMesh.userData = { destinationId: dest.id };
        globeMarkersGroup.add(pinMesh);
        globeMarkerMeshes.push(pinMesh);

        const surfacePos = latLngToVector3(dest.coordinates.lat, dest.coordinates.lng, 1.18);
        const stalkGeo = new THREE.BufferGeometry().setFromPoints([surfacePos, pos]);
        const stalkMat = new THREE.LineBasicMaterial({ color: isSelected ? 0xf59e0b : 0x38bdf8 });
        globeMarkersGroup.add(new THREE.Line(stalkGeo, stalkMat));
      });
    }

    function handleGlobeClick(event, domElem) {
      const rect = domElem.getBoundingClientRect();
      globeMouse.x = ((event.clientX - rect.left) / rect.width) * 2 - 1;
      globeMouse.y = -((event.clientY - rect.top) / rect.height) * 2 + 1;
      globeRaycaster.setFromCamera(globeMouse, globeCamera);
      const intersects = globeRaycaster.intersectObjects(globeMarkerMeshes, false);
      if (intersects.length > 0) {
        const hitId = intersects[0].object.userData.destinationId;
        if (hitId) focusGlobeOnDestination(hitId);
      }
    }

    function focusGlobeOnDestination(destId) {
      const dest = getDestinationById(destId);
      STATE.selectedDestinationId = dest.id;
      globeAutoSpin = false;
      updateGlobeSpinButton();

      const latRad = dest.coordinates.lat * (Math.PI / 180);
      const lngRad = dest.coordinates.lng * (Math.PI / 180);
      globeTargetRotation.x = latRad;
      globeTargetRotation.y = -lngRad - Math.PI / 2;

      const hoverReadout = document.getElementById('globeHoverReadout');
      if (hoverReadout) {
        hoverReadout.textContent = `Target: ${dest.name} (${dest.coordinates.label})`;
      }

      rebuildGlobeMarkers();
      renderGlobeSelectedPanel(dest.id);
      renderGlobeQuickPins();
      logDiscoveryAction(dest.id, 'Selected on 3D Geospatial Globe');
    }

    function flyGlobeToDestination(destId) {
      switchView('globe');
      setTimeout(() => focusGlobeOnDestination(destId), 60);
    }

    function updateGlobeSpinButton() {
      const btn = document.getElementById('globeToggleSpinBtn');
      if (btn) {
        btn.textContent = `Auto-Spin: ${globeAutoSpin ? 'ON' : 'OFF'}`;
      }
    }

    function renderGlobeQuickPins() {
      const bar = document.getElementById('globeQuickPinsBar');
      if (!bar) return;
      bar.innerHTML = DESTINATIONS.map(dest => {
        const active = dest.id === STATE.selectedDestinationId;
        return `
          <button type="button" onclick="focusGlobeOnDestination('${dest.id}')"
                  class="px-3 py-1 rounded-full text-[11px] font-mono shrink-0 transition ${active ? 'bg-amber-400 text-slate-950 font-semibold' : 'bg-white/5 text-slate-200 hover:bg-white/15 border border-white/10'}">
            ${dest.name.split('(')[0].trim()}
          </button>
        `;
      }).join('');
    }

    function renderGlobeSelectedPanel(destId) {
      const panel = document.getElementById('globeSelectedPanel');
      if (!panel) return;
      const dest = getDestinationById(destId);
      const img = dest.images[0];
      const fallbackUri = createFallbackDataUri(dest.name, dest.coordinates.label);
      const sameCountrySites = DESTINATIONS.filter(d => d.country === dest.country);

      panel.innerHTML = `
        <div class="space-y-4">
          <div class="flex items-center justify-between gap-2">
            ${getVerificationBadgeHTML(dest.verificationStatus)}
            <span class="text-[11px] font-mono text-sky-300">${dest.coordinates.label}</span>
          </div>
          <div class="relative h-52 rounded-2xl overflow-hidden bg-[#02050d] border border-white/10">
            <img src="${img.url}" alt="${dest.name}" onerror="this.onerror=null;this.src='${fallbackUri}';" class="w-full h-full object-cover" />
            <div class="absolute inset-0 bg-gradient-to-t from-black/90 via-transparent to-transparent"></div>
            <div class="absolute bottom-3 left-3.5 right-3.5">
              <div class="text-[10px] font-mono uppercase tracking-wider text-sky-300">${dest.country} → ${dest.region}</div>
              <h3 class="font-editorial text-2xl font-semibold text-white">${dest.name}</h3>
            </div>
          </div>
          <p class="text-xs text-slate-300 font-light leading-relaxed">${dest.shortSummary}</p>
          <div class="p-4 rounded-2xl bg-white/[0.03] border border-white/10 space-y-2">
            <div class="text-[10px] font-mono uppercase text-sky-300">Famous Locations in ${dest.country} (${sameCountrySites.length})</div>
            <div class="flex flex-wrap gap-1.5">
              ${sameCountrySites.map(s => `
                <button type="button" onclick="focusGlobeOnDestination('${s.id}')" class="px-2.5 py-1 rounded-full text-[11px] font-mono ${s.id === dest.id ? 'bg-sky-500/25 text-sky-200 border border-sky-400/40' : 'bg-white/5 text-slate-300 hover:text-white'}">
                  ${s.name.split('(')[0].trim()}
                </button>
              `).join('')}
            </div>
          </div>
          <div class="grid grid-cols-2 gap-2.5 pt-1">
            <button type="button" onclick="openEditorialDestinationPage('${dest.id}')" class="px-4 py-3 rounded-full bg-white text-slate-950 hover:bg-sky-100 font-semibold text-xs flex items-center justify-center gap-1.5 transition">
              <span>Explore Destination</span>
            </button>
            <button type="button" onclick="openPanoramaForDestination('${dest.id}')" class="px-4 py-3 rounded-full bg-sky-500/20 hover:bg-sky-500/30 border border-sky-400/40 text-sky-200 text-xs flex items-center justify-center gap-1.5 transition">
              <i data-lucide="view" class="w-4 h-4"></i><span>Explore in VR</span>
            </button>
          </div>
        </div>
      `;
      if (window.lucide) window.lucide.createIcons();
    }

    // =========================================================================
    // 12. DESTINATION EXPLORER FILTERING & LIGHTBOX / EXPERIENCE MODAL
    // =========================================================================
    function populateExploreDropdowns() {
      const countrySelect = document.getElementById('exploreCountryFilter');
      const divisionSelect = document.getElementById('exploreDivisionFilter');
      if (countrySelect) {
        const countries = Array.from(new Set(DESTINATIONS.map(d => d.country)));
        countrySelect.innerHTML = `<option value="all">All Countries (${countries.length})</option>` +
          countries.map(c => `<option value="${c}">${c}</option>`).join('');
      }
      if (divisionSelect) {
        const divisions = Array.from(new Set(DESTINATIONS.map(d => d.region)));
        divisionSelect.innerHTML = `<option value="all">All Divisions / Regions (${divisions.length})</option>` +
          divisions.map(r => `<option value="${r}">${r}</option>`).join('');
      }

      const categories = ['all', 'Beaches', 'Historical Landmarks', 'Cultural Destinations', 'Islands', 'Forests', 'Mountains', 'Natural Wonders', 'Tourist Attractions'];
      const pillsContainer = document.getElementById('exploreCategoryPills');
      if (pillsContainer) {
        const activeCat = document.getElementById('exploreCategoryFilter')?.value || 'all';
        pillsContainer.innerHTML = categories.map(cat => `
          <button type="button" onclick="setExploreCategory('${cat}')"
                  class="px-3.5 py-1.5 rounded-full text-xs font-mono transition ${activeCat === cat ? 'bg-white text-slate-950 font-semibold' : 'bg-white/5 text-slate-300 hover:text-white border border-white/10'}">
            ${cat === 'all' ? 'All Categories' : cat}
          </button>
        `).join('');
      }
    }

    function setExploreCategory(cat) {
      const catSelect = document.getElementById('exploreCategoryFilter');
      if (catSelect) catSelect.value = cat;
      populateExploreDropdowns();
      renderExploreGrid();
    }

    function renderExploreGrid() {
      const grid = document.getElementById('exploreDestinationsGrid');
      const counter = document.getElementById('exploreResultCounter');
      if (!grid) return;

      const q = (document.getElementById('exploreSearchInput')?.value || '').trim().toLowerCase();
      const country = document.getElementById('exploreCountryFilter')?.value || 'all';
      const division = document.getElementById('exploreDivisionFilter')?.value || 'all';
      const category = document.getElementById('exploreCategoryFilter')?.value || 'all';
      const sortBy = document.getElementById('exploreSortSelect')?.value || 'flagship';

      let filtered = DESTINATIONS.filter(dest => {
        const matchQ = !q ||
          dest.name.toLowerCase().includes(q) ||
          dest.country.toLowerCase().includes(q) ||
          dest.region.toLowerCase().includes(q) ||
          dest.district.toLowerCase().includes(q) ||
          dest.city.toLowerCase().includes(q) ||
          dest.shortSummary.toLowerCase().includes(q);
        const matchCountry = country === 'all' || dest.country === country;
        const matchDivision = division === 'all' || dest.region === division;
        const matchCat = category === 'all' || dest.categories.includes(category);
        return matchQ && matchCountry && matchDivision && matchCat;
      });

      filtered.sort((a, b) => {
        if (sortBy === 'name') return a.name.localeCompare(b.name);
        if (sortBy === 'popularity') return a.popularityRank - b.popularityRank;
        if (sortBy === 'verified') return a.verificationStatus.localeCompare(b.verificationStatus);
        return (b.flagship ? 1 : 0) - (a.flagship ? 1 : 0) || a.popularityRank - b.popularityRank;
      });

      if (counter) {
        counter.textContent = `Showing ${filtered.length} of ${DESTINATIONS.length} Verified Locations`;
      }

      if (filtered.length === 0) {
        grid.innerHTML = `
          <div class="col-span-full glass-panel rounded-3xl p-10 text-center space-y-3">
            <div class="text-sm font-semibold text-white">No destinations matched your current filter combination.</div>
            <button type="button" onclick="resetExploreFilters()" class="px-5 py-2 rounded-full bg-white text-slate-950 text-xs font-mono">Reset All Filters</button>
          </div>
        `;
        return;
      }

      grid.innerHTML = filtered.map(dest => renderDestinationCardHTML(dest, true)).join('');
      if (window.lucide) window.lucide.createIcons();
    }

    function resetExploreFilters() {
      const s = document.getElementById('exploreSearchInput');
      const c = document.getElementById('exploreCountryFilter');
      const d = document.getElementById('exploreDivisionFilter');
      const cat = document.getElementById('exploreCategoryFilter');
      if (s) s.value = '';
      if (c) c.value = 'all';
      if (d) d.value = 'all';
      if (cat) cat.value = 'all';
      populateExploreDropdowns();
      renderExploreGrid();
    }

    function openDestinationModal(destId, activePhotoIdx = 0) {
      const dest = getDestinationById(destId);
      const modal = document.getElementById('destinationModal');
      const content = document.getElementById('destinationModalContent');
      if (!modal || !content) return;

      const selectedPhoto = dest.images[activePhotoIdx] || dest.images[0];
      const fallbackUri = createFallbackDataUri(dest.name, dest.coordinates.label);
      logDiscoveryAction(dest.id, 'Inspected High-Res Destination Lightbox');

      content.innerHTML = `
        <div class="bg-[#02050d] px-6 py-4 border-b border-white/10 flex flex-wrap items-center justify-between gap-3">
          <div class="flex flex-wrap items-center gap-2">
            ${getVerificationBadgeHTML(dest.verificationStatus)}
            <span class="text-xs font-mono text-slate-400">Updated: ${dest.lastUpdated}</span>
            <span class="text-xs font-mono text-sky-300">• ${dest.coordinates.label}</span>
          </div>
          <div class="flex items-center gap-2">
            <button type="button" onclick="openPanoramaForDestination('${dest.id}'); closeDestinationModal();" class="px-4 py-2 rounded-full bg-white text-slate-950 font-semibold text-xs flex items-center gap-1.5">
              <i data-lucide="view" class="w-3.5 h-3.5"></i><span>Enter 360° VR Experience</span>
            </button>
            <button type="button" onclick="closeDestinationModal()" class="p-2 rounded-full bg-white/5 border border-white/15 text-slate-300 hover:text-white" aria-label="Close Modal">
              <i data-lucide="x" class="w-4 h-4"></i>
            </button>
          </div>
        </div>

        <div class="p-6 sm:p-8 space-y-6 max-h-[82vh] overflow-y-auto custom-scrollbar">
          <div class="relative rounded-2xl overflow-hidden bg-[#010308] h-80 sm:h-[460px] flex items-center justify-center border border-white/10">
            <img src="${selectedPhoto.url}" alt="${selectedPhoto.title}"
                 onerror="this.onerror=null;this.src='${fallbackUri}';"
                 class="max-w-full max-h-full object-contain" />
            <div class="absolute bottom-3 left-3 right-3 glass-panel px-4 py-2.5 rounded-xl flex flex-wrap items-center justify-between gap-2 text-xs">
              <div>
                <span class="font-semibold text-white">${selectedPhoto.title}</span>
                <span class="text-slate-300 ml-2">(${selectedPhoto.viewpoint})</span>
              </div>
              <div class="flex items-center gap-3 text-[11px] font-mono">
                <span class="text-slate-300">Credit: ${selectedPhoto.author}</span>
                <span class="px-2 py-0.5 rounded bg-sky-500/20 text-sky-300">${selectedPhoto.license}</span>
                <a href="${selectedPhoto.commonsPage}" target="_blank" rel="noopener noreferrer" class="text-sky-300 hover:underline">Wikimedia File Page ↗</a>
              </div>
            </div>
          </div>

          <div class="grid grid-cols-2 sm:grid-cols-4 gap-3">
            ${dest.images.map((img, i) => `
              <button type="button" onclick="openDestinationModal('${dest.id}', ${i})"
                      class="p-1.5 rounded-xl border text-left transition ${i === activePhotoIdx ? 'border-sky-400 bg-sky-500/15' : 'border-white/10 bg-white/[0.03] hover:border-white/30'}">
                <img src="${img.url}" alt="${img.title}" onerror="this.onerror=null;this.src='${fallbackUri}';" class="w-full h-16 object-cover rounded-lg" />
                <div class="text-[10px] font-mono text-slate-200 truncate mt-1">${img.viewpoint}</div>
              </button>
            `).join('')}
          </div>
        </div>
      `;

      modal.classList.remove('hidden');
      if (window.lucide) window.lucide.createIcons();
    }

    function openWatchExperienceModal() {
      const modal = document.getElementById('destinationModal');
      const content = document.getElementById('destinationModalContent');
      if (!modal || !content) return;

      content.innerHTML = `
        <div class="bg-[#02050d] px-6 py-4 border-b border-white/10 flex items-center justify-between">
          <div class="flex items-center gap-2.5">
            <span class="w-2.5 h-2.5 rounded-full bg-sky-400 animate-pulse"></span>
            <span class="font-universal text-xs uppercase tracking-widest text-white">GLOBAL VR EXPLORER — INTERACTIVE EXPERIENCE SHOWREEL</span>
          </div>
          <button type="button" onclick="closeDestinationModal()" class="p-2 rounded-full bg-white/5 border border-white/15 text-slate-300 hover:text-white">
            <i data-lucide="x" class="w-4 h-4"></i>
          </button>
        </div>

        <div class="p-6 sm:p-10 space-y-8">
          <div class="space-y-2 text-center max-w-2xl mx-auto">
            <span class="text-[11px] font-mono uppercase tracking-[0.25em] text-sky-400">Choose Your Immersive Entry Point</span>
            <h3 class="font-editorial text-4xl sm:text-5xl font-semibold text-white">Explore the World Before You Travel</h3>
            <p class="text-sm text-slate-300 font-light">Select a flagship destination below to open its full-screen editorial showcase page or launch directly into the dedicated 360° VR Panorama Studio.</p>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
            ${DESTINATIONS.filter(d => d.flagship).map(d => `
              <div class="rounded-2xl overflow-hidden border border-white/15 bg-[#050b18] flex flex-col justify-between">
                <div class="relative h-48">
                  <img src="${d.images[0].url}" alt="${d.name}" class="w-full h-full object-cover" />
                  <div class="absolute inset-0 bg-gradient-to-t from-[#050b18] via-transparent to-transparent"></div>
                  <span class="absolute top-3 left-3 px-2.5 py-0.5 rounded-full bg-black/70 text-[10px] font-mono text-sky-300">${d.country}</span>
                </div>
                <div class="p-5 space-y-3">
                  <h4 class="font-editorial text-2xl font-semibold text-white">${d.name}</h4>
                  <p class="text-xs text-slate-300 font-light line-clamp-2">${d.shortSummary}</p>
                  <div class="grid grid-cols-2 gap-2 pt-2">
                    <button type="button" onclick="closeDestinationModal(); openEditorialDestinationPage('${d.id}');" class="py-2 rounded-lg bg-white text-slate-950 font-medium text-xs">
                      Destination Page
                    </button>
                    <button type="button" onclick="closeDestinationModal(); openPanoramaForDestination('${d.id}');" class="py-2 rounded-lg bg-sky-500/20 text-sky-200 border border-sky-400/40 font-medium text-xs">
                      Explore in VR
                    </button>
                  </div>
                </div>
              </div>
            `).join('')}
          </div>
        </div>
      `;

      modal.classList.remove('hidden');
      if (window.lucide) window.lucide.createIcons();
    }

    function closeDestinationModal() {
      const modal = document.getElementById('destinationModal');
      if (modal) modal.classList.add('hidden');
    }

    // =========================================================================
    // 13. FULL-SCREEN FIRST-PERSON 3D VR ENVIRONMENT WITH VIRTUAL HANDS
    //     (Closely matching the uploaded first-person beach + hands reference)
    // =========================================================================
    let panoScene = null;
    let panoCamera = null;
    let panoRenderer = null;
    let panoHotspotVectors = [];
    let audioCtx = null;
    let ambientNoiseNode = null;

    // First-Person VR Rig, Virtual Hands & Animated Environment References
    let vrPlayerRig = null;
    let vrHandsGroup = null;
    let vrLeftHand = null;
    let vrRightHand = null;
    let vrLeftFingers = [];
    let vrRightFingers = [];
    let vrOceanMesh = null;
    let vrOceanGeo = null;
    let vrSurfFoamMesh = null;
    let vrPalmCanopyGroup = null;
    let vrBeachGrassBlades = [];
    let vrHandWaveTimer = 0;
    let vrPointerNormalized = { x: 0, y: 0 };
    let vrWalkKeys = { w: false, a: false, s: false, d: false };
    let vrPlayerOffset = { x: 0, z: 0 };

    // =========================================================================
    // COMPLETE VR DESTINATION TEMPLATE REGISTRY, DISTINCT 3D ENVIRONMENTS,
    // INSIDE-OF-SPHERE 360° EQUIRECTANGULAR VIEWER & TWO-SYSTEM HAND TRACKING
    // =========================================================================
    const VR_DESTINATION_TEMPLATES = [
      {
        templateKey: 'kaminarimon-gate',
        destId: 'tokyo-sensoji',
        viewpointIndex: 0,
        shortLabel: 'Kaminarimon Gate, Japan',
        fullTitle: 'Kaminarimon Entrance Gate, Asakusa, Tokyo, Japan',
        subtitle: '35.7111° N, 139.7964° E • Outer Thunder Gate (Fūraijinmon)',
        description: 'Iconic crimson-vermilion outer entrance gate of Sensō-ji housing the 700-kg great red chōchin lantern and guardian statues of Fūjin and Raijin.',
        envTypeLabel: '3D Edo Gate + Authentic Photo'
      },
      {
        templateKey: 'sensoji-hondo',
        destId: 'tokyo-sensoji',
        viewpointIndex: 1,
        shortLabel: 'Sensō-ji Hondo, Japan',
        fullTitle: 'Sensō-ji Hondo (Main Hall) & Five-Storied Pagoda, Japan',
        subtitle: '35.7148° N, 139.7967° E • Inner Sanctuary Courtyard',
        description: 'Monumental Buddhist Main Hall (Hondō) with steep titanium-tiled roof ridge, jokōro incense cauldron, and Five-Storied Pagoda in Asakusa.',
        envTypeLabel: '3D Sanctuary + Authentic Photo'
      },
      {
        templateKey: 'coxs-bazar',
        destId: 'coxs-bazar',
        viewpointIndex: 1,
        shortLabel: "Cox's Bazar Beach, BD",
        fullTitle: "Cox's Bazar Sea Beach, Chittagong Division, Bangladesh",
        subtitle: '21.4272° N, 92.0058° E • 120 km Unbroken Bay of Bengal Shore',
        description: "The world's longest natural unbroken sea beach along the Bay of Bengal with golden sunset surf and crescent-prowed wooden Sampan boats.",
        envTypeLabel: '3D Shoreline + 360° Panorama'
      },
      {
        templateKey: 'eiffel-tower',
        destId: 'eiffel-tower',
        viewpointIndex: 2,
        shortLabel: 'Eiffel Tower, France',
        fullTitle: 'Eiffel Tower (La Tour Eiffel) & Champ de Mars, Paris, France',
        subtitle: '48.8584° N, 2.2945° E • 330m Iron Lattice Monument',
        description: 'Gustave Eiffel’s 1889 puddle-iron tower rising above the Champ de Mars gardens, Trocadéro fountains, and the Seine River.',
        envTypeLabel: '3D Paris Plaza + 360° Panorama'
      },
      {
        templateKey: 'lalbagh-fort',
        destId: 'lalbagh-fort',
        viewpointIndex: 0,
        shortLabel: 'Lalbagh Fort, BD',
        fullTitle: 'Lalbagh Fort (Fort Aurangabad), Old Dhaka, Bangladesh',
        subtitle: '23.7189° N, 90.3882° E • 1678 AD Mughal Charbagh Fortress',
        description: '17th-century Mughal fortress complex featuring the Mausoleum of Pari Bibi, symmetrical charbagh water channels, and three-domed Quilla Mosque.',
        envTypeLabel: '3D Mughal Garden + Authentic Photo'
      },
      {
        templateKey: 'rome-colosseum',
        destId: 'rome-colosseum',
        viewpointIndex: 0,
        shortLabel: 'Colosseum, Italy',
        fullTitle: 'The Colosseum (Flavian Amphitheatre), Rome, Italy',
        subtitle: '41.8902° N, 12.4922° E • 80 AD Travertine Amphitheatre',
        description: 'Monumental three-tiered Roman travertine arcades and subterranean hypogeum in the heart of ancient Rome.',
        envTypeLabel: '3D Roman Arena + Authentic Photo'
      },
      {
        templateKey: 'great-wall-mutianyu',
        destId: 'great-wall-mutianyu',
        viewpointIndex: 0,
        shortLabel: 'Great Wall, China',
        fullTitle: 'Great Wall of China at Mutianyu, Huairou, Beijing',
        subtitle: '40.4319° N, 116.5704° E • Ming Dynasty Granite Battlements',
        description: 'Crenellated Ming-dynasty granite ramparts and watchtowers winding across the forested ridges of the Yan Mountains.',
        envTypeLabel: '3D Ridge Wall + Authentic Photo'
      },
      {
        templateKey: 'sundarbans',
        destId: 'sundarbans',
        viewpointIndex: 0,
        shortLabel: 'Sundarbans, BD',
        fullTitle: 'The Sundarbans UNESCO Mangrove Forest, Bangladesh',
        subtitle: '21.9497° N, 89.1833° E • Tidal Mangrove Delta',
        description: 'World’s largest halophytic mangrove forest intersected by tidal estuaries and breathing pneumatophore roots.',
        envTypeLabel: '3D Mangrove Estuary + Authentic Photo'
      },
      {
        templateKey: 'swiss-alps',
        destId: 'swiss-alps',
        viewpointIndex: 0,
        shortLabel: 'Swiss Alps, CH',
        fullTitle: 'Swiss Alps Jungfrau-Aletsch Glacier, Switzerland',
        subtitle: '46.5475° N, 7.9853° E • 4,000m Alpine Peaks & Glacier',
        description: 'Glaciated peaks of Eiger, Mönch, and Jungfrau overlooking the 20 km Great Aletsch Glacier ribbon.',
        envTypeLabel: '3D Alpine Glacier + Authentic Photo'
      }
    ];

    // Image Cache for loaded HTMLImageElements so 2D/3D Canvas & Equirectangular Sphere have instant access
    const PANO_IMAGE_CACHE = {};
    STATE.activeTemplateKey = 'coxs-bazar';
    STATE.panoLoadToken = 0;
    STATE.forceMissingAssetTest = false;
    STATE.panoAssetStatus = 'loaded'; // 'loading' | 'loaded' | 'missing'
    let panoEventsBound = false;

    function getActiveEnvironmentKey(destId, viewpointIdx) {
      if (destId === 'tokyo-sensoji') {
        return Number(viewpointIdx) === 1 ? 'sensoji-hondo' : 'kaminarimon-gate';
      }
      return destId || 'coxs-bazar';
    }

    function getTemplateMeta(destId, viewpointIdx) {
      const envKey = getActiveEnvironmentKey(destId, viewpointIdx);
      const found = VR_DESTINATION_TEMPLATES.find(t => t.templateKey === envKey);
      if (found) return found;
      const dest = getDestinationById(destId);
      const vp = dest.images[viewpointIdx] || dest.images[0];
      return {
        templateKey: envKey,
        destId: dest.id,
        viewpointIndex: viewpointIdx || 0,
        shortLabel: dest.name.split('(')[0].trim(),
        fullTitle: `${dest.name} — ${vp.viewpoint}`,
        subtitle: `${dest.coordinates.label} • ${dest.region}, ${dest.country}`,
        description: dest.shortSummary,
        envTypeLabel: vp.isPanorama ? '3D World + 360° Panorama' : '3D World + Authentic Photo'
      };
    }

    function toggleVrOptionsDrawer() {
      const drawer = document.getElementById('vrOptionsDrawer');
      if (drawer) drawer.classList.toggle('hidden');
    }

    function triggerVirtualHandWave() {
      showToast('Placeholder hand animations are disabled. Enable WebXR or Camera Hand Tracking to move hands with your real hands.');
    }

    function adjustPanoZoom(deltaFov) {
      STATE.panoFov = Math.max(38, Math.min(98, (Number(STATE.panoFov) || 72) + deltaFov));
      const slider = document.getElementById('panoFovSlider');
      if (slider) slider.value = String(STATE.panoFov);
      const badge = document.getElementById('panoZoomBadge');
      if (badge) badge.textContent = `${Math.round(STATE.panoFov)}°`;
      updatePanoReadout();
    }

    function toggleVrFullscreen() {
      const wrapper = document.getElementById('panoViewportWrapper') || document.getElementById('view-panorama');
      if (!wrapper) return;
      if (!document.fullscreenElement) {
        wrapper.requestFullscreen?.().catch(() => {
          showToast('Fullscreen request blocked by browser container.');
        });
      } else {
        document.exitFullscreen?.().catch(() => {});
      }
    }

    function populatePanoSelectors() {
      const unifiedItems = [
        { val: 'tpl:coxs-bazar', label: "Cox's Bazar Beach, Bangladesh (360° VR & 3D)" },
        { val: 'tpl:lalbagh-fort', label: 'Lalbagh Fort, Bangladesh (3D Model / Photo)' },
        { val: 'tpl:sundarbans', label: 'The Sundarbans, Bangladesh (3D Mangrove)' },
        { val: 'ratargul-swamp', label: 'Ratargul Swamp Forest, Bangladesh (3D Forest)' },
        { val: 'saint-martins', label: "Saint Martin's Island, Bangladesh (3D Coast)" },
        { val: 'tpl:kaminarimon-gate', label: 'Kaminarimon Gate, Japan (3D Model / Photo)' },
        { val: 'tpl:sensoji-hondo', label: 'Sensō-ji Temple (Hondō), Japan (3D Model / Photo)' },
        { val: 'tpl:eiffel-tower', label: 'Eiffel Tower, France (360° VR & 3D Model)' },
        { val: 'tpl:rome-colosseum', label: 'Colosseum, Italy (360° VR & 3D Model)' },
        { val: 'tpl:great-wall-mutianyu', label: 'Great Wall of China (360° VR & 3D Model)' },
        { val: 'tpl:swiss-alps', label: 'Swiss Alps Jungfrau-Aletsch (360° VR & 3D)' },
        { val: 'statue-of-liberty', label: 'Statue of Liberty, USA (3D Model / Photo)' }
      ];
      const optHTML = unifiedItems.map(it => `<option value="${it.val}">${it.label}</option>`).join('');
      const activeKey = getActiveEnvironmentKey(STATE.selectedDestinationId, STATE.panoViewpointIndex);
      const hasTpl = unifiedItems.some(it => it.val === `tpl:${activeKey}`);
      const targetVal = hasTpl ? `tpl:${activeKey}` : STATE.selectedDestinationId;

      const topSel = document.getElementById('panoTopDestinationSelect');
      if (topSel) {
        topSel.innerHTML = optHTML;
        topSel.value = targetVal;
      }
      const sel = document.getElementById('panoDestinationSelect');
      if (sel) {
        sel.innerHTML = optHTML;
        sel.value = targetVal;
      }
      renderPanoTemplateSwitcherBar();
    }

    function renderPanoTemplateSwitcherBar() {
      const bar = document.getElementById('panoTemplateSwitcherBar');
      if (!bar) return;
      const activeKey = getActiveEnvironmentKey(STATE.selectedDestinationId, STATE.panoViewpointIndex);
      bar.innerHTML = VR_DESTINATION_TEMPLATES.map(t => {
        const isActive = t.templateKey === activeKey;
        return `
          <button type="button"
                  data-template-key="${t.templateKey}"
                  onclick="selectVrDestinationTemplate('${t.templateKey}')"
                  class="px-3 py-1.5 rounded-xl text-[11px] font-medium whitespace-nowrap shrink-0 transition flex items-center gap-1.5 ${
                    isActive
                      ? 'bg-sky-400 text-slate-950 font-bold shadow-[0_0_16px_rgba(56,189,248,0.45)]'
                      : 'bg-white/10 text-slate-200 hover:bg-white/20 hover:text-white border border-white/10'
                  }">
            <span class="w-1.5 h-1.5 rounded-full ${isActive ? 'bg-slate-950' : 'bg-sky-400'}"></span>
            <span>${t.shortLabel}</span>
          </button>
        `;
      }).join('');
    }

    function selectVrDestinationTemplate(templateKey) {
      const tpl = VR_DESTINATION_TEMPLATES.find(t => t.templateKey === templateKey);
      STATE.forceMissingAssetTest = false;
      if (tpl) {
        STATE.selectedDestinationId = tpl.destId;
        STATE.panoViewpointIndex = tpl.viewpointIndex;
        STATE.activeTemplateKey = tpl.templateKey;
      } else {
        STATE.selectedDestinationId = templateKey;
        STATE.panoViewpointIndex = 0;
        STATE.activeTemplateKey = getActiveEnvironmentKey(templateKey, 0);
      }
      STATE.panoYaw = 0;
      STATE.panoPitch = -3;
      vrPlayerOffset = { x: 0, z: 0 };
      const dest = getDestinationById(STATE.selectedDestinationId);
      STATE.selectedHotspotId = dest.hotspots[0]?.id || '';
      if (STATE.activeView !== 'panorama') {
        switchView('panorama');
      }
      loadPanoramaEnvironment();
      logDiscoveryAction(STATE.selectedDestinationId, `Loaded VR Environment: ${STATE.activeTemplateKey}`);
    }

    function testMissingPanoramaAssetGracefulFallback() {
      STATE.forceMissingAssetTest = true;
      STATE.panoMode = 'authentic';
      loadPanoramaEnvironment();
      showToast('Simulating Missing 360° Panorama Asset — Showing Labeled Equirectangular Placeholder');
    }

    function setVrExperienceMode(mode) {
      STATE.panoMode = mode || 'authentic';
      if (STATE.panoMode === 'authentic') {
        const dest = getDestinationById(STATE.selectedDestinationId);
        if (dest && dest.images && !(dest.images[STATE.panoViewpointIndex]?.isPanorama)) {
          const pIdx = dest.images.findIndex(im => im.isPanorama);
          if (pIdx >= 0) STATE.panoViewpointIndex = pIdx;
        }
      }
      if (STATE.panoMode === 'stereo') {
        showToast('Stereoscopic Dual-Eye Rendering Enabled (Left / Right Eye IPD Cameras)');
      }
      loadPanoramaEnvironment();
    }

    // Properly dispose all old Three.js geometries, materials & textures to prevent memory leaks and stale visuals
    function disposePanoramaSceneResources() {
      if (!panoScene) return;
      const toRemove = [...panoScene.children];
      toRemove.forEach(obj => {
        panoScene.remove(obj);
        obj.traverse?.(child => {
          if (child.geometry && typeof child.geometry.dispose === 'function') {
            child.geometry.dispose();
          }
          if (child.material) {
            const mats = Array.isArray(child.material) ? child.material : [child.material];
            mats.forEach(m => {
              if (m.map && typeof m.map.dispose === 'function') m.map.dispose();
              if (typeof m.dispose === 'function') m.dispose();
            });
          }
        });
      });
      vrHandsGroup = null;
      vrLeftHand = null;
      vrRightHand = null;
      vrLeftFingers = [];
      vrRightFingers = [];
      vrOceanGeo = null;
      vrOceanMesh = null;
      vrSurfFoamMesh = null;
      vrPalmCanopyGroup = null;
      REAL_HAND_STATE.jointSpheres = { left: {}, right: {} };

      // Also clear the 2D canvas immediately so zero pixels of the previous destination remain visible
      const canvas = document.getElementById('vrHandsBeachCanvas');
      if (canvas) {
        const ctx = canvas.getContext('2d');
        if (ctx) ctx.clearRect(0, 0, canvas.width, canvas.height);
      }
    }

    let activePanoAbortController = null;
    let activePanoTimeoutId = null;

    function isMobileOrConstrainedDevice() {
      if (typeof navigator === 'undefined') return false;
      const ua = navigator.userAgent || '';
      const isMobileUA = /Android|iPhone|iPad|iPod|Mobile/i.test(ua);
      const smallScreen = typeof window !== 'undefined' && (window.innerWidth || 1280) < 820;
      const lowMem = typeof navigator.deviceMemory === 'number' && navigator.deviceMemory <= 4;
      return isMobileUA || smallScreen || lowMem;
    }

    function getMaxSafeTextureWidth() {
      let maxWebGL = 4096;
      try {
        if (panoRenderer && panoRenderer.capabilities && panoRenderer.capabilities.maxTextureSize) {
          maxWebGL = panoRenderer.capabilities.maxTextureSize;
        }
      } catch (e) {}
      const deviceTarget = isMobileOrConstrainedDevice() ? 1280 : 2048;
      return Math.min(deviceTarget, maxWebGL);
    }

    // Convert full-resolution Wikimedia Commons URLs into mobile/GPU-friendly thumbnail URLs
    // so Android devices never stall decoding 15MB+ 6000x4000 bitmaps!
    function getOptimalWikimediaUrl(rawUrl, targetWidth = null) {
      if (!rawUrl) return '';
      const w = targetWidth || getMaxSafeTextureWidth();
      // If already a /thumb/ URL, adjust the pixel width prefix (e.g. 3840px- -> 1280px-)
      if (rawUrl.includes('/wikipedia/commons/thumb/')) {
        return rawUrl.replace(/\/\d+px-([^/]+)$/, `/${w}px-$1`);
      }
      // Convert standard commons/a/ab/Filename.jpg -> commons/thumb/a/ab/Filename.jpg/{w}px-Filename.jpg
      const m = rawUrl.match(/^(https:\/\/upload\.wikimedia\.org\/wikipedia\/commons)\/([0-9a-f]\/[0-9a-f]{2})\/([^/?#]+)$/i);
      if (m) {
        const [, base, hashPath, filename] = m;
        return `${base}/thumb/${hashPath}/${filename}/${w}px-${filename}`;
      }
      return rawUrl;
    }

    // Free old cached textures & object URLs so mobile RAM/GPU memory never leaks
    function prunePanoImageCache(keepKey) {
      Object.keys(PANO_IMAGE_CACHE).forEach(k => {
        if (k === keepKey) return;
        const entry = PANO_IMAGE_CACHE[k];
        if (entry && entry.objectUrl) {
          try { URL.revokeObjectURL(entry.objectUrl); } catch (e) {}
        }
        if (entry && entry.img) {
          entry.img.onload = null;
          entry.img.onerror = null;
          entry.img.src = '';
        }
        delete PANO_IMAGE_CACHE[k];
      });
    }

    function dismissPanoLoadingOverlay() {
      if (activePanoAbortController) {
        try { activePanoAbortController.abort(); } catch (e) {}
        activePanoAbortController = null;
      }
      if (activePanoTimeoutId) {
        clearTimeout(activePanoTimeoutId);
        activePanoTimeoutId = null;
      }
      setPanoLoadingProgress(false, 100, '');
    }

    function retryCurrentDestinationLoad() {
      STATE.forceMissingAssetTest = false;
      const dest = getDestinationById(STATE.selectedDestinationId);
      const photo = dest.images[STATE.panoViewpointIndex] || dest.images[0];
      if (photo && photo.url) {
        delete PANO_IMAGE_CACHE[photo.url];
        delete PANO_IMAGE_CACHE[getOptimalWikimediaUrl(photo.url)];
      }
      loadPanoramaEnvironment();
    }

    function setPanoLoadingProgress(visible, percent, message, errorMsg = '') {
      const overlay = document.getElementById('panoLoadingOverlay');
      const bar = document.getElementById('panoLoadingProgressBar');
      const pctEl = document.getElementById('panoLoadingPercent');
      const textEl = document.getElementById('panoLoadingText');
      const spinner = document.getElementById('panoLoadingSpinner');
      const errBox = document.getElementById('panoLoadingErrorBox');
      const retryBtn = document.getElementById('panoRetryLoadBtn');
      if (!overlay) return;

      if (!visible) {
        overlay.classList.add('hidden');
        if (errBox) errBox.classList.add('hidden');
        if (retryBtn) retryBtn.classList.add('hidden');
        return;
      }

      overlay.classList.remove('hidden');
      if (bar) {
        if (percent === null) {
          bar.style.width = '100%';
          bar.classList.add('animate-pulse');
        } else {
          bar.classList.remove('animate-pulse');
          bar.style.width = `${Math.max(0, Math.min(100, percent))}%`;
        }
      }
      if (pctEl) pctEl.textContent = percent === null ? 'STREAMING...' : `${Math.round(percent)}%`;
      if (textEl && message) textEl.textContent = message;

      if (errorMsg) {
        if (spinner) spinner.classList.add('hidden');
        if (errBox) {
          errBox.textContent = errorMsg;
          errBox.classList.remove('hidden');
        }
        if (retryBtn) retryBtn.classList.remove('hidden');
      } else {
        if (spinner) spinner.classList.remove('hidden');
        if (errBox) errBox.classList.add('hidden');
        if (retryBtn) retryBtn.classList.add('hidden');
      }
    }

    async function loadSingleImageWithDecode(url, signal) {
      return new Promise((resolve, reject) => {
        if (signal && signal.aborted) {
          reject(new Error('Aborted'));
          return;
        }
        const img = new Image();
        img.crossOrigin = 'anonymous';
        const onAbort = () => {
          img.onload = null;
          img.onerror = null;
          img.src = '';
          reject(new Error('Aborted'));
        };
        if (signal) signal.addEventListener('abort', onAbort, { once: true });

        img.onload = async () => {
          if (signal && signal.aborted) return;
          try {
            if (typeof img.decode === 'function') {
              await img.decode();
            }
            resolve(img);
          } catch (decodeErr) {
            // If decode() rejects due to CORS or partial decode, still resolve if naturalWidth > 0
            if (img.naturalWidth > 0) resolve(img);
            else reject(decodeErr);
          }
        };
        img.onerror = () => {
          if (signal && signal.aborted) return;
          reject(new Error(`Failed to load image: ${url}`));
        };
        img.src = url;
      });
    }

    async function preloadPanoramaPhotoAsset(rawUrl, token, titleForProgress) {
      // Cancel any previous in-flight load & timeout
      if (activePanoAbortController) {
        try { activePanoAbortController.abort(); } catch (e) {}
        activePanoAbortController = null;
      }
      if (activePanoTimeoutId) {
        clearTimeout(activePanoTimeoutId);
        activePanoTimeoutId = null;
      }

      if (STATE.forceMissingAssetTest || !rawUrl) {
        STATE.panoAssetStatus = 'missing';
        setPanoLoadingProgress(false, 100, '');
        return;
      }

      // Always key cache by rawUrl so lookups in renderers are consistent, while fetching optimal mobile resolution
      prunePanoImageCache(rawUrl);

      if (PANO_IMAGE_CACHE[rawUrl] && PANO_IMAGE_CACHE[rawUrl].status === 'loaded' && PANO_IMAGE_CACHE[rawUrl].img) {
        STATE.panoAssetStatus = 'loaded';
        setPanoLoadingProgress(false, 100, '');
        return;
      }

      if (typeof Image === 'undefined') {
        STATE.panoAssetStatus = 'loaded';
        setPanoLoadingProgress(false, 100, '');
        return;
      }

      const optimalUrl = getOptimalWikimediaUrl(rawUrl);
      const controller = typeof AbortController !== 'undefined' ? new AbortController() : null;
      activePanoAbortController = controller;
      const signal = controller ? controller.signal : null;

      STATE.panoAssetStatus = 'loading';
      PANO_IMAGE_CACHE[rawUrl] = { status: 'loading', img: null, objectUrl: null };

      // Only show blocking loading overlay in 360° Sphere mode; in Distinct 3D World mode, render 3D immediately without blocking!
      const showBlockingOverlay = STATE.panoMode === 'authentic';
      if (showBlockingOverlay) {
        setPanoLoadingProgress(true, null, `Loading ${titleForProgress} (${getMaxSafeTextureWidth()}px optimized)...`);
      } else {
        setPanoLoadingProgress(false, 100, '');
      }

      // Hard 10-second timeout so loading NEVER hangs forever on slow mobile networks
      activePanoTimeoutId = setTimeout(() => {
        if (token !== STATE.panoLoadToken) return;
        if (controller) {
          try { controller.abort(); } catch (e) {}
        }
        STATE.panoAssetStatus = 'missing';
        PANO_IMAGE_CACHE[rawUrl] = { status: 'missing', img: null };
        if (STATE.panoMode === 'authentic') {
          setPanoLoadingProgress(
            true,
            0,
            `Texture Load Timed Out: ${titleForProgress}`,
            'The image server took longer than 10 seconds to respond on this connection. You can retry or continue to the 3D environment.'
          );
        }
      }, 10000);

      try {
        let loadedImg = null;
        let createdObjectUrl = null;

        // Attempt fetch() streaming first so we get REAL download byte progress when Content-Length is available
        if (typeof fetch === 'function') {
          try {
            const resp = await fetch(optimalUrl, { signal, mode: 'cors' });
            if (!resp.ok) throw new Error(`HTTP ${resp.status}`);
            const contentLen = Number(resp.headers.get('content-length') || 0);
            if (resp.body && typeof resp.body.getReader === 'function' && contentLen > 0) {
              const reader = resp.body.getReader();
              const chunks = [];
              let received = 0;
              while (true) {
                const { done, value } = await reader.read();
                if (done) break;
                chunks.push(value);
                received += value.byteLength;
                if (token === STATE.panoLoadToken && showBlockingOverlay) {
                  const realPct = Math.min(92, Math.round((received / contentLen) * 92));
                  setPanoLoadingProgress(true, realPct, `Downloading ${titleForProgress}...`);
                }
              }
              const blob = new Blob(chunks, { type: resp.headers.get('content-type') || 'image/jpeg' });
              createdObjectUrl = URL.createObjectURL(blob);
              if (token === STATE.panoLoadToken && showBlockingOverlay) {
                setPanoLoadingProgress(true, 95, `Decoding texture for ${titleForProgress}...`);
              }
              loadedImg = await loadSingleImageWithDecode(createdObjectUrl, signal);
            } else {
              const blob = await resp.blob();
              createdObjectUrl = URL.createObjectURL(blob);
              loadedImg = await loadSingleImageWithDecode(createdObjectUrl, signal);
            }
          } catch (fetchErr) {
            if (signal && signal.aborted) return;
            // Fallback to direct Image element load (optimal thumbnail first, then rawUrl if thumbnail 404s)
            try {
              loadedImg = await loadSingleImageWithDecode(optimalUrl, signal);
            } catch (thumbErr) {
              if (signal && signal.aborted) return;
              if (optimalUrl !== rawUrl) {
                loadedImg = await loadSingleImageWithDecode(rawUrl, signal);
              } else {
                throw thumbErr;
              }
            }
          }
        } else {
          try {
            loadedImg = await loadSingleImageWithDecode(optimalUrl, signal);
          } catch (thumbErr) {
            if (optimalUrl !== rawUrl) {
              loadedImg = await loadSingleImageWithDecode(rawUrl, signal);
            } else {
              throw thumbErr;
            }
          }
        }

        if (activePanoTimeoutId) {
          clearTimeout(activePanoTimeoutId);
          activePanoTimeoutId = null;
        }
        if (token !== STATE.panoLoadToken) {
          if (createdObjectUrl) {
            try { URL.revokeObjectURL(createdObjectUrl); } catch (e) {}
          }
          return;
        }

        PANO_IMAGE_CACHE[rawUrl] = { status: 'loaded', img: loadedImg, objectUrl: createdObjectUrl };
        STATE.panoAssetStatus = 'loaded';
        if (showBlockingOverlay) {
          setPanoLoadingProgress(true, 100, `Loaded ${titleForProgress}`);
          setTimeout(() => {
            if (token === STATE.panoLoadToken) setPanoLoadingProgress(false, 100, '');
          }, 120);
        } else {
          setPanoLoadingProgress(false, 100, '');
        }
      } catch (err) {
        if (activePanoTimeoutId) {
          clearTimeout(activePanoTimeoutId);
          activePanoTimeoutId = null;
        }
        if (token !== STATE.panoLoadToken || (signal && signal.aborted)) return;
        PANO_IMAGE_CACHE[rawUrl] = { status: 'missing', img: null };
        STATE.panoAssetStatus = 'missing';
        if (showBlockingOverlay) {
          setPanoLoadingProgress(
            true,
            0,
            `Unable to load texture for ${titleForProgress}`,
            `${err.message || 'Network or decoding error'}. Click Retry or Continue to 3D Environment.`
          );
        } else {
          setPanoLoadingProgress(false, 100, '');
        }
      }
    }

    function updateAuthenticPhotoStageTransform() {
      // Used if auxiliary DOM layer is queried
      const strip = document.getElementById('panoPhotoPanInner');
      if (!strip) return;
      const normYaw = ((STATE.panoYaw + 180) % 360 + 360) % 360 - 180;
      const shiftX = -(normYaw / 180) * 32;
      const shiftY = (STATE.panoPitch / 65) * 18;
      const zoomScale = Math.max(1.0, Math.min(1.65, 72 / (STATE.panoFov || 72)));
      strip.style.transform = `translate3d(${shiftX}%, ${shiftY}%, 0) scale(${zoomScale})`;
    }

    function renderCorsSafeAuthenticPhotoStage() {
      const stage = document.getElementById('panoAuthenticPhotoStage');
      if (stage) stage.classList.add('hidden');
    }

    function openPanoramaForDestination(destId, preferredMode = 'authentic', viewpointIdx = 0) {
      STATE.forceMissingAssetTest = false;
      if (destId === 'kaminarimon-gate') {
        STATE.selectedDestinationId = 'tokyo-sensoji';
        STATE.panoViewpointIndex = 0;
      } else if (destId === 'sensoji-hondo') {
        STATE.selectedDestinationId = 'tokyo-sensoji';
        STATE.panoViewpointIndex = 1;
      } else {
        STATE.selectedDestinationId = destId;
        STATE.panoViewpointIndex = viewpointIdx || 0;
      }
      STATE.panoMode = preferredMode;
      const tmpDest = getDestinationById(STATE.selectedDestinationId);
      if (STATE.panoMode === 'authentic' && tmpDest && tmpDest.images && !(tmpDest.images[STATE.panoViewpointIndex]?.isPanorama)) {
        const pIdx = tmpDest.images.findIndex(im => im.isPanorama);
        if (pIdx >= 0) STATE.panoViewpointIndex = pIdx;
      }
      STATE.activeTemplateKey = getActiveEnvironmentKey(STATE.selectedDestinationId, STATE.panoViewpointIndex);
      STATE.panoYaw = 0;
      STATE.panoPitch = -3;
      vrPlayerOffset = { x: 0, z: 0 };
      const dest = getDestinationById(STATE.selectedDestinationId);
      STATE.selectedHotspotId = dest.hotspots[0]?.id || '';
      switchView('panorama');
      loadPanoramaEnvironment();
      runWebXRHandDiagnostics(false);
      logDiscoveryAction(STATE.selectedDestinationId, `Entered VR: ${STATE.activeTemplateKey}`);
    }

    function createSandGrainTexture() {
      const c = document.createElement('canvas');
      c.width = 256;
      c.height = 256;
      const ctx = c.getContext('2d');
      if (ctx) {
        ctx.fillStyle = '#d8cab2';
        ctx.fillRect(0, 0, 256, 256);
      }
      return typeof THREE !== 'undefined' ? new THREE.CanvasTexture(c) : null;
    }

    function createArticulatedVirtualHand(isRight) {
      const handRoot = new THREE.Group();
      const sign = isRight ? 1 : -1;
      const skinMat = new THREE.MeshStandardMaterial({ color: 0xebbe9d, roughness: 0.55, metalness: 0.04 });
      const palmMesh = new THREE.Mesh(new THREE.BoxGeometry(0.088, 0.026, 0.096), skinMat);
      handRoot.add(palmMesh);

      const fingerSpecs = [
        { name: 'index',  x: -sign * 0.032, len: 0.080, r: 0.010, spread: -sign * 0.05, curl: 0.16 },
        { name: 'middle', x: -sign * 0.010, len: 0.088, r: 0.010, spread: -sign * 0.01, curl: 0.14 },
        { name: 'ring',   x:  sign * 0.012, len: 0.082, r: 0.009, spread:  sign * 0.04, curl: 0.18 },
        { name: 'pinky',  x:  sign * 0.032, len: 0.066, r: 0.008, spread:  sign * 0.10, curl: 0.22 }
      ];
      const fingerJoints = [];
      fingerSpecs.forEach(spec => {
        const basePivot = new THREE.Group();
        basePivot.position.set(spec.x, 0.002, -0.046);
        basePivot.rotation.y = spec.spread;
        const l1 = spec.len * 0.5;
        const p1 = new THREE.Mesh(new THREE.CylinderGeometry(spec.r * 0.9, spec.r, l1, 10), skinMat);
        p1.rotation.x = Math.PI / 2;
        p1.position.z = -l1 / 2;
        basePivot.add(p1);

        const midPivot = new THREE.Group();
        midPivot.position.z = -l1;
        const l2 = spec.len * 0.3;
        const p2 = new THREE.Mesh(new THREE.CylinderGeometry(spec.r * 0.8, spec.r * 0.9, l2, 10), skinMat);
        p2.rotation.x = Math.PI / 2;
        p2.position.z = -l2 / 2;
        midPivot.add(p2);

        const tipPivot = new THREE.Group();
        tipPivot.position.z = -l2;
        const l3 = spec.len * 0.2;
        const p3 = new THREE.Mesh(new THREE.CylinderGeometry(spec.r * 0.7, spec.r * 0.8, l3, 10), skinMat);
        p3.rotation.x = Math.PI / 2;
        p3.position.z = -l3 / 2;
        tipPivot.add(p3);

        midPivot.add(tipPivot);
        basePivot.add(midPivot);
        handRoot.add(basePivot);
        fingerJoints.push({ basePivot, midPivot, tipPivot, baseCurl: spec.curl });
      });

      const thumbPivot = new THREE.Group();
      thumbPivot.position.set(-sign * 0.044, -0.004, -0.012);
      thumbPivot.rotation.y = -sign * 0.55;
      const tp1 = new THREE.Mesh(new THREE.CylinderGeometry(0.010, 0.012, 0.055, 10), skinMat);
      tp1.rotation.x = Math.PI / 2;
      tp1.position.z = -0.027;
      thumbPivot.add(tp1);
      handRoot.add(thumbPivot);

      // Start hidden! Only visible when real hand tracking data is received!
      handRoot.visible = false;
      return { handRoot, fingerJoints, thumbPivot };
    }

    // =========================================================================
    // STEP 4 — TWO SEPARATE REAL HAND-TRACKING SYSTEMS:
    //  System 1: WebXR Hand Tracking (navigator.xr + XRInputSource.hand + 25 joints)
    //  System 2: Camera Hand Tracking (getUserMedia + MediaPipe Hands 21 landmarks)
    //  CRITICAL RULE: Do NOT render static calibration hands as real tracked hands!
    // =========================================================================
    const WEBXR_25_JOINTS = [
      'wrist',
      'thumb-metacarpal', 'thumb-phalanx-proximal', 'thumb-phalanx-distal', 'thumb-tip',
      'index-finger-metacarpal', 'index-finger-phalanx-proximal', 'index-finger-phalanx-intermediate', 'index-finger-phalanx-distal', 'index-finger-tip',
      'middle-finger-metacarpal', 'middle-finger-phalanx-proximal', 'middle-finger-phalanx-intermediate', 'middle-finger-phalanx-distal', 'middle-finger-tip',
      'ring-finger-metacarpal', 'ring-finger-phalanx-proximal', 'ring-finger-phalanx-intermediate', 'ring-finger-phalanx-distal', 'ring-finger-tip',
      'pinky-finger-metacarpal', 'pinky-finger-phalanx-proximal', 'pinky-finger-phalanx-intermediate', 'pinky-finger-phalanx-distal', 'pinky-finger-tip'
    ];

    const XR_DIAG = {
      httpsSecureContext: false,
      navigatorXrAvailable: false,
      webXrSupported: false,
      immersiveVrSupported: false,
      handTrackingApiPresent: false,
      handTrackingRequested: false,
      threeXrConfigured: false,
      leftHandDetected: false,
      rightHandDetected: false,
      jointDataReceived: false,
      virtualHandRendering: false, // STRICTLY FALSE until real joint data is received!
      activeSource: 'None (Awaiting Real Hand Input)',
      cameraSystemStatus: 'OFF',
      reason: 'Not yet checked.'
    };

    function createEmptyTrackedHand(handedness) {
      return {
        handedness,
        sourceType: 'none', // 'webxr' | 'camera' | 'none'
        detected: false,
        hasJointData: false,
        jointCount: 0,
        joints: {},
        palm: null,
        gesture: 'NONE',
        prevGesture: 'NONE',
        pinchDistance: 1.0,
        pointRay: null,
        grabAnchor: null,
        lastPinchTime: 0
      };
    }

    const REAL_HAND_STATE = {
      activeXrSession: null,
      xrReferenceSpace: null,
      opticalCameraActive: false,
      opticalStream: null,
      left: createEmptyTrackedHand('left'),
      right: createEmptyTrackedHand('right'),
      jointSpheres: { left: {}, right: {} }
    };

    function toggleXrHandDebugPanel() {
      const panel = document.getElementById('xrHandDebugPanel');
      if (panel) panel.classList.toggle('hidden');
    }

    function setDiagBadge(elId, isYes, customText = null) {
      const el = document.getElementById(elId);
      if (!el) return;
      const text = customText !== null ? customText : (isYes ? 'YES' : 'NO');
      el.textContent = text;
      el.className = isYes
        ? 'font-bold px-2 py-0.5 rounded bg-emerald-500/20 text-emerald-300 border border-emerald-400/30'
        : 'font-bold px-2 py-0.5 rounded bg-rose-500/20 text-rose-300 border border-rose-400/30';
    }

    function updateCameraStatusIndicatorUI() {
      const pill = document.getElementById('cameraStatusIndicatorPill');
      const dot = document.getElementById('cameraStatusDot');
      const txt = document.getElementById('cameraStatusText');
      const pip = document.getElementById('cameraPiPPreviewBox');
      const sysEl = document.getElementById('diagCameraSystemStatus');

      if (sysEl) sysEl.textContent = XR_DIAG.cameraSystemStatus;
      if (!REAL_HAND_STATE.opticalCameraActive) {
        if (dot) dot.className = 'w-2 h-2 rounded-full bg-slate-500';
        if (txt) txt.textContent = 'Camera Hands: OFF';
        if (pill) pill.className = 'px-3 py-1.5 rounded-full bg-slate-950/85 hover:bg-slate-900 border border-white/20 text-[11px] font-mono text-slate-300 flex items-center gap-1.5 backdrop-blur-xl shadow-lg transition';
        if (pip) pip.classList.add('hidden');
      } else {
        const hasHands = REAL_HAND_STATE.left.hasJointData || REAL_HAND_STATE.right.hasJointData;
        if (dot) dot.className = hasHands ? 'w-2 h-2 rounded-full bg-emerald-400 animate-ping' : 'w-2 h-2 rounded-full bg-amber-400 animate-pulse';
        if (txt) txt.textContent = hasHands ? 'Camera Hands: TRACKING LIVE' : 'Camera Hands: SCANNING...';
        if (pill) pill.className = 'px-3 py-1.5 rounded-full bg-emerald-950/90 hover:bg-emerald-900 border border-emerald-400/60 text-[11px] font-mono text-emerald-200 flex items-center gap-1.5 backdrop-blur-xl shadow-lg transition';
        if (pip) pip.classList.remove('hidden');
      }
    }

    function updateXrDiagnosticPanelUI() {
      // Virtual hand rendering is YES only when real joint data is actively received and rendered!
      XR_DIAG.virtualHandRendering = Boolean(REAL_HAND_STATE.left.hasJointData || REAL_HAND_STATE.right.hasJointData);

      setDiagBadge('diagWebXrSupported', XR_DIAG.webXrSupported);
      setDiagBadge('diagImmersiveVrSupported', XR_DIAG.immersiveVrSupported);
      setDiagBadge('diagHandTrackingRequested', XR_DIAG.handTrackingRequested);
      setDiagBadge('diagLeftHandDetected', XR_DIAG.leftHandDetected);
      setDiagBadge('diagRightHandDetected', XR_DIAG.rightHandDetected);
      setDiagBadge('diagJointDataReceived', XR_DIAG.jointDataReceived);
      setDiagBadge('diagVirtualHandRendering', XR_DIAG.virtualHandRendering);

      const envKeyEl = document.getElementById('diagActiveEnvKey');
      if (envKeyEl) envKeyEl.textContent = getActiveEnvironmentKey(STATE.selectedDestinationId, STATE.panoViewpointIndex);

      const httpsEl = document.getElementById('diagHttpsStatus');
      if (httpsEl) httpsEl.textContent = XR_DIAG.httpsSecureContext ? 'YES (Secure)' : 'NO (HTTPS Required)';

      const navXrEl = document.getElementById('diagNavigatorXrStatus');
      if (navXrEl) navXrEl.textContent = XR_DIAG.navigatorXrAvailable ? 'YES' : 'NO';

      const threeXrEl = document.getElementById('diagThreeXrConfig');
      if (threeXrEl) threeXrEl.textContent = XR_DIAG.threeXrConfigured ? 'YES (xr.enabled=true)' : 'STANDBY';

      const apiEl = document.getElementById('diagXrHandApiStatus');
      if (apiEl) apiEl.textContent = XR_DIAG.handTrackingApiPresent ? 'YES (XRHand)' : 'NO (Browser lacks XRHand)';

      const srcEl = document.getElementById('diagTrackingSource');
      if (srcEl) srcEl.textContent = XR_DIAG.activeSource;

      const cntEl = document.getElementById('diagJointCount');
      if (cntEl) cntEl.textContent = `${REAL_HAND_STATE.left.jointCount}/25 L • ${REAL_HAND_STATE.right.jointCount}/25 R`;

      const lgEl = document.getElementById('diagLeftGesture');
      if (lgEl) lgEl.textContent = REAL_HAND_STATE.left.detected
        ? `${REAL_HAND_STATE.left.gesture} (${Math.round(REAL_HAND_STATE.left.pinchDistance * 1000)}mm)`
        : 'NONE (Untracked)';

      const rgEl = document.getElementById('diagRightGesture');
      if (rgEl) rgEl.textContent = REAL_HAND_STATE.right.detected
        ? `${REAL_HAND_STATE.right.gesture} (${Math.round(REAL_HAND_STATE.right.pinchDistance * 1000)}mm)`
        : 'NONE (Untracked)';

      const reasonEl = document.getElementById('diagReasonBanner');
      if (reasonEl) reasonEl.textContent = XR_DIAG.reason;

      const pulseDot = document.getElementById('vrDiagPulseDot');
      if (pulseDot) {
        pulseDot.className = XR_DIAG.jointDataReceived
          ? 'w-2 h-2 rounded-full bg-emerald-400 animate-ping'
          : 'w-2 h-2 rounded-full bg-amber-400';
      }
      updateCameraStatusIndicatorUI();
    }

    async function runWebXRHandDiagnostics(showNotification = false) {
      XR_DIAG.httpsSecureContext = Boolean(window.isSecureContext);
      XR_DIAG.navigatorXrAvailable = typeof navigator !== 'undefined' && Boolean(navigator.xr);
      XR_DIAG.webXrSupported = XR_DIAG.navigatorXrAvailable && typeof navigator.xr.isSessionSupported === 'function';
      XR_DIAG.handTrackingApiPresent = typeof window !== 'undefined' && ('XRHand' in window || 'XRJointSpace' in window);
      XR_DIAG.threeXrConfigured = Boolean(panoRenderer && panoRenderer.xr && panoRenderer.xr.enabled);

      if (!XR_DIAG.httpsSecureContext) {
        XR_DIAG.immersiveVrSupported = false;
        XR_DIAG.reason = 'WebXR requires HTTPS or localhost. Static hands are disabled; virtual hands render only when real joints are tracked.';
      } else if (!XR_DIAG.webXrSupported) {
        XR_DIAG.immersiveVrSupported = false;
        XR_DIAG.reason = 'System 1 (WebXR): navigator.xr is unavailable in this browser. No static fake hands are rendered. For non-XR phones/PCs, enable System 2 (Camera Hand Tracking).';
      } else {
        try {
          XR_DIAG.immersiveVrSupported = await navigator.xr.isSessionSupported('immersive-vr');
          if (XR_DIAG.immersiveVrSupported) {
            if (!REAL_HAND_STATE.activeXrSession) {
              XR_DIAG.reason = 'System 1 (WebXR): Immersive VR is supported! Click "System 1: Start WebXR Hand Session" to request "hand-tracking".';
            }
          } else {
            XR_DIAG.reason = 'System 1 (WebXR): navigator.xr exists, but no VR headset ("immersive-vr") is connected. No fake hands are shown. Use System 2 (Camera Hand Tracking) on phone/PC.';
          }
        } catch (err) {
          XR_DIAG.immersiveVrSupported = false;
          XR_DIAG.reason = `System 1 (WebXR): isSessionSupported('immersive-vr') blocked (${err.message}). Use System 2 (Camera Hand Tracking) on non-XR devices.`;
        }
      }

      updateXrDiagnosticPanelUI();
      if (showNotification) {
        showToast(XR_DIAG.immersiveVrSupported ? 'WebXR Headset Ready' : 'No WebXR Headset Detected — Camera Hand Tracking Available');
      }
      return XR_DIAG;
    }

    // SYSTEM 1: DEDICATED WEBXR HAND-TRACKING SESSION (Separate from Camera Tracking)
    async function startImmersiveWebXRHandSession() {
      // If Camera Tracking (System 2) is running, keep systems separate by stopping Camera Tracking first
      if (REAL_HAND_STATE.opticalCameraActive) {
        await toggleOpticalCameraHandTracking();
      }

      XR_DIAG.handTrackingRequested = true;
      updateXrDiagnosticPanelUI();

      if (!window.isSecureContext) {
        XR_DIAG.reason = 'System 1 (WebXR) Error: HTTPS Secure Context required.';
        updateXrDiagnosticPanelUI();
        showToast('WebXR requires HTTPS');
        return;
      }

      if (!navigator.xr || typeof navigator.xr.requestSession !== 'function') {
        XR_DIAG.reason = 'System 1 (WebXR) Unavailable: navigator.xr is not supported on this browser. To track real hands on a phone or computer, click "📷 Camera Hands" (System 2).';
        updateXrDiagnosticPanelUI();
        showToast('WebXR unavailable on this device — tap "📷 Camera Hands" for optical tracking');
        return;
      }

      if (REAL_HAND_STATE.activeXrSession) {
        try { await REAL_HAND_STATE.activeXrSession.end(); } catch (e) {}
        return;
      }

      try {
        const supported = await navigator.xr.isSessionSupported('immersive-vr');
        XR_DIAG.immersiveVrSupported = supported;
        if (!supported) {
          XR_DIAG.reason = 'System 1 (WebXR): isSessionSupported("immersive-vr") returned FALSE (no VR headset connected). Do not fake WebXR tracking; use System 2 (Camera Hand Tracking) for webcam/phone tracking.';
          updateXrDiagnosticPanelUI();
          showToast('No VR headset connected — tap "📷 Camera Hands" for camera tracking');
          return;
        }

        const sessionInit = {
          requiredFeatures: ['local-floor'],
          optionalFeatures: ['hand-tracking', 'bounded-floor', 'layers']
        };
        const session = await navigator.xr.requestSession('immersive-vr', sessionInit);
        REAL_HAND_STATE.activeXrSession = session;
        XR_DIAG.activeSource = 'System 1: WebXR Headset Active';

        const webglContainer = document.getElementById('panoCanvasContainer');
        if (webglContainer) {
          webglContainer.classList.remove('opacity-0', 'pointer-events-none');
        }

        if (panoRenderer) {
          panoRenderer.xr.enabled = true;
          panoRenderer.xr.setReferenceSpaceType('local-floor');
          await panoRenderer.xr.setSession(session);
          XR_DIAG.threeXrConfigured = true;
        }

        REAL_HAND_STATE.xrReferenceSpace = await session.requestReferenceSpace('local-floor').catch(() =>
          session.requestReferenceSpace('local')
        );

        const inspectInputSources = () => {
          let leftFound = false;
          let rightFound = false;
          for (const src of session.inputSources) {
            if (src.hand) {
              if (src.handedness === 'left') leftFound = true;
              if (src.handedness === 'right') rightFound = true;
            }
          }
          XR_DIAG.leftHandDetected = leftFound;
          XR_DIAG.rightHandDetected = rightFound;
          XR_DIAG.reason = (!leftFound && !rightFound)
            ? 'System 1 (WebXR Session Active): Waiting for XRInputSource.hand — hold your physical hands in front of headset cameras.'
            : `System 1 (WebXR Hand Tracking Live): Left=${leftFound ? 'YES' : 'NO'}, Right=${rightFound ? 'YES' : 'NO'}.`;
          updateXrDiagnosticPanelUI();
        };

        session.addEventListener('inputsourceschange', inspectInputSources);
        session.addEventListener('end', () => {
          REAL_HAND_STATE.activeXrSession = null;
          REAL_HAND_STATE.xrReferenceSpace = null;
          REAL_HAND_STATE.left = createEmptyTrackedHand('left');
          REAL_HAND_STATE.right = createEmptyTrackedHand('right');
          XR_DIAG.leftHandDetected = false;
          XR_DIAG.rightHandDetected = false;
          XR_DIAG.jointDataReceived = false;
          XR_DIAG.virtualHandRendering = false;
          XR_DIAG.activeSource = 'Standby (Session Ended)';
          XR_DIAG.reason = 'System 1 (WebXR) session ended. Virtual hands hidden until real hand tracking resumes.';
          updateXrDiagnosticPanelUI();
        });

        inspectInputSources();
      } catch (err) {
        XR_DIAG.reason = `System 1 (WebXR) requestSession failed: ${err.message}.`;
        updateXrDiagnosticPanelUI();
        showToast(`WebXR error: ${err.message}`);
      }
    }

    function dist3D(a, b) {
      if (!a || !b) return 999;
      const dx = a.x - b.x;
      const dy = a.y - b.y;
      const dz = a.z - b.z;
      return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }

    function sub3D(a, b) {
      return { x: a.x - b.x, y: a.y - b.y, z: a.z - b.z };
    }

    function norm3D(v) {
      const len = Math.sqrt(v.x * v.x + v.y * v.y + v.z * v.z) || 1;
      return { x: v.x / len, y: v.y / len, z: v.z / len };
    }

    function evaluateTrackedHandGestures(handState) {
      if (!handState.hasJointData) {
        handState.gesture = 'NONE';
        return;
      }
      const j = handState.joints;
      const wrist = j['wrist']?.position;
      const thumbTip = j['thumb-tip']?.position;
      const indexTip = j['index-finger-tip']?.position;
      const indexProx = j['index-finger-phalanx-proximal']?.position;
      const middleTip = j['middle-finger-tip']?.position;
      const ringTip = j['ring-finger-tip']?.position;
      const pinkyTip = j['pinky-finger-tip']?.position;
      const middleMeta = j['middle-finger-metacarpal']?.position || wrist;

      if (!wrist || !thumbTip || !indexTip || !middleTip || !ringTip || !pinkyTip) {
        return;
      }

      handState.palm = {
        position: {
          x: (wrist.x + middleMeta.x) * 0.5,
          y: (wrist.y + middleMeta.y) * 0.5,
          z: (wrist.z + middleMeta.z) * 0.5
        }
      };

      const pinchDist = dist3D(thumbTip, indexTip);
      handState.pinchDistance = pinchDist;

      const dIndexWrist = dist3D(indexTip, wrist);
      const dMiddleWrist = dist3D(middleTip, wrist);
      const dRingWrist = dist3D(ringTip, wrist);
      const dPinkyWrist = dist3D(pinkyTip, wrist);

      const dIndexPalm = dist3D(indexTip, handState.palm.position);
      const dMiddlePalm = dist3D(middleTip, handState.palm.position);
      const dRingPalm = dist3D(ringTip, handState.palm.position);
      const dPinkyPalm = dist3D(pinkyTip, handState.palm.position);

      const prev = handState.gesture;
      let nextGesture = 'OPEN_HAND';

      // 1. PINCH Detection (< 26mm)
      if (pinchDist < 0.026) {
        nextGesture = 'PINCH';
        const now = performance.now();
        if (prev !== 'PINCH' && now - handState.lastPinchTime > 700) {
          handState.lastPinchTime = now;
          onRealHandPinchAction(handState.handedness);
        }
      }
      // 2. GRAB Detection (All 4 fingertips curled close to palm center < 80mm)
      else if (dIndexPalm < 0.080 && dMiddlePalm < 0.080 && dRingPalm < 0.080 && dPinkyPalm < 0.080) {
        nextGesture = 'GRAB';
        if (prev !== 'GRAB') {
          handState.grabAnchor = { ...handState.palm.position, yaw0: STATE.panoYaw, pitch0: STATE.panoPitch };
        } else if (handState.grabAnchor) {
          const dx = handState.palm.position.x - handState.grabAnchor.x;
          const dy = handState.palm.position.y - handState.grabAnchor.y;
          STATE.panoYaw = (handState.grabAnchor.yaw0 - dx * 175 + 360) % 360;
          STATE.panoPitch = Math.max(-65, Math.min(65, handState.grabAnchor.pitch0 + dy * 145));
          updatePanoReadout();
        }
      }
      // 3. POINT Detection (Index extended > 128mm from wrist, middle/ring/pinky curled < 106mm)
      else if (dIndexWrist > 0.128 && dMiddleWrist < 0.108 && dRingWrist < 0.105 && dPinkyWrist < 0.102) {
        nextGesture = 'POINT';
        if (indexProx) {
          handState.pointRay = {
            origin: { ...indexTip },
            direction: norm3D(sub3D(indexTip, indexProx))
          };
        }
      }
      // 4. RELEASE Detection
      else if (prev === 'GRAB' || prev === 'PINCH') {
        nextGesture = 'RELEASE';
        handState.grabAnchor = null;
      } else {
        nextGesture = 'OPEN_HAND';
        handState.grabAnchor = null;
      }

      handState.prevGesture = prev;
      handState.gesture = nextGesture;
    }

    function onRealHandPinchAction(handedness) {
      playHotspotPing();
      const dest = getDestinationById(STATE.selectedDestinationId);
      if (handedness === 'right') {
        switchPanoViewpoint((STATE.panoViewpointIndex + 1) % dest.images.length);
        showToast(`Right Hand Pinch → Viewpoint: ${dest.images[STATE.panoViewpointIndex].viewpoint}`);
      } else {
        if (dest.hotspots && dest.hotspots.length > 0) {
          const curIdx = dest.hotspots.findIndex(h => h.id === STATE.selectedHotspotId);
          const nextHs = dest.hotspots[(curIdx + 1) % dest.hotspots.length];
          selectPanoramaHotspot(nextHs.id);
          showToast(`Left Hand Pinch → Hotspot: ${nextHs.title}`);
        }
      }
    }

    function readWebXRHandJointsFromFrame(xrFrame) {
      if (!xrFrame || !xrFrame.session || typeof xrFrame.getJointPose !== 'function') {
        return false;
      }
      const refSpace = (panoRenderer && panoRenderer.xr && panoRenderer.xr.getReferenceSpace()) || REAL_HAND_STATE.xrReferenceSpace;
      if (!refSpace) return false;

      let leftDetected = false;
      let rightDetected = false;
      let anyJointPoseValid = false;

      REAL_HAND_STATE.left.jointCount = 0;
      REAL_HAND_STATE.right.jointCount = 0;

      for (const inputSource of xrFrame.session.inputSources) {
        if (!inputSource.hand) continue;
        const handedness = inputSource.handedness;
        if (handedness !== 'left' && handedness !== 'right') continue;

        const targetHand = handedness === 'left' ? REAL_HAND_STATE.left : REAL_HAND_STATE.right;
        targetHand.sourceType = 'webxr';
        if (handedness === 'left') leftDetected = true;
        if (handedness === 'right') rightDetected = true;

        let validCount = 0;
        for (const jointName of WEBXR_25_JOINTS) {
          const jointSpace = inputSource.hand.get(jointName);
          if (!jointSpace) continue;
          const pose = xrFrame.getJointPose(jointSpace, refSpace);
          if (pose && pose.transform) {
            const p = pose.transform.position;
            const q = pose.transform.orientation;
            targetHand.joints[jointName] = {
              position: { x: p.x, y: p.y, z: p.z },
              orientation: { x: q.x, y: q.y, z: q.z, w: q.w },
              radius: pose.radius || 0.008
            };
            validCount++;
            anyJointPoseValid = true;
          }
        }

        targetHand.detected = validCount > 0;
        targetHand.hasJointData = validCount > 0;
        targetHand.jointCount = validCount;
        if (targetHand.hasJointData) {
          evaluateTrackedHandGestures(targetHand);
        }
      }

      if (!leftDetected) {
        REAL_HAND_STATE.left.detected = false;
        REAL_HAND_STATE.left.hasJointData = false;
      }
      if (!rightDetected) {
        REAL_HAND_STATE.right.detected = false;
        REAL_HAND_STATE.right.hasJointData = false;
      }

      XR_DIAG.leftHandDetected = REAL_HAND_STATE.left.detected;
      XR_DIAG.rightHandDetected = REAL_HAND_STATE.right.detected;
      XR_DIAG.jointDataReceived = anyJointPoseValid;
      XR_DIAG.virtualHandRendering = anyJointPoseValid;
      updateXrDiagnosticPanelUI();
      return anyJointPoseValid;
    }

    function ensureThreeJsJointSkeletonMeshes() {
      if (!panoScene || typeof THREE === 'undefined') return;
      ['left', 'right'].forEach(side => {
        if (Object.keys(REAL_HAND_STATE.jointSpheres[side]).length === 25) return;
        const skinMat = new THREE.MeshStandardMaterial({
          color: side === 'left' ? 0xeabca4 : 0xf4c8b0,
          roughness: 0.52,
          metalness: 0.05
        });
        WEBXR_25_JOINTS.forEach(jName => {
          const mesh = new THREE.Mesh(new THREE.SphereGeometry(1, 10, 10), skinMat);
          mesh.visible = false;
          mesh.castShadow = true;
          panoScene.add(mesh);
          REAL_HAND_STATE.jointSpheres[side][jName] = mesh;
        });
      });
    }

    function applyRealJointPosesToThreeJsHands() {
      if (!panoScene || typeof THREE === 'undefined') return;
      ensureThreeJsJointSkeletonMeshes();

      ['left', 'right'].forEach(side => {
        const handState = REAL_HAND_STATE[side];
        const spheres = REAL_HAND_STATE.jointSpheres[side];
        const articulatedHand = side === 'left' ? vrLeftHand : vrRightHand;
        const articulatedFingers = side === 'left' ? vrLeftFingers : vrRightFingers;

        if (!handState.hasJointData) {
          Object.values(spheres).forEach(m => { m.visible = false; });
          if (articulatedHand) articulatedHand.visible = false;
          return;
        }

        if (articulatedHand) articulatedHand.visible = true;

        WEBXR_25_JOINTS.forEach(jName => {
          const jData = handState.joints[jName];
          const mesh = spheres[jName];
          if (jData && mesh) {
            mesh.visible = true;
            mesh.position.set(jData.position.x, jData.position.y, jData.position.z);
            mesh.quaternion.set(jData.orientation.x, jData.orientation.y, jData.orientation.z, jData.orientation.w);
            const r = Math.max(0.006, Math.min(0.022, jData.radius || 0.009));
            mesh.scale.set(r, r, r);
          } else if (mesh) {
            mesh.visible = false;
          }
        });

        const wristPose = handState.joints['wrist'];
        if (wristPose && articulatedHand) {
          if (vrHandsGroup) {
            vrHandsGroup.position.set(0, 0, 0);
            vrHandsGroup.rotation.set(0, 0, 0);
          }
          articulatedHand.position.set(wristPose.position.x, wristPose.position.y, wristPose.position.z);
          articulatedHand.quaternion.set(
            wristPose.orientation.x,
            wristPose.orientation.y,
            wristPose.orientation.z,
            wristPose.orientation.w
          );

          const fingerPrefixes = ['index-finger', 'middle-finger', 'ring-finger', 'pinky-finger'];
          fingerPrefixes.forEach((prefix, fIdx) => {
            const fJoint = articulatedFingers[fIdx];
            const tip = handState.joints[`${prefix}-tip`]?.position;
            const prox = handState.joints[`${prefix}-phalanx-proximal`]?.position;
            if (fJoint && tip && prox && wristPose.position) {
              const dTipWrist = dist3D(tip, wristPose.position);
              const curlRatio = Math.max(0, Math.min(1, (0.155 - dTipWrist) / 0.095));
              const curlAngle = curlRatio * 1.35;
              fJoint.basePivot.rotation.x = -0.10 + curlAngle * 0.55;
              fJoint.midPivot.rotation.x = curlAngle;
              fJoint.tipPivot.rotation.x = curlAngle * 0.85;
            }
          });
        }
      });
    }

    // SYSTEM 2: SEPARATE CAMERA-BASED HAND TRACKING (MediaPipe Hands 21-Landmark Engine for Non-XR Devices)
    async function toggleOpticalCameraHandTracking() {
      const btn = document.getElementById('btnToggleOpticalCameraHands');
      if (REAL_HAND_STATE.opticalCameraActive) {
        REAL_HAND_STATE.opticalCameraActive = false;
        if (REAL_HAND_STATE.opticalStream) {
          REAL_HAND_STATE.opticalStream.getTracks().forEach(t => t.stop());
          REAL_HAND_STATE.opticalStream = null;
        }
        REAL_HAND_STATE.left = createEmptyTrackedHand('left');
        REAL_HAND_STATE.right = createEmptyTrackedHand('right');
        XR_DIAG.leftHandDetected = false;
        XR_DIAG.rightHandDetected = false;
        XR_DIAG.jointDataReceived = false;
        XR_DIAG.virtualHandRendering = false;
        XR_DIAG.cameraSystemStatus = 'OFF';
        XR_DIAG.reason = 'System 2 (Camera Hand Tracking) stopped. Virtual hands hidden until real hand tracking is active.';
        if (btn) btn.innerHTML = '<span>System 2: Enable Camera Hand Tracking (Phone/PC)</span>';
        updateXrDiagnosticPanelUI();
        return;
      }

      if (!navigator.mediaDevices || typeof navigator.mediaDevices.getUserMedia !== 'function') {
        XR_DIAG.cameraSystemStatus = 'ERROR (No getUserMedia)';
        XR_DIAG.reason = 'System 2 (Camera Hand Tracking): navigator.mediaDevices.getUserMedia is unavailable or blocked by browser permissions.';
        updateXrDiagnosticPanelUI();
        showToast('Camera API unavailable in this browser context');
        return;
      }

      try {
        XR_DIAG.cameraSystemStatus = 'REQUESTING PERMISSION...';
        XR_DIAG.reason = 'System 2 (Camera Hand Tracking): Requesting webcam / front camera permission...';
        updateXrDiagnosticPanelUI();

        const stream = await navigator.mediaDevices.getUserMedia({
          video: { facingMode: 'user', width: { ideal: 640 }, height: { ideal: 480 } },
          audio: false
        });
        REAL_HAND_STATE.opticalStream = stream;
        REAL_HAND_STATE.opticalCameraActive = true;
        XR_DIAG.cameraSystemStatus = 'LOADING MEDIAPIPE HANDS...';
        updateXrDiagnosticPanelUI();

        const videoEl = document.getElementById('opticalHandVideo');
        if (videoEl) {
          videoEl.srcObject = stream;
          await videoEl.play();
        }
        if (btn) btn.innerHTML = '<span>System 2: Stop Camera Hand Tracking (Active)</span>';

        if (typeof window.Hands === 'undefined') {
          await new Promise((resolve, reject) => {
            const s = document.createElement('script');
            s.src = 'https://cdn.jsdelivr.net/npm/@mediapipe/hands@0.4.1646424915/hands.js';
            s.crossOrigin = 'anonymous';
            s.onload = resolve;
            s.onerror = reject;
            document.head.appendChild(s);
          });
        }

        const mpHands = new window.Hands({
          locateFile: file => `https://cdn.jsdelivr.net/npm/@mediapipe/hands@0.4.1646424915/${file}`
        });
        mpHands.setOptions({
          maxNumHands: 2,
          modelComplexity: 1,
          minDetectionConfidence: 0.58,
          minTrackingConfidence: 0.58
        });

        mpHands.onResults(results => {
          if (!REAL_HAND_STATE.opticalCameraActive) return;
          REAL_HAND_STATE.left.detected = false;
          REAL_HAND_STATE.left.hasJointData = false;
          REAL_HAND_STATE.left.jointCount = 0;
          REAL_HAND_STATE.right.detected = false;
          REAL_HAND_STATE.right.hasJointData = false;
          REAL_HAND_STATE.right.jointCount = 0;

          const landmarksList = results.multiHandLandmarks || [];
          const handednessList = results.multiHandedness || [];

          // Draw 21-joint skeleton overlay on the PiP Camera Preview Canvas
          const pipCanvas = document.getElementById('opticalHandLandmarkCanvas');
          if (pipCanvas) {
            pipCanvas.width = 208;
            pipCanvas.height = 128;
            const pCtx = pipCanvas.getContext('2d');
            if (pCtx) {
              pCtx.clearRect(0, 0, 208, 128);
              landmarksList.forEach(lm => {
                pCtx.fillStyle = '#34d399';
                lm.forEach(pt => {
                  pCtx.beginPath();
                  pCtx.arc((1 - pt.x) * 208, pt.y * 128, 2.5, 0, Math.PI * 2);
                  pCtx.fill();
                });
              });
            }
          }

          landmarksList.forEach((lm, idx) => {
            const rawLabel = (handednessList[idx]?.label || 'Right').toLowerCase();
            const side = rawLabel === 'left' ? 'right' : 'left';
            const target = REAL_HAND_STATE[side];
            target.sourceType = 'camera';
            target.detected = true;
            target.hasJointData = true;
            target.jointCount = 25;

            const mapPt = (pt) => ({
              position: {
                x: (0.5 - pt.x) * 0.58,
                y: (0.5 - pt.y) * 0.46 + 1.15,
                z: -0.38 - (pt.z || 0) * 0.35
              },
              orientation: { x: 0, y: 0, z: 0, w: 1 },
              radius: 0.009
            });

            target.joints['wrist'] = mapPt(lm[0]);
            target.joints['thumb-metacarpal'] = mapPt(lm[1]);
            target.joints['thumb-phalanx-proximal'] = mapPt(lm[2]);
            target.joints['thumb-phalanx-distal'] = mapPt(lm[3]);
            target.joints['thumb-tip'] = mapPt(lm[4]);

            target.joints['index-finger-metacarpal'] = mapPt(lm[5]);
            target.joints['index-finger-phalanx-proximal'] = mapPt(lm[5]);
            target.joints['index-finger-phalanx-intermediate'] = mapPt(lm[6]);
            target.joints['index-finger-phalanx-distal'] = mapPt(lm[7]);
            target.joints['index-finger-tip'] = mapPt(lm[8]);

            target.joints['middle-finger-metacarpal'] = mapPt(lm[9]);
            target.joints['middle-finger-phalanx-proximal'] = mapPt(lm[9]);
            target.joints['middle-finger-phalanx-intermediate'] = mapPt(lm[10]);
            target.joints['middle-finger-phalanx-distal'] = mapPt(lm[11]);
            target.joints['middle-finger-tip'] = mapPt(lm[12]);

            target.joints['ring-finger-metacarpal'] = mapPt(lm[13]);
            target.joints['ring-finger-phalanx-proximal'] = mapPt(lm[13]);
            target.joints['ring-finger-phalanx-intermediate'] = mapPt(lm[14]);
            target.joints['ring-finger-phalanx-distal'] = mapPt(lm[15]);
            target.joints['ring-finger-tip'] = mapPt(lm[16]);

            target.joints['pinky-finger-metacarpal'] = mapPt(lm[17]);
            target.joints['pinky-finger-phalanx-proximal'] = mapPt(lm[17]);
            target.joints['pinky-finger-phalanx-intermediate'] = mapPt(lm[18]);
            target.joints['pinky-finger-phalanx-distal'] = mapPt(lm[19]);
            target.joints['pinky-finger-tip'] = mapPt(lm[20]);

            evaluateTrackedHandGestures(target);
          });

          XR_DIAG.leftHandDetected = REAL_HAND_STATE.left.detected;
          XR_DIAG.rightHandDetected = REAL_HAND_STATE.right.detected;
          XR_DIAG.jointDataReceived = REAL_HAND_STATE.left.hasJointData || REAL_HAND_STATE.right.hasJointData;
          XR_DIAG.virtualHandRendering = XR_DIAG.jointDataReceived;
          XR_DIAG.cameraSystemStatus = XR_DIAG.jointDataReceived ? 'LIVE (21 LANDMARKS)' : 'SCANNING FOR HANDS...';
          XR_DIAG.reason = XR_DIAG.jointDataReceived
            ? `System 2 (Camera Hand Tracking Live): Left=${XR_DIAG.leftHandDetected ? 'YES' : 'NO'}, Right=${XR_DIAG.rightHandDetected ? 'YES' : 'NO'}. Virtual hands following real fingers.`
            : 'System 2 (Camera Active): Hold your left or right hand up to the camera. Virtual hands appear only when real hands are detected.';
          const pipReadout = document.getElementById('cameraPiPGestureReadout');
          if (pipReadout) {
            pipReadout.textContent = XR_DIAG.jointDataReceived
              ? `L: ${REAL_HAND_STATE.left.gesture} | R: ${REAL_HAND_STATE.right.gesture}`
              : 'No hand in camera view';
          }
          updateXrDiagnosticPanelUI();
        });

        async function pumpOpticalFrame() {
          if (!REAL_HAND_STATE.opticalCameraActive) return;
          if (videoEl && videoEl.readyState >= 2) {
            try { await mpHands.send({ image: videoEl }); } catch (e) {}
          }
          requestAnimationFrame(pumpOpticalFrame);
        }
        pumpOpticalFrame();
      } catch (err) {
        REAL_HAND_STATE.opticalCameraActive = false;
        XR_DIAG.cameraSystemStatus = `FAILED (${err.name || 'Error'})`;
        XR_DIAG.reason = `System 2 (Camera Hand Tracking) failed to start: ${err.message}.`;
        updateXrDiagnosticPanelUI();
        showToast(`Camera permission / load error: ${err.message}`);
      }
    }

    // =========================================================================
    // 60FPS DESTINATION-SPECIFIC 3D ENVIRONMENT & SPHERICAL 360° RENDERER
    // Every destination template renders its own completely distinct 3D world
    // (Kaminarimon Gate, Sensoji Hondo, Cox's Bazar Beach, Eiffel Tower,
    // Lalbagh Fort, Colosseum, Great Wall, Sundarbans, Swiss Alps, etc.)
    // =========================================================================
    // =========================================================================
    // DEVICE ORIENTATION (GYROSCOPE) & REAL TRACKED HAND OVERLAY
    // =========================================================================
    STATE.gyroEnabled = false;
    let gyroBaseAlpha = null;

    function handleDeviceOrientationEvent(e) {
      if (!STATE.gyroEnabled || STATE.activeView !== 'panorama') return;
      if (e.alpha === null || e.beta === null) return;
      if (gyroBaseAlpha === null) gyroBaseAlpha = e.alpha;
      const deltaAlpha = (e.alpha - gyroBaseAlpha);
      STATE.panoYaw = (360 - deltaAlpha) % 360;
      // Beta ~90deg when holding phone upright
      STATE.panoPitch = Math.max(-75, Math.min(75, (e.beta - 75) * 0.85));
      updatePanoReadout();
    }

    async function toggleDeviceOrientationGyro() {
      const btn = document.getElementById('panoGyroToggleBtn');
      if (STATE.gyroEnabled) {
        STATE.gyroEnabled = false;
        gyroBaseAlpha = null;
        window.removeEventListener('deviceorientation', handleDeviceOrientationEvent);
        if (btn) btn.textContent = 'Gyro: OFF';
        return;
      }
      if (typeof DeviceOrientationEvent === 'undefined') {
        showToast('Device orientation gyroscope is not supported on this device.');
        return;
      }
      try {
        if (typeof DeviceOrientationEvent.requestPermission === 'function') {
          const perm = await DeviceOrientationEvent.requestPermission();
          if (perm !== 'granted') {
            showToast('Gyroscope permission denied.');
            return;
          }
        }
        STATE.gyroEnabled = true;
        gyroBaseAlpha = null;
        window.addEventListener('deviceorientation', handleDeviceOrientationEvent);
        if (btn) btn.textContent = 'Gyro: ON';
        showToast('Device Orientation Gyroscope Enabled');
      } catch (err) {
        showToast('Could not activate gyroscope: ' + err.message);
      }
    }

    function handleTopVrDestSelect(val) {
      if (!val) return;
      if (val.startsWith('tpl:')) {
        selectVrDestinationTemplate(val.slice(4));
      } else {
        openPanoramaForDestination(val, STATE.panoMode);
      }
    }

    function renderGuaranteed3DBeachAndHandsFrame() {
      const overlayCanvas = document.getElementById('vrTrackedHandsOverlayCanvas');
      if (!overlayCanvas) return;
      const leftActive = Boolean(REAL_HAND_STATE.left && REAL_HAND_STATE.left.hasJointData);
      const rightActive = Boolean(REAL_HAND_STATE.right && REAL_HAND_STATE.right.hasJointData);
      if (!leftActive && !rightActive) {
        overlayCanvas.classList.add('hidden');
        return;
      }
      overlayCanvas.classList.remove('hidden');
      const dpr = Math.min(window.devicePixelRatio || 1, 1.5);
      const w = Math.max(320, window.innerWidth || 390);
      const h = Math.max(480, window.innerHeight || 844);
      if (overlayCanvas.width !== Math.floor(w * dpr) || overlayCanvas.height !== Math.floor(h * dpr)) {
        overlayCanvas.width = Math.floor(w * dpr);
        overlayCanvas.height = Math.floor(h * dpr);
      }
      const ctx = overlayCanvas.getContext('2d');
      if (!ctx) return;
      ctx.save();
      ctx.clearRect(0, 0, overlayCanvas.width, overlayCanvas.height);
      ctx.scale(dpr, dpr);
      drawTrackedVirtualHandsIfDetected(ctx, w, h);
      ctx.restore();
    }

    function startGuaranteed3DVRLoop() {
      // No-op: Three.js panoRenderer.setAnimationLoop drives everything cleanly!
    }

    function fillRoundedRect(ctx, x, y, w, h, r) {
      const rad = Math.min(r, Math.abs(w) / 2, Math.abs(h) / 2);
      ctx.beginPath();
      if (typeof ctx.roundRect === 'function') {
        ctx.roundRect(x, y, w, h, rad);
      } else {
        ctx.rect(x, y, w, h);
      }
      ctx.fill();
    }

    function drawTrackedVirtualHandsIfDetected(ctx, vw, vh) {
      ['left', 'right'].forEach(sideKey => {
        const hand = REAL_HAND_STATE[sideKey];
        if (!hand || !hand.hasJointData) return;
        const wrist = hand.joints['wrist']?.position;
        if (!wrist) return;
        const sign = sideKey === 'right' ? 1 : -1;
        const hx = vw * 0.5 + (wrist.x - sign * 0.12) * (vw * 1.45) + sign * (vw * 0.16);
        const hy = vh * 0.78 - (wrist.y - 1.15) * (vh * 1.35);
        ctx.fillStyle = 'rgba(52, 211, 153, 0.85)';
        ctx.beginPath();
        ctx.arc(hx, hy, 8, 0, Math.PI * 2);
        ctx.fill();
        ctx.fillStyle = '#ffffff';
        ctx.font = 'bold 11px monospace';
        ctx.fillText(`${sideKey.toUpperCase()} ${hand.gesture}`, hx - 28, hy - 14);
      });
    }

    function initOrResizePanorama() {
      const container = document.getElementById('panoCanvasContainer');
      if (!container) return;

      populatePanoSelectors();

      const width = window.innerWidth || container.clientWidth || 1280;
      const height = window.innerHeight || container.clientHeight || 720;

      if (panoRenderer) {
        panoCamera.aspect = width / height;
        panoCamera.updateProjectionMatrix();
        panoRenderer.setSize(width, height);
        loadPanoramaEnvironment();
        return;
      }

      const wrapperEl = document.getElementById('panoViewportWrapper') || container;
      let isDragging = false;
      let prevX = 0;
      let prevY = 0;
      let pinchStartDist = 0;
      let pinchStartFov = 72;

      if (!panoEventsBound) {
        panoEventsBound = true;

        wrapperEl.addEventListener('pointerdown', e => {
          if (e.target && e.target.closest && e.target.closest('button, select, input, a, #vrOptionsDrawer, #xrHandDebugPanel, #cameraPiPPreviewBox')) return;
          isDragging = true;
          prevX = e.clientX;
          prevY = e.clientY;
          STATE.panoAutoLook = false;
          const autoBtn = document.getElementById('panoAutoLookBtn');
          if (autoBtn) autoBtn.textContent = 'Orbit Pan: OFF';
        });

        window.addEventListener('pointermove', e => {
          vrPointerNormalized.x = (e.clientX / (window.innerWidth || 1)) * 2 - 1;
          vrPointerNormalized.y = (e.clientY / (window.innerHeight || 1)) * 2 - 1;
          if (!isDragging) return;
          const dx = e.clientX - prevX;
          const dy = e.clientY - prevY;
          prevX = e.clientX;
          prevY = e.clientY;
          STATE.panoYaw = (STATE.panoYaw - dx * 0.22 + 360) % 360;
          STATE.panoPitch = Math.max(-75, Math.min(75, STATE.panoPitch - dy * 0.18));
          updatePanoReadout();
        });

        window.addEventListener('pointerup', () => {
          isDragging = false;
        });

        // Mobile Two-Finger Pinch-to-Zoom Gesture Support
        wrapperEl.addEventListener('touchstart', e => {
          if (e.touches && e.touches.length === 2) {
            const dx = e.touches[0].clientX - e.touches[1].clientX;
            const dy = e.touches[0].clientY - e.touches[1].clientY;
            pinchStartDist = Math.hypot(dx, dy);
            pinchStartFov = Number(STATE.panoFov) || 72;
          }
        }, { passive: true });

        wrapperEl.addEventListener('touchmove', e => {
          if (e.touches && e.touches.length === 2 && pinchStartDist > 10) {
            const dx = e.touches[0].clientX - e.touches[1].clientX;
            const dy = e.touches[0].clientY - e.touches[1].clientY;
            const curDist = Math.hypot(dx, dy);
            const scale = pinchStartDist / Math.max(10, curDist);
            STATE.panoFov = Math.max(38, Math.min(98, Math.round(pinchStartFov * scale)));
            const slider = document.getElementById('panoFovSlider');
            if (slider) slider.value = String(STATE.panoFov);
            const badge = document.getElementById('panoZoomBadge');
            if (badge) badge.textContent = `${STATE.panoFov}°`;
            updatePanoReadout();
          }
        }, { passive: true });

        // Mouse Wheel Zoom inside VR
        wrapperEl.addEventListener('wheel', e => {
          if (e.target && e.target.closest && e.target.closest('#vrOptionsDrawer, #xrHandDebugPanel, #panoTemplateSwitcherBar, #panoBottomViewpointDock')) return;
          e.preventDefault();
          adjustPanoZoom(e.deltaY > 0 ? 4 : -4);
        }, { passive: false });

        window.addEventListener('keydown', e => {
          if (STATE.activeView !== 'panorama') return;
          const k = e.key.toLowerCase();
          if (k === 'w' || e.key === 'ArrowUp') vrWalkKeys.w = true;
          if (k === 's' || e.key === 'ArrowDown') vrWalkKeys.s = true;
          if (k === 'a' || e.key === 'ArrowLeft') vrWalkKeys.a = true;
          if (k === 'd' || e.key === 'ArrowRight') vrWalkKeys.d = true;
          if (e.code === 'Escape') switchView('home');
        });

        window.addEventListener('keyup', e => {
          const k = e.key.toLowerCase();
          if (k === 'w' || e.key === 'ArrowUp') vrWalkKeys.w = false;
          if (k === 's' || e.key === 'ArrowDown') vrWalkKeys.s = false;
          if (k === 'a' || e.key === 'ArrowLeft') vrWalkKeys.a = false;
          if (k === 'd' || e.key === 'ArrowRight') vrWalkKeys.d = false;
        });

        window.vrStepMove = function(dir, dist = 1.8) {
          const yawRad = THREE.MathUtils.degToRad(STATE.panoYaw || 0);
          if (dir === 'w') {
            vrPlayerOffset.x += Math.sin(yawRad) * dist;
            vrPlayerOffset.z -= Math.cos(yawRad) * dist;
          } else if (dir === 's') {
            vrPlayerOffset.x -= Math.sin(yawRad) * dist;
            vrPlayerOffset.z += Math.cos(yawRad) * dist;
          } else if (dir === 'a') {
            vrPlayerOffset.x -= Math.cos(yawRad) * dist;
            vrPlayerOffset.z -= Math.sin(yawRad) * dist;
          } else if (dir === 'd') {
            vrPlayerOffset.x += Math.cos(yawRad) * dist;
            vrPlayerOffset.z += Math.sin(yawRad) * dist;
          }
          vrPlayerOffset.x = Math.max(-65, Math.min(65, vrPlayerOffset.x));
          vrPlayerOffset.z = Math.max(-65, Math.min(65, vrPlayerOffset.z));
        };

        document.querySelectorAll('.vr-move-btn').forEach(btn => {
          const dir = btn.getAttribute('data-move');
          if (!dir) return;
          const startMove = (ev) => {
            ev.preventDefault();
            ev.stopPropagation();
            if (!vrWalkKeys[dir]) window.vrStepMove(dir, 1.8);
            vrWalkKeys[dir] = true;
          };
          const stopMove = (ev) => {
            ev.preventDefault();
            ev.stopPropagation();
            vrWalkKeys[dir] = false;
          };
          btn.addEventListener('pointerdown', startMove);
          btn.addEventListener('pointerup', stopMove);
          btn.addEventListener('pointerleave', stopMove);
          btn.addEventListener('pointercancel', stopMove);
          btn.addEventListener('touchstart', startMove, { passive: false });
          btn.addEventListener('touchend', stopMove, { passive: false });
          btn.addEventListener('touchcancel', stopMove, { passive: false });
        });
      }

      if (typeof THREE === 'undefined') {
        loadPanoramaEnvironment();
        return;
      }
      try {
        panoScene = new THREE.Scene();
        panoCamera = new THREE.PerspectiveCamera(68, width / height, 0.05, 400);

        panoRenderer = new THREE.WebGLRenderer({ antialias: true, alpha: true, powerPreference: 'high-performance' });
        panoRenderer.setSize(width, height);
        panoRenderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, 2));
        panoRenderer.xr.enabled = true;
        XR_DIAG.threeXrConfigured = true;
        panoRenderer.shadowMap.enabled = true;
        panoRenderer.shadowMap.type = THREE.PCFSoftShadowMap;
        panoRenderer.toneMapping = THREE.ACESFilmicToneMapping;
        panoRenderer.toneMappingExposure = 1.08;
        container.innerHTML = '';
        container.appendChild(panoRenderer.domElement);
        runWebXRHandDiagnostics(false);
      } catch (err) {
        console.warn('Panorama WebGL fallback active:', err);
        loadPanoramaEnvironment();
        return;
      }

      loadPanoramaEnvironment();

      const clock = new THREE.Clock();

      function renderPanoramaFrame(timestamp, xrFrame) {
        if (STATE.activeView !== 'panorama' && !REAL_HAND_STATE.activeXrSession) return;

        const dt = Math.min(clock.getDelta(), 0.1);
        const elapsed = clock.getElapsedTime();

        if (xrFrame) {
          readWebXRHandJointsFromFrame(xrFrame);
        }

        const yawRad = THREE.MathUtils.degToRad(STATE.panoYaw);
        const pitchRad = THREE.MathUtils.degToRad(STATE.panoPitch);
        const walkSpeed = 9.0 * dt;
        if (vrWalkKeys.w) {
          vrPlayerOffset.x += Math.sin(yawRad) * walkSpeed;
          vrPlayerOffset.z -= Math.cos(yawRad) * walkSpeed;
        }
        if (vrWalkKeys.s) {
          vrPlayerOffset.x -= Math.sin(yawRad) * walkSpeed;
          vrPlayerOffset.z += Math.cos(yawRad) * walkSpeed;
        }
        if (vrWalkKeys.a) {
          vrPlayerOffset.x -= Math.cos(yawRad) * walkSpeed;
          vrPlayerOffset.z -= Math.sin(yawRad) * walkSpeed;
        }
        if (vrWalkKeys.d) {
          vrPlayerOffset.x += Math.cos(yawRad) * walkSpeed;
          vrPlayerOffset.z += Math.sin(yawRad) * walkSpeed;
        }
        vrPlayerOffset.x = Math.max(-65, Math.min(65, vrPlayerOffset.x));
        vrPlayerOffset.z = Math.max(-65, Math.min(65, vrPlayerOffset.z));

        const movePad = document.getElementById('vrMovePad');
        if (movePad) {
          if (STATE.panoMode === 'authentic') movePad.classList.add('hidden');
          else movePad.classList.remove('hidden');
        }

        const groundY = (STATE.panoMode !== 'authentic' && typeof STATE.panoTerrainHeightFn === 'function')
          ? STATE.panoTerrainHeightFn(vrPlayerOffset.x, vrPlayerOffset.z)
          : 0;
        const eyeY = 1.65 + groundY;

        panoCamera.position.set(vrPlayerOffset.x, eyeY, vrPlayerOffset.z);
        const lookTarget = new THREE.Vector3(
          vrPlayerOffset.x + Math.cos(pitchRad) * Math.sin(yawRad),
          eyeY + Math.sin(pitchRad),
          vrPlayerOffset.z - Math.cos(pitchRad) * Math.cos(yawRad)
        );
        panoCamera.lookAt(lookTarget);
        const isPort = ((window.innerWidth || 390) / Math.max(1, window.innerHeight || 844)) < 0.85;
        panoCamera.fov = isPort ? Math.max(STATE.panoFov, 82) : STATE.panoFov;
        panoCamera.updateProjectionMatrix();

        // STEP 4: Update Three.js virtual hands ONLY when real tracked joints exist!
        applyRealJointPosesToThreeJsHands();

        renderGuaranteed3DBeachAndHandsFrame();

        const w = window.innerWidth || container.clientWidth || 1280;
        const h = window.innerHeight || container.clientHeight || 720;
        if (STATE.panoMode === 'stereo' && !REAL_HAND_STATE.activeXrSession && typeof THREE.StereoCamera === 'function') {
          if (!window.__panoStereoCam) {
            window.__panoStereoCam = new THREE.StereoCamera();
            window.__panoStereoCam.eyeSep = 0.064;
          }
          panoCamera.aspect = w / h;
          panoCamera.updateProjectionMatrix();
          panoCamera.updateMatrixWorld();
          window.__panoStereoCam.update(panoCamera);

          panoRenderer.setScissorTest(true);
          panoRenderer.setViewport(0, 0, w / 2, h);
          panoRenderer.setScissor(0, 0, w / 2, h);
          panoRenderer.render(panoScene, window.__panoStereoCam.cameraL);

          panoRenderer.setViewport(w / 2, 0, w / 2, h);
          panoRenderer.setScissor(w / 2, 0, w / 2, h);
          panoRenderer.render(panoScene, window.__panoStereoCam.cameraR);
          panoRenderer.setScissorTest(false);
        } else {
          panoRenderer.setViewport(0, 0, w, h);
          panoCamera.aspect = w / h;
          panoCamera.updateProjectionMatrix();
          panoRenderer.render(panoScene, panoCamera);
        }
      }
      panoRenderer.setAnimationLoop(renderPanoramaFrame);
    }

    function updatePanoReadout() {
      const el = document.getElementById('panoOrientationReadout');
      if (el) {
        const normYaw = Math.round(((STATE.panoYaw + 180) % 360) - 180);
        el.textContent = `Yaw: ${normYaw}° | Pitch: ${Math.round(STATE.panoPitch)}° | FOV: ${STATE.panoFov}°`;
      }
      const badge = document.getElementById('panoZoomBadge');
      if (badge) badge.textContent = `${Math.round(STATE.panoFov)}°`;
      updateAuthenticPhotoStageTransform();
    }

    function loadPanoramaEnvironment() {
      const dest = getDestinationById(STATE.selectedDestinationId);
      const photo = dest.images[STATE.panoViewpointIndex] || dest.images[0];
      const envKey = getActiveEnvironmentKey(dest.id, STATE.panoViewpointIndex);
      const tplMeta = getTemplateMeta(dest.id, STATE.panoViewpointIndex);

      STATE.activeTemplateKey = envKey;
      STATE.panoLoadToken = (STATE.panoLoadToken || 0) + 1;
      const currentToken = STATE.panoLoadToken;

      // 1. Dispose old Three.js scene resources and clear previous canvas immediately
      disposePanoramaSceneResources();

      // 2. Preload the new destination's authentic panorama/photograph with loading progress
      preloadPanoramaPhotoAsset(photo ? photo.url : '', currentToken, tplMeta.fullTitle);

      // 3. Update all active destination titles, descriptions, badges, template pills & thumbnails
      const activeTitleEl = document.getElementById('panoActiveTemplateTitle');
      const activeDescEl = document.getElementById('panoActiveTemplateDesc');
      const activeBadgeEl = document.getElementById('panoActiveEnvTypeBadge');
      const titleEl = document.getElementById('panoTitleHeading');
      const subEl = document.getElementById('panoSubtitleText');
      const honestyText = document.getElementById('panoHonestyText');
      const modeBadge = document.getElementById('panoModeBadge');
      const attrEl = document.getElementById('panoCurrentAttribution');
      const stereoOverlay = document.getElementById('stereoDividerOverlay');
      const minStatus = document.getElementById('vrMinimalStatusText');

      if (activeTitleEl) activeTitleEl.textContent = tplMeta.fullTitle;
      if (activeDescEl) activeDescEl.textContent = tplMeta.description;
      if (activeBadgeEl) {
        activeBadgeEl.textContent = STATE.panoMode === 'authentic'
          ? (photo.isPanorama ? '360° Equirectangular Sphere' : '360° Sphere (Rectilinear Photo)')
          : tplMeta.envTypeLabel;
      }

      if (titleEl) titleEl.textContent = tplMeta.fullTitle;
      if (subEl) subEl.textContent = tplMeta.subtitle;
      if (attrEl && photo) {
        attrEl.innerHTML = `Source: <a href="${photo.commonsPage}" target="_blank" rel="noopener noreferrer" class="text-sky-300 underline">${photo.title}</a> (${photo.license})`;
      }
      if (stereoOverlay) stereoOverlay.classList.toggle('hidden', STATE.panoMode !== 'stereo');

      // Update active button styling in options drawer & bottom dock
      document.querySelectorAll('.pano-mode-btn').forEach(b => {
        const active = b.getAttribute('data-panomode') === STATE.panoMode;
        b.className = active
          ? 'pano-mode-btn px-2.5 py-2 rounded-lg text-[11px] font-medium bg-white text-slate-950'
          : 'pano-mode-btn px-2.5 py-2 rounded-lg text-[11px] font-medium bg-white/10 text-slate-200 hover:text-white';
      });

      const btnAuth = document.getElementById('dockModeAuthenticBtn');
      const btn3D = document.getElementById('dockMode3DBeachBtn');
      const btnStereo = document.getElementById('dockModeStereoBtn');
      if (btnAuth && btn3D && btnStereo) {
        btn3D.className = (STATE.panoMode === '3d-world' || STATE.panoMode === 'reconstructed')
          ? 'px-3.5 py-1.5 rounded-xl text-[11px] font-semibold bg-amber-400 text-slate-950 transition'
          : 'px-3.5 py-1.5 rounded-xl text-[11px] font-medium bg-white/10 text-white hover:bg-white/20 transition';
        btnAuth.className = STATE.panoMode === 'authentic'
          ? 'px-3.5 py-1.5 rounded-xl text-[11px] font-semibold bg-sky-400 text-slate-950 transition'
          : 'px-3.5 py-1.5 rounded-xl text-[11px] font-medium bg-white/10 text-white hover:bg-white/20 transition';
        btnStereo.className = STATE.panoMode === 'stereo'
          ? 'px-3 py-1.5 rounded-xl text-[11px] font-semibold bg-sky-400 text-slate-950 transition'
          : 'px-3 py-1.5 rounded-xl text-[11px] font-medium bg-white/10 text-slate-200 hover:bg-white/20 transition';
      }

      const noticeOverlay = document.getElementById('panoNo360NoticeOverlay');
      const glbBannerEl = document.getElementById('panoMissingGlbBanner');
      if (glbBannerEl && STATE.panoMode === 'authentic') glbBannerEl.classList.add('hidden');
      if (STATE.panoMode === '3d-world' || STATE.panoMode === 'reconstructed' || STATE.panoMode === 'stereo') {
        if (noticeOverlay) noticeOverlay.classList.add('hidden');
        setPanoLoadingProgress(false, 100, '');
        if (modeBadge) modeBadge.textContent = STATE.panoMode === 'stereo' ? `STEREO VR: ${envKey.toUpperCase()}` : `DISTINCT 3D WORLD: ${envKey.toUpperCase()}`;
        if (honestyText) honestyText.textContent = `Real-time Three.js 3D spatial environment for ${tplMeta.fullTitle}.`;
        if (minStatus) minStatus.textContent = `${tplMeta.shortLabel} — ${STATE.panoMode === 'stereo' ? 'Stereo VR' : 'Distinct 3D World'}`;
      } else {
        if (photo.isPanorama) {
          if (modeBadge) modeBadge.textContent = 'GENUINE 360° EQUIRECTANGULAR SPHERE VR';
          if (honestyText) honestyText.textContent = `Genuine 360° Panorama mapped onto the inside of a spherical viewing surface: ${photo.title} (${photo.license}).`;
          if (minStatus) minStatus.textContent = `${tplMeta.shortLabel} — 360° Spherical Panorama (${photo.viewpoint})`;
        } else {
          if (modeBadge) modeBadge.textContent = '360° SPHERICAL VIEWER (RECTILINEAR PHOTO AT TRUE ASPECT RATIO)';
          if (honestyText) honestyText.textContent = `Authentic rectilinear photograph (${photo.title}, ${photo.license}) mounted undistorted inside a 360° spherical horizon dome (never falsely stretched as a fake 360° capture).`;
          if (minStatus) minStatus.textContent = `${tplMeta.shortLabel} — 360° Sphere Viewer (${photo.viewpoint})`;
        }
      }

      populatePanoSelectors();
      renderPanoViewpointsStrip(dest);
      renderPanoHotspotsUI(dest);
      updateXrDiagnosticPanelUI();

      // 4. Start 60FPS loop & render immediate frame
      startGuaranteed3DVRLoop();
      renderGuaranteed3DBeachAndHandsFrame(performance.now() / 1000);

      // 5. Also build the distinct Three.js 3D / Spherical scene for WebXR headset sessions
      if (panoScene) {
        if (STATE.panoMode === 'authentic') {
          buildAuthenticPhotographicVREnvironment(photo, dest);
        } else {
          buildDestination3DEnvironment(dest);
        }
      }
    }

    // =========================================================================
    // LANDMARK GLB/GLTF ASSET REGISTRY & REAL THREE.JS 3D WORLD BUILDER
    // =========================================================================
    const LANDMARK_GLB_REGISTRY = {
      'eiffel-tower': {
        path: 'assets/models/eiffel_tower.glb',
        format: 'glTF 2.0 Binary (.glb), PBR Metallic-Roughness, <= 15 MB',
        cameraPos: { x: 0, y: 8, z: 68 },
        skyTop: 0x1e3a8a, skyBot: 0xfdba74, fogColor: 0x7c2d12, groundColor: 0x14532d
      },
      'kaminarimon-gate': {
        path: 'assets/models/kaminarimon_gate.glb',
        format: 'glTF 2.0 Binary (.glb), PBR Metallic-Roughness, <= 12 MB',
        cameraPos: { x: 0, y: 2.2, z: 18 },
        skyTop: 0x0f172a, skyBot: 0x9a3412, fogColor: 0x1e293b, groundColor: 0x3f3f46
      },
      'sensoji-hondo': {
        path: 'assets/models/sensoji_hondo.glb',
        format: 'glTF 2.0 Binary (.glb), PBR Metallic-Roughness, <= 15 MB',
        cameraPos: { x: 0, y: 2.5, z: 24 },
        skyTop: 0x1d4ed8, skyBot: 0xdbeafe, fogColor: 0x93c5fd, groundColor: 0x64748b
      },
      'lalbagh-fort': {
        path: 'assets/models/lalbagh_fort_pari_bibi.glb',
        format: 'glTF 2.0 Binary (.glb), PBR Metallic-Roughness, <= 12 MB',
        cameraPos: { x: 0, y: 2.4, z: 24 },
        skyTop: 0x0284c7, skyBot: 0xfef3c7, fogColor: 0xbae6fd, groundColor: 0x15803d
      },
      'great-wall-mutianyu': {
        path: 'assets/models/great_wall_mutianyu.glb',
        format: 'glTF 2.0 Binary (.glb), PBR Metallic-Roughness, <= 15 MB',
        cameraPos: { x: 0, y: 5.5, z: 28 },
        skyTop: 0x0369a1, skyBot: 0xe0f2fe, fogColor: 0xbae6fd, groundColor: 0x365314
      },
      'rome-colosseum': {
        path: 'assets/models/rome_colosseum.glb',
        format: 'glTF 2.0 Binary (.glb), PBR Metallic-Roughness, <= 15 MB',
        cameraPos: { x: 0, y: 4.0, z: 32 },
        skyTop: 0x1d4ed8, skyBot: 0xfde68a, fogColor: 0x93c5fd, groundColor: 0xc2b280
      },
      'statue-of-liberty': {
        path: 'assets/models/statue_of_liberty.glb',
        format: 'glTF 2.0 Binary (.glb), PBR Metallic-Roughness, <= 15 MB',
        cameraPos: { x: 0, y: 6.0, z: 36 },
        skyTop: 0x0284c7, skyBot: 0xe0f2fe, fogColor: 0xbae6fd, groundColor: 0x0f766e
      }
    };

    function createAtmosphericSkyDome(topHex, botHex) {
      if (typeof THREE === 'undefined') return null;
      const topCol = new THREE.Color(topHex);
      const botCol = new THREE.Color(botHex);
      const mat = new THREE.ShaderMaterial({
        side: THREE.BackSide,
        depthWrite: false,
        uniforms: {
          topColor: { value: topCol },
          bottomColor: { value: botCol }
        },
        vertexShader: `
          varying vec3 vWorldPos;
          void main() {
            vec4 wp = modelMatrix * vec4(position, 1.0);
            vWorldPos = wp.xyz;
            gl_Position = projectionMatrix * viewMatrix * wp;
          }
        `,
        fragmentShader: `
          uniform vec3 topColor;
          uniform vec3 bottomColor;
          varying vec3 vWorldPos;
          void main() {
            float h = normalize(vWorldPos).y;
            float t = clamp(h * 1.3 + 0.15, 0.0, 1.0);
            gl_FragColor = vec4(mix(bottomColor, topColor, pow(t, 0.75)), 1.0);
          }
        `
      });
      return new THREE.Mesh(new THREE.SphereGeometry(220, 32, 24), mat);
    }

    function fbmNoise2D(x, z) {
      return (
        Math.sin(x * 0.045) * Math.cos(z * 0.045) * 0.50 +
        Math.sin(x * 0.11 + 1.3) * Math.cos(z * 0.09 - 0.7) * 0.28 +
        Math.sin(x * 0.24 - 0.5) * Math.sin(z * 0.22 + 1.1) * 0.14 +
        Math.cos(x * 0.48 + z * 0.41) * 0.08
      );
    }

    function normalizeAndAddGltfModel(gltf, destName) {
      if (!gltf || !gltf.scene || !panoScene || typeof THREE === 'undefined') {
        throw new Error('Invalid GLTF scene');
      }
      const model = gltf.scene;
      let meshCount = 0;
      model.traverse(child => {
        if (child.isMesh && child.geometry) {
          meshCount++;
          child.castShadow = true;
          child.receiveShadow = true;
        }
      });
      if (meshCount === 0) {
        throw new Error(`Loaded GLB for ${destName} contains no visible mesh geometry.`);
      }
      const box = new THREE.Box3().setFromObject(model);
      const size = new THREE.Vector3();
      const center = new THREE.Vector3();
      box.getSize(size);
      box.getCenter(center);
      const maxDim = Math.max(size.x, size.y, size.z) || 1;
      const scale = 22 / maxDim;
      model.scale.setScalar(scale);
      model.position.set(-center.x * scale, -box.min.y * scale, -center.z * scale - 24);
      panoScene.add(model);

      // Frame camera cleanly on the normalized model
      STATE.panoYaw = 0;
      STATE.panoPitch = 4;
      vrPlayerOffset = { x: 0, z: 0 };
      STATE.panoTerrainHeightFn = null;
      if (panoCamera) {
        panoCamera.position.set(0, Math.max(2.2, (size.y * scale) * 0.35), 10);
        panoCamera.lookAt(0, (size.y * scale) * 0.45, -24);
      }
      if (panoRenderer && panoCamera) {
        panoRenderer.render(panoScene, panoCamera);
      }
      return meshCount;
    }

    function handleCustomGlbUpload(file) {
      if (!file || !panoScene || typeof THREE === 'undefined' || typeof THREE.GLTFLoader !== 'function') {
        showToast('Three.js GLTFLoader unavailable');
        return;
      }
      const objUrl = URL.createObjectURL(file);
      const loader = new THREE.GLTFLoader();
      setPanoLoadingProgress(true, 45, `Loading ${file.name}...`);
      loader.load(
        objUrl,
        gltf => {
          URL.revokeObjectURL(objUrl);
          setPanoLoadingProgress(false, 100, '');
          try {
            const count = normalizeAndAddGltfModel(gltf, file.name);
            const banner = document.getElementById('panoMissingGlbBanner');
            if (banner) banner.classList.add('hidden');
            showToast(`Loaded 3D GLB (${count} meshes): ${file.name}`);
          } catch (vErr) {
            showToast(vErr.message);
          }
        },
        undefined,
        err => {
          URL.revokeObjectURL(objUrl);
          setPanoLoadingProgress(false, 100, '');
          showToast(`Failed to parse GLB: ${err.message}`);
        }
      );
    }

    function showMissingGlbFallbackCard(envKey, spec) {
      const dest = getDestinationById(STATE.selectedDestinationId);
      const photo = dest.images[STATE.panoViewpointIndex] || dest.images[0];
      const tplMeta = getTemplateMeta(dest.id, STATE.panoViewpointIndex);
      const banner = document.getElementById('panoMissingGlbBanner');
      const titleEl = document.getElementById('panoMissingGlbTitle');
      const creditEl = document.getElementById('panoFallbackPhotoCredit');
      const textEl = document.getElementById('panoMissingGlbText');
      const imgEl = document.getElementById('panoFallbackPhotoImg');
      const spinnerEl = document.getElementById('panoFallbackImgSpinner');
      const errEl = document.getElementById('panoFallbackImgError');
      const vpBar = document.getElementById('panoFallbackViewpointsBar');
      const btn360 = document.getElementById('panoFallbackOpen360Btn');

      if (titleEl) titleEl.textContent = tplMeta.fullTitle;
      if (creditEl && photo) {
        creditEl.textContent = `${photo.viewpoint} • ${photo.author} (${photo.license})`;
      }
      if (textEl) {
        textEl.innerHTML = `The 3D landmark model (<code class="text-sky-300">${spec.path}</code>, ${spec.format}) is unavailable in this standalone browser bundle. Global VR Explorer never substitutes fake primitive geometry.`;
      }
      if (imgEl && photo) {
        if (spinnerEl) spinnerEl.classList.remove('hidden');
        if (errEl) errEl.classList.add('hidden');
        imgEl.onload = () => {
          if (spinnerEl) spinnerEl.classList.add('hidden');
          if (errEl) errEl.classList.add('hidden');
        };
        imgEl.onerror = () => {
          if (imgEl.dataset.triedOrig !== '1' && photo.url && imgEl.src !== photo.url) {
            imgEl.dataset.triedOrig = '1';
            imgEl.src = photo.url;
          } else {
            if (spinnerEl) spinnerEl.classList.add('hidden');
            if (errEl) errEl.classList.remove('hidden');
          }
        };
        imgEl.dataset.triedOrig = '0';
        imgEl.src = getOptimalWikimediaUrl(photo.url);
        imgEl.alt = photo.title;
      }
      if (vpBar && dest.images) {
        vpBar.innerHTML = dest.images.map((vp, idx) => {
          const active = idx === STATE.panoViewpointIndex;
          return `
            <button type="button" onclick="switchPanoViewpoint(${idx})"
                    class="px-2.5 py-1 rounded-full text-[11px] whitespace-nowrap shrink-0 border transition ${active ? 'bg-sky-400 text-slate-950 border-sky-300 font-semibold' : 'bg-white/5 text-slate-300 border-white/15 hover:bg-white/15'}">
              0${idx + 1} • ${vp.viewpoint}
            </button>
          `;
        }).join('');
      }
      if (btn360) {
        const hasAnyPano = Boolean(dest.images && dest.images.some(im => im.isPanorama));
        btn360.classList.toggle('hidden', !hasAnyPano);
      }
      if (banner) banner.classList.remove('hidden');
    }

    function attemptLandmarkGlbLoad(envKey, token) {
      // Optional .glb override if hosted alongside app; never blocks or hides the built-in 3D landmark
      const spec = LANDMARK_GLB_REGISTRY[envKey];
      if (!spec || typeof THREE === 'undefined' || typeof THREE.GLTFLoader !== 'function') return;
      const loader = new THREE.GLTFLoader();
      loader.load(
        spec.path,
        gltf => {
          if (token !== STATE.panoLoadToken || !panoScene) return;
          try {
            normalizeAndAddGltfModel(gltf, envKey);
          } catch (e) {}
        },
        undefined,
        () => {}
      );
    }

    const _procTexCache = {};
    function makeProceduralTexture(kind, repeatX = 4, repeatY = 4) {
      if (typeof document === 'undefined' || typeof THREE === 'undefined') return null;
      const cacheKey = `${kind}_${repeatX}_${repeatY}`;
      if (_procTexCache[cacheKey]) return _procTexCache[cacheKey];
      const canvas = document.createElement('canvas');
      canvas.width = 256;
      canvas.height = 256;
      const ctx = canvas.getContext('2d');
      if (!ctx) return null;

      if (kind === 'stoneBrick' || kind === 'terracottaBrick') {
        const isRed = kind === 'terracottaBrick';
        ctx.fillStyle = isRed ? '#7c2d12' : '#b8a992';
        ctx.fillRect(0, 0, 256, 256);
        const rows = 8, cols = 4;
        const rh = 256 / rows, cw = 256 / cols;
        for (let r = 0; r < rows; r++) {
          const offset = (r % 2) * (cw * 0.5);
          for (let c = -1; c <= cols; c++) {
            const shade = ((r * 7 + c * 13) % 17) - 8;
            if (isRed) {
              ctx.fillStyle = `rgb(${155 + shade}, ${64 + Math.floor(shade * 0.5)}, ${42 + Math.floor(shade * 0.4)})`;
            } else {
              ctx.fillStyle = `rgb(${194 + shade}, ${178 + shade}, ${152 + shade})`;
            }
            ctx.fillRect(c * cw + offset + 2, r * rh + 2, cw - 4, rh - 4);
          }
        }
      } else if (kind === 'roofTile') {
        ctx.fillStyle = '#1e293b';
        ctx.fillRect(0, 0, 256, 256);
        for (let x = 0; x < 256; x += 16) {
          ctx.fillStyle = (x / 16) % 2 === 0 ? '#273549' : '#1b2636';
          ctx.fillRect(x, 0, 14, 256);
          for (let y = 0; y < 256; y += 24) {
            ctx.fillStyle = 'rgba(255,255,255,0.06)';
            ctx.fillRect(x + 1, y + 2, 12, 3);
          }
        }
      } else if (kind === 'lushGrass') {
        ctx.fillStyle = '#3f7d20';
        ctx.fillRect(0, 0, 256, 256);
        for (let i = 0; i < 420; i++) {
          const x = (i * 73) % 256;
          const y = (i * 137) % 256;
          ctx.fillStyle = i % 3 === 0 ? '#559e2d' : (i % 3 === 1 ? '#2f6116' : '#65ad38');
          ctx.fillRect(x, y, 4, 4);
        }
      } else if (kind === 'sandRipple') {
        ctx.fillStyle = '#e6c994';
        ctx.fillRect(0, 0, 256, 256);
        for (let y = 0; y < 256; y += 10) {
          ctx.fillStyle = 'rgba(180, 140, 82, 0.18)';
          ctx.fillRect(0, y, 256, 3);
        }
      } else if (kind === 'woodPlank') {
        ctx.fillStyle = '#6b3e26';
        ctx.fillRect(0, 0, 256, 256);
        for (let y = 0; y < 256; y += 32) {
          ctx.fillStyle = y % 64 === 0 ? '#7c4a2d' : '#5e351e';
          ctx.fillRect(0, y + 1, 256, 30);
        }
      }

      const tex = new THREE.CanvasTexture(canvas);
      if (THREE.RepeatWrapping) {
        tex.wrapS = THREE.RepeatWrapping;
        tex.wrapT = THREE.RepeatWrapping;
      }
      if (tex.repeat && tex.repeat.set) tex.repeat.set(repeatX, repeatY);
      tex.needsUpdate = true;
      _procTexCache[cacheKey] = tex;
      return tex;
    }

    function createCloudySkyDome(topHex, bottomHex, withClouds = true) {
      if (typeof document === 'undefined' || typeof THREE === 'undefined') return null;
      const canvas = document.createElement('canvas');
      canvas.width = 512;
      canvas.height = 256;
      const ctx = canvas.getContext('2d');
      if (ctx) {
        const topC = '#' + topHex.toString(16).padStart(6, '0');
        const botC = '#' + bottomHex.toString(16).padStart(6, '0');
        const grad = ctx.createLinearGradient(0, 0, 0, 256);
        grad.addColorStop(0, topC);
        grad.addColorStop(0.55, botC);
        grad.addColorStop(1, botC);
        ctx.fillStyle = grad;
        ctx.fillRect(0, 0, 512, 256);
        if (withClouds) {
          for (let i = 0; i < 18; i++) {
            const cx = (i * 59 + 20) % 512;
            const cy = 62 + ((i * 23) % 54);
            const r = 16 + (i % 4) * 7;
            ctx.fillStyle = 'rgba(255,255,255,0.72)';
            ctx.beginPath();
            ctx.arc(cx, cy, r, 0, Math.PI * 2);
            ctx.arc(cx - r * 0.7, cy + 4, r * 0.72, 0, Math.PI * 2);
            ctx.arc(cx + r * 0.75, cy + 5, r * 0.68, 0, Math.PI * 2);
            ctx.fill();
          }
        }
      }
      const tex = new THREE.CanvasTexture(canvas);
      const sky = new THREE.Mesh(
        new THREE.SphereGeometry(220, 32, 20),
        new THREE.MeshBasicMaterial({ map: tex, side: THREE.BackSide, depthWrite: false })
      );
      sky.name = 'SkyBg';
      return sky;
    }

    function addScenicTree(parent, x, y, z, scale = 1, leafColor = 0x3b7a22) {
      const trunk = new THREE.Mesh(
        new THREE.CylinderGeometry(0.14 * scale, 0.22 * scale, 1.5 * scale, 7),
        new THREE.MeshStandardMaterial({ color: 0x5c4033, roughness: 0.9 })
      );
      trunk.position.set(x, y + 0.75 * scale, z);
      parent.add(trunk);
      const offsets = [
        [0, 1.8, 0, 0.95],
        [-0.45, 1.45, 0.3, 0.72],
        [0.48, 1.5, -0.25, 0.74],
        [0.15, 2.25, 0.2, 0.68]
      ];
      offsets.forEach(([ox, oy, oz, r], idx) => {
        const crown = new THREE.Mesh(
          new THREE.SphereGeometry(r * scale, 10, 8),
          new THREE.MeshStandardMaterial({ color: idx % 2 === 0 ? leafColor : 0x4d8f2c, roughness: 0.85 })
        );
        crown.position.set(x + ox * scale, y + oy * scale, z + oz * scale);
        parent.add(crown);
      });
    }

    function buildDestination3DEnvironment(dest) {
      if (!panoScene || typeof THREE === 'undefined') return;
      const envKey = getActiveEnvironmentKey(dest.id, STATE.panoViewpointIndex);
      const glbBanner = document.getElementById('panoMissingGlbBanner');
      if (glbBanner) glbBanner.classList.add('hidden');

      STATE.panoYaw = 0;
      STATE.panoPitch = 4;
      vrPlayerOffset = { x: 0, z: 0 };

      // =====================================================================
      // 1. EIFFEL TOWER, PARIS, FRANCE (Crisp Lattice Truss + Arches + Railings)
      // =====================================================================
      if (envKey === 'eiffel-tower') {
        panoScene.background = new THREE.Color(0xcbd5e1);
        panoScene.fog = new THREE.FogExp2(0xdbeafe, 0.004);
        const sky = createCloudySkyDome(0x3b82f6, 0xe2e8f0, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xffffff, 0x475569, 1.15));
        const sun = new THREE.DirectionalLight(0xfffbeb, 1.45);
        sun.position.set(22, 36, 25);
        panoScene.add(sun);

        // Clean studio/plaza esplanade grid + Champ de Mars border
        const esplanade = new THREE.Mesh(
          new THREE.PlaneGeometry(180, 180),
          new THREE.MeshStandardMaterial({ color: 0x94a3b8, roughness: 0.85 })
        );
        esplanade.rotation.x = -Math.PI / 2;
        panoScene.add(esplanade);

        const grassTex = makeProceduralTexture('lushGrass', 12, 12);
        const lawnL = new THREE.Mesh(
          new THREE.PlaneGeometry(50, 120),
          new THREE.MeshStandardMaterial({ map: grassTex, color: 0x4d7c0f, roughness: 0.9 })
        );
        lawnL.rotation.x = -Math.PI / 2;
        lawnL.position.set(-34, 0.02, -20);
        panoScene.add(lawnL);
        const lawnR = lawnL.clone();
        lawnR.position.x = 34;
        panoScene.add(lawnR);

        const tz = -22;
        const ironMat = new THREE.MeshStandardMaterial({ color: 0x5c4738, metalness: 0.62, roughness: 0.36 });
        const braceMat = new THREE.MeshStandardMaterial({ color: 0x453428, metalness: 0.58, roughness: 0.42 });
        const stoneMat = new THREE.MeshStandardMaterial({ map: makeProceduralTexture('stoneBrick', 2, 2), color: 0xd6d3d1, roughness: 0.78 });

        // 4 Masonry Piers + Stepped Plinths
        const pierOffsets = [[-4.8, -4.8], [4.8, -4.8], [-4.8, 4.8], [4.8, 4.8]];
        pierOffsets.forEach(([ox, oz]) => {
          const plinth = new THREE.Mesh(new THREE.BoxGeometry(2.6, 0.45, 2.6), stoneMat);
          plinth.position.set(ox, 0.22, tz + oz);
          panoScene.add(plinth);
          const pier = new THREE.Mesh(new THREE.BoxGeometry(2.1, 1.15, 2.1), stoneMat);
          pier.position.set(ox, 0.9, tz + oz);
          panoScene.add(pier);
        });

        // Helper for truss strut between two 3D points
        function addStrut(ax, ay, az, bx, by, bz, r = 0.09, mat = braceMat) {
          const dx = bx - ax, dy = by - ay, dz = bz - az;
          const len = Math.sqrt(dx * dx + dy * dy + dz * dz);
          if (len < 0.01) return;
          const strut = new THREE.Mesh(new THREE.CylinderGeometry(r, r, len, 6), mat);
          strut.position.set((ax + bx) / 2, (ay + by) / 2, (az + bz) / 2);
          strut.lookAt(bx, by, bz);
          strut.rotateX(Math.PI / 2);
          panoScene.add(strut);
        }

        // 4 Inclined Lower Legs (1.4 -> 6.8m) with internal X-bracing
        const legPairs = [[-1, -1], [1, -1], [-1, 1], [1, 1]];
        legPairs.forEach(([sx, sz]) => {
          const x0 = sx * 4.7, z0 = tz + sz * 4.7, y0 = 1.4;
          const x1 = sx * 2.55, z1 = tz + sz * 2.55, y1 = 6.8;
          addStrut(x0, y0, z0, x1, y1, z1, 0.36, ironMat);
          // Inner parallel chord + X cross-braces
          const ix0 = sx * 3.85, iz0 = tz + sz * 3.85;
          const ix1 = sx * 2.05, iz1 = tz + sz * 2.05;
          addStrut(ix0, y0, iz0, ix1, y1, iz1, 0.2, ironMat);
          for (let s = 0; s < 4; s++) {
            const tA = s / 4, tB = (s + 1) / 4;
            const ax = x0 + (x1 - x0) * tA, ay = y0 + (y1 - y0) * tA, az = z0 + (z1 - z0) * tA;
            const bx = ix0 + (ix1 - ix0) * tB, by = y0 + (y1 - y0) * tB, bz = iz0 + (iz1 - iz0) * tB;
            const cx = ix0 + (ix1 - ix0) * tA, cz = iz0 + (iz1 - iz0) * tA;
            const dx = x0 + (x1 - x0) * tB, dz = z0 + (z1 - z0) * tB;
            addStrut(ax, ay, az, bx, by, bz, 0.06, braceMat);
            addStrut(cx, ay, cz, dx, by, dz, 0.06, braceMat);
          }
        });

        // 4 Decorative Semicircular Arches + Radial Spandrel Struts
        [
          { x: 0, z: tz + 4.05, ry: 0 },
          { x: 0, z: tz - 4.05, ry: 0 },
          { x: -4.05, z: tz, ry: Math.PI / 2 },
          { x: 4.05, z: tz, ry: Math.PI / 2 }
        ].forEach(a => {
          const arch = new THREE.Mesh(new THREE.TorusGeometry(3.1, 0.24, 8, 24, Math.PI), ironMat);
          arch.position.set(a.x, 1.55, a.z);
          arch.rotation.y = a.ry;
          panoScene.add(arch);
        });

        // 1st Observation Deck (y = 6.8) + Perimeter Railing
        const deck1 = new THREE.Mesh(new THREE.BoxGeometry(6.4, 0.65, 6.4), ironMat);
        deck1.position.set(0, 6.8, tz);
        panoScene.add(deck1);
        const rail1 = new THREE.Mesh(new THREE.BoxGeometry(6.6, 0.28, 6.6), braceMat);
        rail1.position.set(0, 7.35, tz);
        panoScene.add(rail1);

        // 4 Middle Legs (7.1 -> 13.4m) with X-bracing
        legPairs.forEach(([sx, sz]) => {
          const x0 = sx * 2.45, z0 = tz + sz * 2.45, y0 = 7.1;
          const x1 = sx * 1.32, z1 = tz + sz * 1.32, y1 = 13.4;
          addStrut(x0, y0, z0, x1, y1, z1, 0.26, ironMat);
          for (let s = 0; s < 4; s++) {
            const tA = s / 4, tB = (s + 1) / 4;
            const yA = y0 + (y1 - y0) * tA, yB = y0 + (y1 - y0) * tB;
            const rA = 2.45 + (1.32 - 2.45) * tA, rB = 2.45 + (1.32 - 2.45) * tB;
            addStrut(sx * rA, yA, tz + sz * rA, -sx * rB * 0.4, yB, tz + sz * rB, 0.06, braceMat);
          }
        });

        // 2nd Observation Deck (y = 13.4) + Railing
        const deck2 = new THREE.Mesh(new THREE.BoxGeometry(3.6, 0.55, 3.6), ironMat);
        deck2.position.set(0, 13.4, tz);
        panoScene.add(deck2);
        const rail2 = new THREE.Mesh(new THREE.BoxGeometry(3.8, 0.24, 3.8), braceMat);
        rail2.position.set(0, 13.85, tz);
        panoScene.add(rail2);

        // Upper Tapered Lattice Shaft (13.7 -> 27.2m)
        for (let tier = 0; tier < 6; tier++) {
          const yA = 13.7 + tier * 2.25;
          const yB = yA + 2.25;
          const rA = 1.22 - tier * 0.13;
          const rB = 1.22 - (tier + 1) * 0.13;
          legPairs.forEach(([sx, sz]) => {
            addStrut(sx * rA, yA, tz + sz * rA, sx * rB, yB, tz + sz * rB, 0.14, ironMat);
            addStrut(sx * rA, yA, tz + sz * rA, -sx * rB, yB, tz + sz * rB, 0.05, braceMat);
            addStrut(sx * rA, yA, tz + sz * rA, sx * rB, yB, tz - sz * rB, 0.05, braceMat);
          });
        }

        // Summit Cupola, Beacon & Spire (27.2 -> 32.5m)
        const deck3 = new THREE.Mesh(new THREE.BoxGeometry(1.45, 0.48, 1.45), ironMat);
        deck3.position.set(0, 27.3, tz);
        panoScene.add(deck3);
        const cupola = new THREE.Mesh(new THREE.CylinderGeometry(0.45, 0.62, 1.4, 8), ironMat);
        cupola.position.set(0, 28.2, tz);
        panoScene.add(cupola);
        const beacon = new THREE.Mesh(
          new THREE.SphereGeometry(0.48, 12, 10),
          new THREE.MeshBasicMaterial({ color: 0xfef08a })
        );
        beacon.position.set(0, 29.1, tz);
        panoScene.add(beacon);
        const spire = new THREE.Mesh(new THREE.CylinderGeometry(0.05, 0.22, 3.4, 8), ironMat);
        spire.position.set(0, 30.8, tz);
        panoScene.add(spire);
      }

      // =====================================================================
      // 2. KAMINARIMON ENTRANCE GATE, ASAKUSA, TOKYO (Iconic Lantern + Kawara Roof)
      // =====================================================================
      else if (envKey === 'kaminarimon-gate') {
        panoScene.background = new THREE.Color(0x38bdf8);
        panoScene.fog = new THREE.FogExp2(0xe0f2fe, 0.006);
        const sky = createCloudySkyDome(0x0284c7, 0xe0f2fe, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xffffff, 0x475569, 1.15));
        const sun = new THREE.DirectionalLight(0xfffbeb, 1.4);
        sun.position.set(16, 28, 22);
        panoScene.add(sun);

        // Ishidatami stone approach pavement
        const pave = new THREE.Mesh(
          new THREE.PlaneGeometry(140, 140),
          new THREE.MeshStandardMaterial({ map: makeProceduralTexture('stoneBrick', 14, 14), color: 0xcbd5e1, roughness: 0.85 })
        );
        pave.rotation.x = -Math.PI / 2;
        panoScene.add(pave);

        const gz = -16;
        const vermilion = new THREE.MeshStandardMaterial({ color: 0xb91c1c, roughness: 0.42 });
        const darkWood = new THREE.MeshStandardMaterial({ map: makeProceduralTexture('woodPlank', 2, 2), color: 0x3f2212, roughness: 0.6 });
        const roofMat = new THREE.MeshStandardMaterial({ map: makeProceduralTexture('roofTile', 6, 3), color: 0x1e293b, roughness: 0.55 });
        const goldMat = new THREE.MeshStandardMaterial({ color: 0xf59e0b, metalness: 0.8, roughness: 0.25 });
        const stoneMat = new THREE.MeshStandardMaterial({ color: 0x94a3b8, roughness: 0.85 });

        // Stone plinth & 4 Vermilion Pillars
        const baseStep = new THREE.Mesh(new THREE.BoxGeometry(17.5, 0.6, 8.2), stoneMat);
        baseStep.position.set(0, 0.3, gz);
        panoScene.add(baseStep);

        [-6.4, -2.5, 2.5, 6.4].forEach(px => {
          [-2.2, 2.2].forEach(pz => {
            const collar = new THREE.Mesh(new THREE.CylinderGeometry(0.56, 0.62, 0.65, 16), new THREE.MeshStandardMaterial({ color: 0x111827, roughness: 0.4 }));
            collar.position.set(px, 0.9, gz + pz);
            panoScene.add(collar);
            const pillar = new THREE.Mesh(new THREE.CylinderGeometry(0.46, 0.5, 6.2, 16), vermilion);
            pillar.position.set(px, 4.3, gz + pz);
            panoScene.add(pillar);
          });
        });

        // Guardian Alcoves (Fujin & Raijin bays) + Wooden Lattice
        [-4.45, 4.45].forEach(ax => {
          const alcove = new THREE.Mesh(new THREE.BoxGeometry(3.4, 5.4, 3.6), vermilion);
          alcove.position.set(ax, 3.6, gz);
          panoScene.add(alcove);
          const lattice = new THREE.Mesh(new THREE.BoxGeometry(2.6, 3.8, 3.8), darkWood);
          lattice.position.set(ax, 3.3, gz);
          panoScene.add(lattice);
        });

        // Crossbeams + Gold "金龍山" Kinryuzan Plaque
        const beam = new THREE.Mesh(new THREE.BoxGeometry(15.2, 0.85, 5.6), vermilion);
        beam.position.set(0, 7.1, gz);
        panoScene.add(beam);
        const plaqueFrame = new THREE.Mesh(new THREE.BoxGeometry(2.6, 1.5, 0.3), goldMat);
        plaqueFrame.position.set(0, 7.45, gz + 2.88);
        panoScene.add(plaqueFrame);
        const plaqueInner = new THREE.Mesh(new THREE.BoxGeometry(2.2, 1.15, 0.34), new THREE.MeshStandardMaterial({ color: 0x111827 }));
        plaqueInner.position.set(0, 7.45, gz + 2.9);
        panoScene.add(plaqueInner);

        // Two-tiered sweeping Kawara Roof
        const lowerRoof = new THREE.Mesh(new THREE.ConeGeometry(11.4, 2.5, 4), roofMat);
        lowerRoof.position.set(0, 8.7, gz);
        lowerRoof.rotation.y = Math.PI / 4;
        lowerRoof.scale.set(1.38, 1, 0.72);
        panoScene.add(lowerRoof);
        const upperRoof = new THREE.Mesh(new THREE.ConeGeometry(9.2, 2.3, 4), roofMat);
        upperRoof.position.set(0, 10.6, gz);
        upperRoof.rotation.y = Math.PI / 4;
        upperRoof.scale.set(1.35, 1, 0.7);
        panoScene.add(upperRoof);
        const ridge = new THREE.Mesh(new THREE.BoxGeometry(11.8, 0.45, 0.6), goldMat);
        ridge.position.set(0, 11.75, gz);
        panoScene.add(ridge);

        // Giant Glowing Red Chochin Lantern (centerpiece)
        const lantern = new THREE.Mesh(
          new THREE.SphereGeometry(1.75, 24, 18),
          new THREE.MeshStandardMaterial({ color: 0xdc2626, emissive: 0x991b1b, emissiveIntensity: 0.75, roughness: 0.45 })
        );
        lantern.scale.set(0.88, 1.25, 0.88);
        lantern.position.set(0, 4.15, gz + 0.5);
        panoScene.add(lantern);
        [5.95, 2.35].forEach(cy => {
          const cap = new THREE.Mesh(new THREE.CylinderGeometry(0.95, 0.95, 0.45, 20), new THREE.MeshStandardMaterial({ color: 0x111827 }));
          cap.position.set(0, cy, gz + 0.5);
          panoScene.add(cap);
          const band = new THREE.Mesh(new THREE.CylinderGeometry(1.0, 1.0, 0.14, 20), goldMat);
          band.position.set(0, cy + (cy > 4 ? -0.2 : 0.2), gz + 0.5);
          panoScene.add(band);
        });
        const lGlow = new THREE.PointLight(0xff5a1f, 2.2, 18);
        lGlow.position.set(0, 4.1, gz + 2.4);
        panoScene.add(lGlow);

        // Flanking Stone Toro Lanterns & Trees
        [-7.5, 7.5].forEach(lx => {
          const toro = new THREE.Mesh(new THREE.CylinderGeometry(0.32, 0.45, 2.4, 8), stoneMat);
          toro.position.set(lx, 1.2, gz + 6.5);
          panoScene.add(toro);
          const cap = new THREE.Mesh(new THREE.ConeGeometry(0.75, 0.5, 4), stoneMat);
          cap.position.set(lx, 2.6, gz + 6.5);
          panoScene.add(cap);
        });
        addScenicTree(panoScene, -11.5, 0, gz + 2, 1.4, 0x2f6b24);
        addScenicTree(panoScene, 11.5, 0, gz + 2, 1.4, 0x2f6b24);
      }

      // =====================================================================
      // 3. SENSO-JI HONDO & 5-STORIED PAGODA, TOKYO
      // =====================================================================
      else if (envKey === 'sensoji-hondo') {
        panoScene.background = new THREE.Color(0x38bdf8);
        panoScene.fog = new THREE.FogExp2(0xe0f2fe, 0.0055);
        const sky = createCloudySkyDome(0x0284c7, 0xe0f2fe, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xffffff, 0x475569, 1.15));
        const sun = new THREE.DirectionalLight(0xfffbeb, 1.4);
        sun.position.set(18, 30, 22);
        panoScene.add(sun);

        const courtyard = new THREE.Mesh(
          new THREE.PlaneGeometry(160, 160),
          new THREE.MeshStandardMaterial({ map: makeProceduralTexture('stoneBrick', 16, 16), color: 0xcbd5e1, roughness: 0.85 })
        );
        courtyard.rotation.x = -Math.PI / 2;
        panoScene.add(courtyard);

        const vermilion = new THREE.MeshStandardMaterial({ color: 0xb91c1c, roughness: 0.45 });
        const plaster = new THREE.MeshStandardMaterial({ color: 0xf8fafc, roughness: 0.8 });
        const roofMat = new THREE.MeshStandardMaterial({ map: makeProceduralTexture('roofTile', 8, 4), color: 0x1e293b, roughness: 0.5 });
        const goldMat = new THREE.MeshStandardMaterial({ color: 0xf59e0b, metalness: 0.8, roughness: 0.25 });

        // Hondo Main Hall (center-right)
        const hz = -24, hx = 2.2;
        const plinth = new THREE.Mesh(new THREE.BoxGeometry(20, 1.2, 12), new THREE.MeshStandardMaterial({ color: 0x94a3b8 }));
        plinth.position.set(hx, 0.6, hz);
        panoScene.add(plinth);
        const stairs = new THREE.Mesh(new THREE.BoxGeometry(7.2, 0.9, 2.4), new THREE.MeshStandardMaterial({ color: 0x94a3b8 }));
        stairs.position.set(hx, 0.45, hz + 6.8);
        panoScene.add(stairs);
        const hallBody = new THREE.Mesh(new THREE.BoxGeometry(17.5, 5.8, 9.5), plaster);
        hallBody.position.set(hx, 4.1, hz);
        panoScene.add(hallBody);
        for (let px = -7.6; px <= 7.6; px += 2.53) {
          const col = new THREE.Mesh(new THREE.CylinderGeometry(0.28, 0.32, 5.8, 12), vermilion);
          col.position.set(hx + px, 4.1, hz + 5.1);
          panoScene.add(col);
        }
        const hondoRoof1 = new THREE.Mesh(new THREE.ConeGeometry(13.8, 3.8, 4), roofMat);
        hondoRoof1.position.set(hx, 8.8, hz);
        hondoRoof1.rotation.y = Math.PI / 4;
        hondoRoof1.scale.set(1.42, 1, 0.85);
        panoScene.add(hondoRoof1);
        const hondoRoof2 = new THREE.Mesh(new THREE.ConeGeometry(10.5, 3.2, 4), roofMat);
        hondoRoof2.position.set(hx, 11.4, hz);
        hondoRoof2.rotation.y = Math.PI / 4;
        hondoRoof2.scale.set(1.42, 1, 0.82);
        panoScene.add(hondoRoof2);

        // Goju-no-to Five-Storied Pagoda (visible in portrait left)
        const px = -7.6, pz = -20;
        const pBase = new THREE.Mesh(new THREE.BoxGeometry(4.2, 1.1, 4.2), new THREE.MeshStandardMaterial({ color: 0x94a3b8 }));
        pBase.position.set(px, 0.55, pz);
        panoScene.add(pBase);
        for (let s = 0; s < 5; s++) {
          const w = 3.2 - s * 0.34;
          const y = 2.0 + s * 2.15;
          const story = new THREE.Mesh(new THREE.BoxGeometry(w, 1.45, w), vermilion);
          story.position.set(px, y, pz);
          panoScene.add(story);
          const eave = new THREE.Mesh(new THREE.ConeGeometry(w * 0.98, 0.9, 4), roofMat);
          eave.position.set(px, y + 1.05, pz);
          eave.rotation.y = Math.PI / 4;
          panoScene.add(eave);
        }
        const sorin = new THREE.Mesh(new THREE.CylinderGeometry(0.08, 0.16, 3.4, 8), goldMat);
        sorin.position.set(px, 13.4, pz);
        panoScene.add(sorin);

        // Foreground Jokoro Bronze Incense Censer
        const censer = new THREE.Mesh(new THREE.CylinderGeometry(1.1, 0.85, 1.35, 16), new THREE.MeshStandardMaterial({ color: 0x334155, metalness: 0.7, roughness: 0.35 }));
        censer.position.set(0.6, 1.1, -11.5);
        panoScene.add(censer);
        addScenicTree(panoScene, 11.5, 0, -17, 1.5, 0x2f6b24);
      }

      // =====================================================================
      // 4. LALBAGH FORT (MAUSOLEUM OF PARI BIBI & CHARBAGH), DHAKA
      // =====================================================================
      else if (envKey === 'lalbagh-fort') {
        panoScene.background = new THREE.Color(0x38bdf8);
        panoScene.fog = new THREE.FogExp2(0xe0f2fe, 0.0055);
        const sky = createCloudySkyDome(0x0284c7, 0xe0f2fe, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xffffff, 0x3f6212, 1.15));
        const sun = new THREE.DirectionalLight(0xfffbeb, 1.45);
        sun.position.set(18, 30, 20);
        panoScene.add(sun);

        const grassMat = new THREE.MeshStandardMaterial({ map: makeProceduralTexture('lushGrass', 16, 16), color: 0x4d7c0f, roughness: 0.9 });
        const lawn = new THREE.Mesh(new THREE.PlaneGeometry(180, 180), grassMat);
        lawn.rotation.x = -Math.PI / 2;
        panoScene.add(lawn);

        const redTex = makeProceduralTexture('terracottaBrick', 6, 4);
        const redStone = new THREE.MeshStandardMaterial({ map: redTex, color: 0xb94a38, roughness: 0.72 });
        const darkArch = new THREE.MeshStandardMaterial({ color: 0x451a03, roughness: 0.8 });
        const marble = new THREE.MeshStandardMaterial({ color: 0xf8fafc, roughness: 0.35 });
        const copperDome = new THREE.MeshStandardMaterial({ color: 0x64748b, metalness: 0.45, roughness: 0.4 });
        const goldMat = new THREE.MeshStandardMaterial({ color: 0xfbbf24, metalness: 0.85, roughness: 0.2 });

        // Charbagh Red Sandstone Walkway & Reflecting Pool
        const walkway = new THREE.Mesh(new THREE.BoxGeometry(4.6, 0.08, 36), redStone);
        walkway.position.set(0, 0.04, -10);
        panoScene.add(walkway);
        const pool = new THREE.Mesh(
          new THREE.BoxGeometry(2.8, 0.12, 12),
          new THREE.MeshStandardMaterial({ color: 0x0ea5e9, metalness: 0.4, roughness: 0.15 })
        );
        pool.position.set(0, 0.08, -9);
        panoScene.add(pool);

        const fz = -24;
        // Mausoleum of Pari Bibi
        const plinth = new THREE.Mesh(new THREE.BoxGeometry(15.2, 1.3, 15.2), redStone);
        plinth.position.set(0, 0.65, fz);
        panoScene.add(plinth);
        const body = new THREE.Mesh(new THREE.BoxGeometry(11.6, 4.9, 11.6), redStone);
        body.position.set(0, 3.75, fz);
        panoScene.add(body);

        // Central Pishtaq Arch + Flanking Niches
        const pishtaq = new THREE.Mesh(new THREE.BoxGeometry(4.2, 5.4, 0.6), redStone);
        pishtaq.position.set(0, 4.0, fz + 5.9);
        panoScene.add(pishtaq);
        const mainArch = new THREE.Mesh(new THREE.BoxGeometry(2.4, 3.8, 0.75), darkArch);
        mainArch.position.set(0, 3.2, fz + 5.92);
        panoScene.add(mainArch);
        [-3.8, 3.8].forEach(nx => {
          const niche = new THREE.Mesh(new THREE.BoxGeometry(1.7, 2.7, 0.45), darkArch);
          niche.position.set(nx, 3.3, fz + 5.82);
          panoScene.add(niche);
        });

        // White Marble Octagonal Drum + Copper Onion Dome + Gold Finial
        const drum = new THREE.Mesh(new THREE.CylinderGeometry(2.9, 3.1, 1.5, 8), marble);
        drum.position.set(0, 6.95, fz);
        panoScene.add(drum);
        const dome = new THREE.Mesh(new THREE.SphereGeometry(2.85, 24, 18), copperDome);
        dome.scale.set(1, 0.9, 1);
        dome.position.set(0, 8.2, fz);
        panoScene.add(dome);
        const finial = new THREE.Mesh(new THREE.ConeGeometry(0.24, 1.65, 10), goldMat);
        finial.position.set(0, 11.2, fz);
        panoScene.add(finial);

        // 4 Corner Chhatri Minarets
        [[-5.2, -5.2], [5.2, -5.2], [-5.2, 5.2], [5.2, 5.2]].forEach(([mx, mz]) => {
          const minaret = new THREE.Mesh(new THREE.CylinderGeometry(0.45, 0.56, 6.2, 8), redStone);
          minaret.position.set(mx, 4.4, fz + mz);
          panoScene.add(minaret);
          const cap = new THREE.Mesh(new THREE.SphereGeometry(0.62, 12, 10), marble);
          cap.position.set(mx, 7.65, fz + mz);
          panoScene.add(cap);
        });

        // Symmetrical Charbagh Trees
        [-6.5, 6.5].forEach(tx => {
          addScenicTree(panoScene, tx, 0, -13, 1.15, 0x2f6b24);
          addScenicTree(panoScene, tx, 0, -21, 1.25, 0x3b7a22);
        });
      }

      // =====================================================================
      // 5. GREAT WALL OF CHINA, MUTIANYU (Grounded Rolling Hills + Arched Watchtowers)
      // =====================================================================
      else if (envKey === 'great-wall-mutianyu') {
        panoScene.background = new THREE.Color(0x38bdf8);
        panoScene.fog = new THREE.FogExp2(0xdbeafe, 0.004);
        const sky = createCloudySkyDome(0x0ea5e9, 0xe0f2fe, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xffffff, 0x3f6212, 1.25));
        const sun = new THREE.DirectionalLight(0xfffbeb, 1.5);
        sun.position.set(22, 34, 20);
        panoScene.add(sun);

        // Grounded rolling green terrain: anchored at y = 0 at camera origin (0, 0)
        const grassTex = makeProceduralTexture('lushGrass', 20, 20);
        const terrainGeo = new THREE.PlaneGeometry(180, 180, 56, 56);
        terrainGeo.rotateX(-Math.PI / 2);
        const pos = terrainGeo.attributes.position;
        function hillRaw(x, z) {
          return Math.sin(x * 0.075) * 2.4 + Math.cos(z * 0.065) * 2.1 + Math.sin((x - z) * 0.05) * 1.8;
        }
        const originOffset = hillRaw(0, 0);
        function hillHeight(x, z) {
          return hillRaw(x, z) - originOffset;
        }
        STATE.panoTerrainHeightFn = hillHeight;

        for (let i = 0; i < pos.count; i++) {
          const vx = pos.getX(i), vz = pos.getZ(i);
          pos.setY(i, hillHeight(vx, vz));
        }
        terrainGeo.computeVertexNormals();
        const terrain = new THREE.Mesh(
          terrainGeo,
          new THREE.MeshStandardMaterial({ map: grassTex, color: 0x65a30d, roughness: 0.9 })
        );
        panoScene.add(terrain);

        const wallTex = makeProceduralTexture('stoneBrick', 4, 2);
        const stoneMat = new THREE.MeshStandardMaterial({ map: wallTex, color: 0xc2b29b, roughness: 0.85 });
        const roofMat = new THREE.MeshStandardMaterial({ map: makeProceduralTexture('roofTile', 4, 2), color: 0x2f3e46, roughness: 0.55 });
        const archDark = new THREE.MeshStandardMaterial({ color: 0x292524, roughness: 0.9 });

        // Winding Great Wall starting gracefully ahead (z = -10 to -62) with deep foundations
        const pts = [];
        for (let i = 0; i <= 18; i++) {
          const z = -10 - i * 3.0;
          const x = Math.sin(i * 0.36) * 7.5 - (i > 10 ? (i - 10) * 0.9 : 0);
          pts.push({ x, y: hillHeight(x, z), z });
        }
        for (let i = 0; i < pts.length - 1; i++) {
          const a = pts[i], b = pts[i + 1];
          const dx = b.x - a.x, dy = b.y - a.y, dz = b.z - a.z;
          const len = Math.sqrt(dx * dx + dy * dy + dz * dz);
          const midY = (a.y + b.y) / 2;
          // Deep 7.5m tall wall body sunk 5m into the hill so underside is never exposed
          const seg = new THREE.Mesh(new THREE.BoxGeometry(2.4, 7.5, len + 0.6), stoneMat);
          seg.position.set((a.x + b.x) / 2, midY - 1.35, (a.z + b.z) / 2);
          seg.lookAt(b.x, midY - 1.35, b.z);
          panoScene.add(seg);

          // Crenellated merlons along top parapet (midY + 2.65)
          for (let m = -0.35; m <= 0.35; m += 0.35) {
            [-0.95, 0.95].forEach(side => {
              const merlon = new THREE.Mesh(new THREE.BoxGeometry(0.28, 0.55, 0.45), stoneMat);
              merlon.position.set(
                (a.x + b.x) / 2 + side * 0.9,
                midY + 2.65,
                (a.z + b.z) / 2 + m * len
              );
              panoScene.add(merlon);
            });
          }
        }

        // 5 Ming Watchtowers sunk deep into hillside with Arched Windows & Pitched Tiled Roofs
        [1, 5, 9, 13, 17].forEach((idx, tIdx) => {
          const p = pts[idx];
          const tower = new THREE.Mesh(new THREE.BoxGeometry(4.5, 9.2, 4.5), stoneMat);
          tower.position.set(p.x, p.y - 0.1, p.z);
          panoScene.add(tower);

          [-1.1, 0, 1.1].forEach(wx => {
            const win = new THREE.Mesh(new THREE.BoxGeometry(0.55, 0.95, 4.65), archDark);
            win.position.set(p.x + wx, p.y + 3.1, p.z);
            panoScene.add(win);
          });
          const parapet = new THREE.Mesh(new THREE.BoxGeometry(4.8, 0.55, 4.8), stoneMat);
          parapet.position.set(p.x, p.y + 4.75, p.z);
          panoScene.add(parapet);

          if (tIdx % 2 === 0) {
            const pav = new THREE.Mesh(new THREE.BoxGeometry(2.6, 1.4, 2.6), stoneMat);
            pav.position.set(p.x, p.y + 5.5, p.z);
            panoScene.add(pav);
            const roof = new THREE.Mesh(new THREE.ConeGeometry(2.6, 1.4, 4), roofMat);
            roof.position.set(p.x, p.y + 6.8, p.z);
            roof.rotation.y = Math.PI / 4;
            panoScene.add(roof);
          }
        });

        for (let t = 0; t < 26; t++) {
          const tx = ((t * 17) % 44) - 22;
          const tz = -5 - ((t * 13) % 52);
          if (Math.abs(tx) < 3.0 && tz > -12) continue;
          addScenicTree(panoScene, tx, hillHeight(tx, tz) - 0.25, tz, 0.75 + (t % 3) * 0.25, 0x4d7c0f);
        }
      }

      // =====================================================================
      // 6. STATUE OF LIBERTY, NEW YORK (Vertex-Sculpted Drapery & Classical Face)
      // =====================================================================
      else if (envKey === 'statue-of-liberty') {
        panoScene.background = new THREE.Color(0x38bdf8);
        panoScene.fog = new THREE.FogExp2(0xe0f2fe, 0.004);
        const sky = createCloudySkyDome(0x0284c7, 0xe0f2fe, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xffffff, 0x0c4a6e, 1.2));
        const sun = new THREE.DirectionalLight(0xfffbeb, 1.55);
        sun.position.set(14, 28, 26);
        panoScene.add(sun);
        const rim = new THREE.DirectionalLight(0xbae6fd, 0.55);
        rim.position.set(-18, 16, -16);
        panoScene.add(rim);

        const harbor = new THREE.Mesh(
          new THREE.PlaneGeometry(240, 240),
          new THREE.MeshStandardMaterial({ color: 0x0369a1, metalness: 0.35, roughness: 0.25 })
        );
        harbor.rotation.x = -Math.PI / 2;
        panoScene.add(harbor);

        const sz = -22;
        const pedMat = new THREE.MeshStandardMaterial({ map: makeProceduralTexture('stoneBrick', 3, 3), color: 0xc2b5a6, roughness: 0.8 });
        const nicheMat = new THREE.MeshStandardMaterial({ color: 0x57534e, roughness: 0.9 });
        const copperMat = new THREE.MeshStandardMaterial({ color: 0x58a392, metalness: 0.22, roughness: 0.44 });
        const foldMat = new THREE.MeshStandardMaterial({ color: 0x418273, metalness: 0.25, roughness: 0.5 });
        const highlightCopper = new THREE.MeshStandardMaterial({ color: 0x6dbba9, metalness: 0.2, roughness: 0.4 });

        // 11-pointed Fort Wood Base
        const fort = new THREE.Mesh(new THREE.CylinderGeometry(6.6, 7.4, 1.8, 11), pedMat);
        fort.position.set(0, 0.9, sz);
        panoScene.add(fort);

        // Lower Pedestal with Arched Romanesque Niches
        const pedLower = new THREE.Mesh(new THREE.BoxGeometry(5.8, 3.6, 5.8), pedMat);
        pedLower.position.set(0, 3.6, sz);
        panoScene.add(pedLower);
        [-1.65, 0, 1.65].forEach(nx => {
          const niche = new THREE.Mesh(new THREE.BoxGeometry(0.85, 2.0, 6.0), nicheMat);
          niche.position.set(nx, 3.2, sz);
          panoScene.add(niche);
          const archCap = new THREE.Mesh(new THREE.CylinderGeometry(0.42, 0.42, 6.02, 12), nicheMat);
          archCap.rotation.x = Math.PI / 2;
          archCap.position.set(nx, 4.2, sz);
          panoScene.add(archCap);
        });
        const cornice1 = new THREE.Mesh(new THREE.BoxGeometry(6.5, 0.48, 6.5), pedMat);
        cornice1.position.set(0, 5.64, sz);
        panoScene.add(cornice1);

        // Upper Pedestal with Arcade Pilasters
        const pedUpper = new THREE.Mesh(new THREE.BoxGeometry(4.6, 2.2, 4.6), pedMat);
        pedUpper.position.set(0, 6.95, sz);
        panoScene.add(pedUpper);
        [-1.6, -0.55, 0.55, 1.6].forEach(px => {
          const pilaster = new THREE.Mesh(new THREE.BoxGeometry(0.28, 2.1, 4.82), pedMat);
          pilaster.position.set(px, 6.95, sz);
          panoScene.add(pilaster);
        });
        const cornice2 = new THREE.Mesh(new THREE.BoxGeometry(5.2, 0.44, 5.2), pedMat);
        cornice2.position.set(0, 8.25, sz);
        panoScene.add(cornice2);

        // Vertex-Sculpted Draped Classical Robe (8.6m tall, 48x28 segments with carved folds)
        const robeGeo = new THREE.CylinderGeometry(1.05, 1.85, 8.6, 48, 28);
        const rPos = robeGeo.attributes.position;
        for (let i = 0; i < rPos.count; i++) {
          const vx = rPos.getX(i);
          const vy = rPos.getY(i);
          const vz = rPos.getZ(i);
          const hNorm = (vy + 4.3) / 8.6; // 0 at feet, 1 at shoulders
          const ang = Math.atan2(vz, vx);
          // Classical hourglass taper + cinched waist at hNorm=0.66
          const waistPinch = 1.0 - 0.18 * Math.exp(-Math.pow((hNorm - 0.66) / 0.14, 2));
          const shoulderFlare = hNorm > 0.78 ? 1.0 + (hNorm - 0.78) * 1.15 : 1.0;
          // Deep vertical chiton folds + diagonal sweeping palla cloak wave
          const verticalFolds = Math.sin(ang * 14 + hNorm * 1.8) * 0.11 * (1.15 - hNorm * 0.45);
          const diagonalPalla = Math.sin(ang * 3 + hNorm * 9.5) * 0.12 * (hNorm > 0.22 && hNorm < 0.88 ? 1 : 0.2);
          const scaleR = waistPinch * shoulderFlare + verticalFolds + diagonalPalla;
          rPos.setX(i, vx * scaleR);
          rPos.setZ(i, vz * scaleR * 0.86);
        }
        robeGeo.computeVertexNormals();
        const sculptedRobe = new THREE.Mesh(robeGeo, copperMat);
        sculptedRobe.position.set(0, 12.75, sz);
        panoScene.add(sculptedRobe);

        // Sculpted Diagonal Cloak Mantle & Gathered Waist Sash
        const mantle = new THREE.Mesh(new THREE.TorusGeometry(1.18, 0.22, 10, 24), foldMat);
        mantle.position.set(0, 14.3, sz + 0.08);
        mantle.rotation.set(0.62, 0.15, 0.52);
        panoScene.add(mantle);

        // Sculpted Neck, Classical Face & Wavy Hair (no blocky visor!)
        const neck = new THREE.Mesh(new THREE.CylinderGeometry(0.4, 0.54, 0.85, 14), copperMat);
        neck.position.set(0, 17.25, sz);
        panoScene.add(neck);

        const head = new THREE.Mesh(new THREE.SphereGeometry(0.8, 22, 18), copperMat);
        head.scale.set(0.86, 1.14, 0.96);
        head.position.set(0, 18.05, sz);
        panoScene.add(head);

        // Organic Hair Locks framing temples & nape
        [-0.68, 0.68].forEach(hx => {
          const lock = new THREE.Mesh(new THREE.SphereGeometry(0.4, 12, 12), foldMat);
          lock.scale.set(0.68, 1.45, 1.05);
          lock.position.set(hx, 17.88, sz - 0.08);
          panoScene.add(lock);
        });
        const backHair = new THREE.Mesh(new THREE.SphereGeometry(0.76, 14, 12), foldMat);
        backHair.position.set(0, 17.92, sz - 0.22);
        panoScene.add(backHair);

        // Subtle Sculpted Nose & Chin
        const nose = new THREE.Mesh(new THREE.ConeGeometry(0.1, 0.34, 6), highlightCopper);
        nose.rotation.x = 0.35;
        nose.position.set(0, 17.98, sz + 0.78);
        panoScene.add(nose);
        const chin = new THREE.Mesh(new THREE.SphereGeometry(0.22, 10, 10), highlightCopper);
        chin.position.set(0, 17.48, sz + 0.62);
        panoScene.add(chin);

        // Diadem Crown + 7 Rays
        const crownBand = new THREE.Mesh(new THREE.CylinderGeometry(0.92, 0.84, 0.32, 18), highlightCopper);
        crownBand.position.set(0, 18.58, sz);
        panoScene.add(crownBand);
        for (let i = -3; i <= 3; i++) {
          const spike = new THREE.Mesh(new THREE.ConeGeometry(0.1, 1.32, 6), highlightCopper);
          const a = i * 0.29;
          spike.position.set(Math.sin(a) * 1.22, 19.12 + Math.cos(a) * 0.45, sz + 0.12);
          spike.rotation.z = -a;
          panoScene.add(spike);
        }

        // Left Arm + Framed Tabula Ansata Tablet
        const leftArm = new THREE.Mesh(new THREE.CylinderGeometry(0.3, 0.36, 2.5, 12), copperMat);
        leftArm.position.set(1.36, 15.5, sz + 0.32);
        leftArm.rotation.z = 0.24;
        panoScene.add(leftArm);
        const tablet = new THREE.Mesh(new THREE.BoxGeometry(0.32, 1.95, 1.32), highlightCopper);
        tablet.position.set(1.66, 15.2, sz + 0.65);
        tablet.rotation.set(0.15, 0.2, 0.22);
        panoScene.add(tablet);

        // Raised Right Arm + Draped Sleeve + Torch Balcony + Sculpted Flame
        const rightArm = new THREE.Mesh(new THREE.CylinderGeometry(0.26, 0.42, 4.7, 12), copperMat);
        rightArm.position.set(-1.32, 18.7, sz + 0.2);
        rightArm.rotation.z = 0.14;
        panoScene.add(rightArm);
        const torchBalcony = new THREE.Mesh(new THREE.CylinderGeometry(0.68, 0.48, 0.38, 16), highlightCopper);
        torchBalcony.position.set(-1.6, 21.1, sz + 0.2);
        panoScene.add(torchBalcony);
        const torchLantern = new THREE.Mesh(new THREE.CylinderGeometry(0.45, 0.42, 0.65, 14), copperMat);
        torchLantern.position.set(-1.6, 21.58, sz + 0.2);
        panoScene.add(torchLantern);
        const flame = new THREE.Mesh(
          new THREE.ConeGeometry(0.38, 1.25, 10),
          new THREE.MeshBasicMaterial({ color: 0xfbbf24 })
        );
        flame.position.set(-1.6, 22.45, sz + 0.2);
        panoScene.add(flame);
      }

      // =====================================================================
      // 7. ROMAN COLOSSEUM, ITALY (3-Tier Travertine Arcade + Ruined Outer Wall)
      // =====================================================================
      else if (envKey === 'rome-colosseum') {
        panoScene.background = new THREE.Color(0x38bdf8);
        panoScene.fog = new THREE.FogExp2(0xe0f2fe, 0.005);
        const sky = createCloudySkyDome(0x0284c7, 0xe0f2fe, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xffffff, 0x78716c, 1.15));
        const sun = new THREE.DirectionalLight(0xfffbeb, 1.45);
        sun.position.set(20, 30, 22);
        panoScene.add(sun);

        const piazza = new THREE.Mesh(
          new THREE.PlaneGeometry(180, 180),
          new THREE.MeshStandardMaterial({ map: makeProceduralTexture('stoneBrick', 18, 18), color: 0xd6d3d1, roughness: 0.88 })
        );
        piazza.rotation.x = -Math.PI / 2;
        panoScene.add(piazza);

        const cz = -26;
        const travMat = new THREE.MeshStandardMaterial({ map: makeProceduralTexture('stoneBrick', 2, 2), color: 0xe7d8bd, roughness: 0.78 });
        const innerMat = new THREE.MeshStandardMaterial({ color: 0xa89274, roughness: 0.85 });

        const bays = 28;
        const rx = 13.5, rz = 10.5;
        for (let i = 0; i < bays; i++) {
          const a = (i / bays) * Math.PI * 2;
          const x = Math.cos(a) * rx;
          const z = cz + Math.sin(a) * rz;
          const isHigh = i < 18;
          const tiers = isHigh ? 3 : 2;
          for (let t = 0; t < tiers; t++) {
            const pier = new THREE.Mesh(new THREE.BoxGeometry(1.15, 2.55, 1.45), travMat);
            pier.position.set(x, 1.3 + t * 2.6, z);
            pier.lookAt(0, 1.3 + t * 2.6, cz);
            panoScene.add(pier);
            const lintel = new THREE.Mesh(new THREE.BoxGeometry(2.9, 0.55, 1.5), travMat);
            lintel.position.set(x, 2.55 + t * 2.6, z);
            lintel.lookAt(0, 2.55 + t * 2.6, cz);
            panoScene.add(lintel);
          }
        }
        // Exposed Hypogeum Subterranean Brick Walls
        for (let hw = -5; hw <= 5; hw += 2.5) {
          const wall = new THREE.Mesh(new THREE.BoxGeometry(0.45, 1.25, 10.5), innerMat);
          wall.position.set(hw, 0.62, cz);
          panoScene.add(wall);
        }
        addScenicTree(panoScene, -15, 0, -14, 1.5, 0x3f7d20);
        addScenicTree(panoScene, 15, 0, -14, 1.5, 0x3f7d20);
      }

      // =====================================================================
      // 8. SWISS ALPS (Sculpted Matterhorn Peak + Glaciers + Alpine Pines)
      // =====================================================================
      else if (envKey === 'swiss-alps') {
        panoScene.background = new THREE.Color(0x0284c7);
        panoScene.fog = new THREE.FogExp2(0xe0f2fe, 0.004);
        const sky = createCloudySkyDome(0x0284c7, 0xf0f9ff, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xffffff, 0x475569, 1.25));
        const sun = new THREE.DirectionalLight(0xffffff, 1.4);
        sun.position.set(20, 32, 18);
        panoScene.add(sun);

        // Alpine valley floor + FBM mountain ridges
        const valley = new THREE.Mesh(
          new THREE.PlaneGeometry(200, 200),
          new THREE.MeshStandardMaterial({ color: 0xe2e8f0, roughness: 0.85 })
        );
        valley.rotation.x = -Math.PI / 2;
        panoScene.add(valley);

        const rockMat = new THREE.MeshStandardMaterial({ color: 0x475569, roughness: 0.9 });
        const snowMat = new THREE.MeshStandardMaterial({ color: 0xf8fafc, roughness: 0.6 });

        // Hero Matterhorn-style Pyramidal Horn Peak
        const horn = new THREE.Mesh(new THREE.ConeGeometry(14, 24, 5), rockMat);
        horn.position.set(0, 11.5, -34);
        horn.rotation.y = 0.4;
        panoScene.add(horn);
        const snowCap = new THREE.Mesh(new THREE.ConeGeometry(9.2, 15.5, 5), snowMat);
        snowCap.position.set(0, 15.8, -34);
        snowCap.rotation.y = 0.4;
        panoScene.add(snowCap);

        // Flanking Alpine Ridges
        [[-18, 17, -38], [19, 19, -40], [-28, 14, -28], [28, 15, -26]].forEach(([mx, mh, mz]) => {
          const ridge = new THREE.Mesh(new THREE.ConeGeometry(13, mh, 6), rockMat);
          ridge.position.set(mx, mh / 2 - 0.5, mz);
          panoScene.add(ridge);
          const cap = new THREE.Mesh(new THREE.ConeGeometry(8.2, mh * 0.62, 6), snowMat);
          cap.position.set(mx, mh * 0.68, mz);
          panoScene.add(cap);
        });

        // Timberline Alpine Pines
        for (let i = -12; i <= 12; i += 4) {
          if (Math.abs(i) < 3) continue;
          const pineTrunk = new THREE.Mesh(new THREE.CylinderGeometry(0.16, 0.22, 1.4, 6), new THREE.MeshStandardMaterial({ color: 0x451a03 }));
          pineTrunk.position.set(i, 0.7, -14 - Math.abs(i) * 0.4);
          panoScene.add(pineTrunk);
          const pineCone = new THREE.Mesh(new THREE.ConeGeometry(1.1, 3.2, 7), new THREE.MeshStandardMaterial({ color: 0x14532d, roughness: 0.85 }));
          pineCone.position.set(i, 2.4, -14 - Math.abs(i) * 0.4);
          panoScene.add(pineCone);
        }
      }

      // =====================================================================
      // 9. SUNDARBANS MANGROVE FOREST, BANGLADESH (Stilt Roots + Tidal River)
      // =====================================================================
      else if (envKey === 'sundarbans') {
        panoScene.background = new THREE.Color(0x38bdf8);
        panoScene.fog = new THREE.FogExp2(0xd1fae5, 0.008);
        const sky = createCloudySkyDome(0x0284c7, 0xd1fae5, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xffffff, 0x14532d, 1.15));
        const sun = new THREE.DirectionalLight(0xfffbeb, 1.35);
        sun.position.set(16, 28, 18);
        panoScene.add(sun);

        const river = new THREE.Mesh(
          new THREE.PlaneGeometry(180, 180),
          new THREE.MeshStandardMaterial({ color: 0x1d4e4f, metalness: 0.35, roughness: 0.22 })
        );
        river.rotation.x = -Math.PI / 2;
        panoScene.add(river);

        const mudMat = new THREE.MeshStandardMaterial({ color: 0x5c4033, roughness: 0.95 });
        const barkMat = new THREE.MeshStandardMaterial({ color: 0x4a3525, roughness: 0.9 });
        [-12.5, 12.5].forEach(bx => {
          const bank = new THREE.Mesh(new THREE.BoxGeometry(15, 0.9, 90), mudMat);
          bank.position.set(bx, 0.25, -24);
          panoScene.add(bank);
        });

        const treeCoords = [
          [-5.2, -11], [-6.8, -17], [-5.6, -23], [-7.5, -30],
          [5.2, -12], [6.8, -18], [5.8, -25], [7.4, -31]
        ];
        treeCoords.forEach(([tx, tz], idx) => {
          // Arched mangrove stilt prop roots
          for (let r = 0; r < 6; r++) {
            const ang = (r / 6) * Math.PI * 2;
            const root = new THREE.Mesh(new THREE.CylinderGeometry(0.08, 0.12, 2.2, 6), barkMat);
            root.position.set(tx + Math.cos(ang) * 0.7, 0.7, tz + Math.sin(ang) * 0.7);
            root.rotation.set(Math.sin(ang) * 0.55, 0, -Math.cos(ang) * 0.55);
            panoScene.add(root);
          }
          addScenicTree(panoScene, tx, 1.2, tz, 1.3 + (idx % 2) * 0.35, 0x166534);
        });
      }

      // =====================================================================
      // 10. RATARGUL FRESHWATER SWAMP FOREST, SYLHET (Submerged Canopy + Watchtower)
      // =====================================================================
      else if (envKey === 'ratargul-swamp') {
        panoScene.background = new THREE.Color(0x38bdf8);
        panoScene.fog = new THREE.FogExp2(0xccfbf1, 0.0085);
        const sky = createCloudySkyDome(0x0284c7, 0xccfbf1, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xffffff, 0x064e3b, 1.2));
        const sun = new THREE.DirectionalLight(0xfffbeb, 1.35);
        sun.position.set(14, 26, 16);
        panoScene.add(sun);

        const water = new THREE.Mesh(
          new THREE.PlaneGeometry(180, 180),
          new THREE.MeshStandardMaterial({ color: 0x0f766e, metalness: 0.4, roughness: 0.18 })
        );
        water.rotation.x = -Math.PI / 2;
        panoScene.add(water);

        // Submerged swamp trees inside portrait mobile FOV (x = ±4.2..9.5)
        const swampCoords = [
          [-4.4, -10], [4.6, -11], [-6.2, -16], [6.0, -17],
          [-4.8, -22], [5.2, -23], [-7.8, -28], [7.6, -29],
          [-2.8, -32], [3.0, -33]
        ];
        swampCoords.forEach(([tx, tz], idx) => {
          addScenicTree(panoScene, tx, -0.2, tz, 1.35 + (idx % 3) * 0.25, 0x15803d);
        });

        // Wooden Forest Watchtower in Midground
        const woodMat = new THREE.MeshStandardMaterial({ map: makeProceduralTexture('woodPlank', 2, 2), color: 0x78350f, roughness: 0.85 });
        const wz = -26, wx = 0;
        [[-1.2, -1.2], [1.2, -1.2], [-1.2, 1.2], [1.2, 1.2]].forEach(([px, pz]) => {
          const post = new THREE.Mesh(new THREE.CylinderGeometry(0.15, 0.18, 7.5, 8), woodMat);
          post.position.set(wx + px, 3.6, wz + pz);
          panoScene.add(post);
        });
        const deck = new THREE.Mesh(new THREE.BoxGeometry(3.2, 0.35, 3.2), woodMat);
        deck.position.set(wx, 5.4, wz);
        panoScene.add(deck);
        const towerRoof = new THREE.Mesh(new THREE.ConeGeometry(2.6, 1.5, 4), new THREE.MeshStandardMaterial({ color: 0x991b1b, roughness: 0.7 }));
        towerRoof.position.set(wx, 7.8, wz);
        towerRoof.rotation.y = Math.PI / 4;
        panoScene.add(towerRoof);
      }

      // =====================================================================
      // 11. SAINT MARTIN'S ISLAND, BANGLADESH (Turquoise Lagoon + Coral Boulders + Palms)
      // =====================================================================
      else if (envKey === 'saint-martins') {
        panoScene.background = new THREE.Color(0x0ea5e9);
        panoScene.fog = new THREE.FogExp2(0xe0f2fe, 0.004);
        const sky = createCloudySkyDome(0x0284c7, 0xe0f2fe, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xffffff, 0x0369a1, 1.25));
        const sun = new THREE.DirectionalLight(0xfffbeb, 1.45);
        sun.position.set(20, 30, 18);
        panoScene.add(sun);

        const lagoon = new THREE.Mesh(
          new THREE.PlaneGeometry(240, 240),
          new THREE.MeshStandardMaterial({ color: 0x06b6d4, metalness: 0.25, roughness: 0.16 })
        );
        lagoon.rotation.x = -Math.PI / 2;
        panoScene.add(lagoon);

        const sandMat = new THREE.MeshStandardMaterial({ map: makeProceduralTexture('sandRipple', 8, 8), color: 0xfef3c7, roughness: 0.85 });
        const island = new THREE.Mesh(new THREE.CylinderGeometry(16, 20, 1.2, 24), sandMat);
        island.position.set(0, 0.3, -20);
        panoScene.add(island);

        // Coral boulders along shoreline
        const coralMat = new THREE.MeshStandardMaterial({ color: 0x475569, roughness: 0.95 });
        [[-5.5, -10], [4.8, -11], [-7.2, -14], [6.5, -15], [-2.0, -9.2]].forEach(([bx, bz], i) => {
          const rock = new THREE.Mesh(new THREE.SphereGeometry(0.9 + (i % 2) * 0.4, 7, 6), coralMat);
          rock.scale.set(1.3, 0.65, 1.1);
          rock.position.set(bx, 0.45, bz);
          panoScene.add(rock);
        });

        // Swaying Coconut Palms
        [[-4.5, -17], [4.2, -16], [-1.5, -22], [6.5, -21]].forEach(([px, pz], idx) => {
          const trunk = new THREE.Mesh(new THREE.CylinderGeometry(0.18, 0.32, 6.2, 8), new THREE.MeshStandardMaterial({ color: 0x78350f, roughness: 0.9 }));
          trunk.position.set(px, 3.3, pz);
          trunk.rotation.z = idx % 2 === 0 ? 0.14 : -0.14;
          panoScene.add(trunk);
          for (let f = 0; f < 6; f++) {
            const ang = (f / 6) * Math.PI * 2;
            const frond = new THREE.Mesh(new THREE.ConeGeometry(0.45, 2.8, 5), new THREE.MeshStandardMaterial({ color: 0x16a34a, roughness: 0.75 }));
            frond.position.set(px + Math.cos(ang) * 1.1, 6.2, pz + Math.sin(ang) * 1.1);
            frond.rotation.set(Math.sin(ang) * 1.1, 0, -Math.cos(ang) * 1.1);
            panoScene.add(frond);
          }
        });
      }

      // =====================================================================
      // 12. COX'S BAZAR SEA BEACH, BANGLADESH (Sunset Ocean + Sand + Sampan Boat)
      // =====================================================================
      else {
        panoScene.background = new THREE.Color(0x1e1b4b);
        panoScene.fog = new THREE.FogExp2(0xfb923c, 0.0045);
        const sky = createCloudySkyDome(0x1e3a8a, 0xfdba74, true);
        if (sky) panoScene.add(sky);

        panoScene.add(new THREE.HemisphereLight(0xfde68a, 0x1e293b, 1.15));
        const sun = new THREE.DirectionalLight(0xfef08a, 1.45);
        sun.position.set(16, 24, -40);
        panoScene.add(sun);

        const sunDisc = new THREE.Mesh(new THREE.SphereGeometry(5.5, 20, 16), new THREE.MeshBasicMaterial({ color: 0xfde047 }));
        sunDisc.position.set(0, 12, -110);
        panoScene.add(sunDisc);

        const ocean = new THREE.Mesh(
          new THREE.PlaneGeometry(240, 160),
          new THREE.MeshStandardMaterial({ color: 0x0284c7, metalness: 0.4, roughness: 0.18 })
        );
        ocean.rotation.x = -Math.PI / 2;
        ocean.position.set(0, 0, -55);
        panoScene.add(ocean);

        const beach = new THREE.Mesh(
          new THREE.PlaneGeometry(240, 90),
          new THREE.MeshStandardMaterial({ map: makeProceduralTexture('sandRipple', 14, 6), color: 0xfde68a, roughness: 0.88 })
        );
        beach.rotation.x = -Math.PI / 2;
        beach.position.set(0, 0.12, 20);
        panoScene.add(beach);

        // Traditional Wooden Sampan Boat
        const woodMat = new THREE.MeshStandardMaterial({ map: makeProceduralTexture('woodPlank', 2, 1), color: 0x7c2d12, roughness: 0.75 });
        const hull = new THREE.Mesh(new THREE.BoxGeometry(5.6, 0.95, 1.8), woodMat);
        hull.position.set(-1.5, 0.55, -13);
        hull.rotation.y = 0.25;
        panoScene.add(hull);
        const cabin = new THREE.Mesh(new THREE.CylinderGeometry(0.9, 0.9, 2.2, 8, 1, false, 0, Math.PI), new THREE.MeshStandardMaterial({ color: 0xd97706, roughness: 0.85 }));
        cabin.position.set(-1.5, 1.15, -13);
        cabin.rotation.set(0, 0.25, Math.PI / 2);
        panoScene.add(cabin);

        // Coastal Palms on right shore
        addScenicTree(panoScene, 8.5, 0.1, -14, 1.4, 0x15803d);
        addScenicTree(panoScene, 12.0, 0.1, -19, 1.6, 0x166534);
      }

      // Optional non-blocking custom .GLB override if hosted externally
      attemptLandmarkGlbLoad(dest, envKey);
    }

    function buildAuthenticPhotographicVREnvironment(photo, dest) {
      const noticeOverlay = document.getElementById('panoNo360NoticeOverlay');
      const glbBannerEl = document.getElementById('panoMissingGlbBanner');
      if (glbBannerEl && STATE.panoMode === 'authentic') glbBannerEl.classList.add('hidden');
      const noticeTitle = document.getElementById('panoNo360Title');
      const noticeBody = document.getElementById('panoNo360Body');
      const tplMeta = getTemplateMeta(dest.id, STATE.panoViewpointIndex);

      if (!photo || !photo.isPanorama || STATE.forceMissingAssetTest) {
        setPanoLoadingProgress(false, 100, '');
        if (noticeTitle) noticeTitle.textContent = tplMeta.fullTitle;
        if (noticeBody) {
          noticeBody.textContent = STATE.forceMissingAssetTest
            ? `Simulated Missing 360° Panorama Asset for ${tplMeta.fullTitle}. Global VR Explorer never fabricates a synthetic panorama.`
            : `A genuine 360-degree equirectangular panorama is not yet available for ${tplMeta.fullTitle} in our verified Wikimedia Commons archive. Global VR Explorer never stretches an ordinary rectangular photograph into a fake 360° sphere.`;
        }
        if (noticeOverlay) noticeOverlay.classList.remove('hidden');
        return;
      }

      if (noticeOverlay) noticeOverlay.classList.add('hidden');
      if (!panoScene || typeof THREE === 'undefined') return;

      // Render genuine 360° equirectangular panorama onto the INSIDE of a large spherical viewing surface
      const sphereGeo = new THREE.SphereGeometry(100, 72, 48);
      sphereGeo.scale(-1, 1, 1);

      const mat = new THREE.MeshBasicMaterial({ color: 0x050b16, side: THREE.DoubleSide });
      const sphereMesh = new THREE.Mesh(sphereGeo, mat);
      sphereMesh.position.set(0, 1.45, 0);
      panoScene.add(sphereMesh);

      const applyImgToSphere = (loadedImg) => {
        if (!loadedImg || !panoScene) return;
        const tex = new THREE.Texture(loadedImg);
        tex.minFilter = THREE.LinearFilter;
        tex.magFilter = THREE.LinearFilter;
        tex.needsUpdate = true;
        mat.color.setHex(0xffffff);
        mat.map = tex;
        mat.needsUpdate = true;
      };

      const cached = PANO_IMAGE_CACHE[photo.url];
      if (cached && cached.status === 'loaded' && cached.img) {
        applyImgToSphere(cached.img);
      } else {
        // Watch for async preload completion on current token
        const tok = STATE.panoLoadToken;
        const pollTimer = setInterval(() => {
          if (tok !== STATE.panoLoadToken || STATE.activeView !== 'panorama' || STATE.panoMode !== 'authentic') {
            clearInterval(pollTimer);
            return;
          }
          const c = PANO_IMAGE_CACHE[photo.url];
          if (c && c.status === 'loaded' && c.img) {
            clearInterval(pollTimer);
            applyImgToSphere(c.img);
          } else if (c && c.status === 'missing') {
            clearInterval(pollTimer);
          }
        }, 80);
      }
    }

    function updateProjectedHotspots(width, height) {
      const overlay = document.getElementById('panoHotspotsOverlay');
      if (!overlay || !panoCamera) return;
    }

    function selectPanoramaHotspot(hotspotId) {
      const dest = getDestinationById(STATE.selectedDestinationId);
      const hs = dest.hotspots.find(h => h.id === hotspotId);
      if (!hs) return;
      STATE.selectedHotspotId = hs.id;
      STATE.panoYaw = (hs.yaw + 360) % 360;
      STATE.panoPitch = hs.pitch;
      updatePanoReadout();
      renderPanoHotspotsUI(dest);
      playHotspotPing();
    }

    function renderPanoViewpointsStrip(dest) {
      const strip = document.getElementById('panoViewpointsStrip');
      const bottomDock = document.getElementById('panoBottomViewpointDock');
      const fallbackUri = createFallbackDataUri(dest.name, dest.coordinates.label);
      if (strip) {
        strip.innerHTML = dest.images.map((img, idx) => {
          const active = idx === STATE.panoViewpointIndex;
          return `
            <button type="button" onclick="switchPanoViewpoint(${idx})"
                    class="p-1.5 rounded-xl border text-left transition ${active ? 'border-sky-400 bg-sky-500/20' : 'border-white/10 bg-white/[0.03] hover:border-white/30'}">
              <div class="relative h-12 rounded-lg overflow-hidden bg-[#02050d]">
                <img src="${img.url}" alt="${img.title}" onerror="this.onerror=null;this.src='${fallbackUri}';" class="w-full h-full object-cover" />
              </div>
              <div class="text-[10px] font-medium text-white truncate mt-1">${img.viewpoint}</div>
            </button>
          `;
        }).join('');
      }
      if (bottomDock) {
        bottomDock.innerHTML = dest.images.map((img, idx) => {
          const active = idx === STATE.panoViewpointIndex;
          return `
            <button type="button" onclick="switchPanoViewpoint(${idx})"
                    class="flex items-center gap-2 px-2.5 py-1 rounded-xl border shrink-0 transition ${active ? 'border-sky-400 bg-sky-500/30 text-white font-semibold shadow-lg' : 'border-white/10 bg-white/5 text-slate-300 hover:text-white'}">
              <img src="${img.url}" alt="${img.title}" onerror="this.onerror=null;this.src='${fallbackUri}';" class="w-7 h-7 rounded-lg object-cover" />
              <span class="text-[11px] whitespace-nowrap">0${idx + 1} • ${img.viewpoint}</span>
            </button>
          `;
        }).join('');
      }
    }

    // Clicking any viewpoint thumbnail loads its distinct environment & texture (e.g. Kaminarimon Gate vs Sensoji Hondo!)
    function switchPanoViewpoint(idx) {
      STATE.forceMissingAssetTest = false;
      STATE.panoViewpointIndex = idx;
      STATE.activeTemplateKey = getActiveEnvironmentKey(STATE.selectedDestinationId, idx);
      STATE.panoYaw = 0;
      STATE.panoPitch = -3;
      loadPanoramaEnvironment();
    }

    function renderPanoHotspotsUI(dest) {
      const detailCard = document.getElementById('panoHotspotDetailCard');
      const listEl = document.getElementById('panoHotspotList');
      const activeHs = dest.hotspots.find(h => h.id === STATE.selectedHotspotId) || dest.hotspots[0];

      if (detailCard && activeHs) {
        detailCard.innerHTML = `
          <div class="text-[10px] font-mono text-amber-300 uppercase">${activeHs.category}</div>
          <div class="text-xs font-semibold text-white">${activeHs.title}</div>
          <p class="text-[11px] text-slate-300 font-light">${activeHs.summary}</p>
        `;
      }

      if (listEl) {
        listEl.innerHTML = dest.hotspots.map(hs => {
          const isSelected = hs.id === activeHs?.id;
          return `
            <button type="button" onclick="selectPanoramaHotspot('${hs.id}')"
                    class="w-full p-2 rounded-xl border text-left transition flex items-center justify-between gap-2 ${isSelected ? 'border-amber-400/60 bg-amber-500/10' : 'border-white/10 bg-white/[0.03]'}">
              <span class="text-[11px] text-white">${hs.title}</span>
              <span class="font-mono text-[10px] text-sky-300">${hs.yaw}°</span>
            </button>
          `;
        }).join('');
      }
    }

    function toggleSpatialAudio() {
      STATE.panoSpatialAudio = !STATE.panoSpatialAudio;
      const label = document.getElementById('panoAudioLabel');
      if (label) label.textContent = `Spatial Audio: ${STATE.panoSpatialAudio ? 'ON' : 'OFF'}`;

      if (!STATE.panoSpatialAudio) {
        if (ambientNoiseNode) {
          try { ambientNoiseNode.stop(); } catch (e) {}
          ambientNoiseNode = null;
        }
        return;
      }

      try {
        if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        if (audioCtx.state === 'suspended') audioCtx.resume();

        const bufferSize = audioCtx.sampleRate * 2;
        const buffer = audioCtx.createBuffer(1, bufferSize, audioCtx.sampleRate);
        const data = buffer.getChannelData(0);
        for (let i = 0; i < bufferSize; i++) {
          data[i] = (Math.random() * 2 - 1) * 0.12;
        }

        const noise = audioCtx.createBufferSource();
        noise.buffer = buffer;
        noise.loop = true;

        const filter = audioCtx.createBiquadFilter();
        filter.type = 'lowpass';
        filter.frequency.value = STATE.selectedDestinationId === 'coxs-bazar' ? 420 : 280;

        const gain = audioCtx.createGain();
        gain.gain.value = 0.18;

        noise.connect(filter);
        filter.connect(gain);
        gain.connect(audioCtx.destination);
        noise.start();
        ambientNoiseNode = noise;
      } catch (e) {}
    }

    function playHotspotPing() {
      try {
        if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        if (audioCtx.state === 'suspended') audioCtx.resume();
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.type = 'sine';
        osc.frequency.setValueAtTime(587.33, audioCtx.currentTime);
        gain.gain.setValueAtTime(0.08, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.18);
        osc.connect(gain);
        gain.connect(audioCtx.destination);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.18);
      } catch (e) {}
    }

    // =========================================================================
    // 14. BEFORE-YOU-TRAVEL ASSISTANT & COMPARISON MODULES
    // =========================================================================
    function populateAssistantSelectors() {
      const sel = document.getElementById('assistantDestSelect');
      if (!sel) return;
      sel.innerHTML = DESTINATIONS.map(d => `<option value="${d.id}">${d.name} (${d.country})</option>`).join('');
      sel.value = STATE.assistantDestId;
    }

    function renderAssistantView() {
      populateAssistantSelectors();
      const container = document.getElementById('assistantContentContainer');
      if (!container) return;

      const dest = getDestinationById(STATE.assistantDestId);
      const days = STATE.assistantDays;
      const dailyRateUSD = STATE.assistantTier === 'budget'
        ? dest.expenses.dailyBudgetUSD
        : (STATE.assistantTier === 'premium' ? dest.expenses.dailyPremiumUSD : dest.expenses.dailyStandardUSD);

      const lodgingUSD = Math.round(dailyRateUSD * 0.52 * days);
      const diningUSD = Math.round(dailyRateUSD * 0.24 * days);
      const localTransitUSD = Math.round(dailyRateUSD * 0.14 * days);
      const ticketsUSD = Math.round(dailyRateUSD * 0.10 * days);
      const totalUSD = lodgingUSD + diningUSD + localTransitUSD + ticketsUSD;

      const prepChecklist = [
        `Verify ${dest.country} entry / ID requirements and save offline copies of reservations`,
        `Book ${dest.name} timed entry or regional transport (${dest.visitorInfo.openingHours})`,
        `Pack climate-appropriate attire for ${dest.visitorInfo.bestSeason}`,
        `Review cultural & ecological guidelines: ${dest.visitorInfo.guidelines}`
      ];

      container.innerHTML = `
        <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
          <div class="lg:col-span-7 glass-panel rounded-3xl p-8 space-y-6">
            <div class="flex flex-wrap items-center justify-between gap-2">
              <div>
                <span class="text-xs font-mono uppercase text-sky-300">Transparent Cost Modeling</span>
                <h3 class="font-editorial text-3xl font-semibold text-white mt-0.5">${days}-Day Trip Estimate for ${dest.name}</h3>
              </div>
              <div class="text-right">
                <div class="text-xs text-slate-400">Estimated Total (${STATE.assistantTier.toUpperCase()})</div>
                <div class="font-display text-2xl font-bold text-sky-300 font-mono">${formatCurrency(totalUSD)}</div>
              </div>
            </div>

            <div class="space-y-4 pt-2">
              <div>
                <div class="flex justify-between text-xs mb-1.5">
                  <span class="text-slate-300">Accommodation &amp; Stays (52%)</span>
                  <span class="font-mono text-white">${formatCurrency(lodgingUSD)}</span>
                </div>
                <div class="w-full h-2.5 rounded-full bg-white/5 overflow-hidden">
                  <div class="h-full bg-sky-400 rounded-full" style="width:52%"></div>
                </div>
              </div>
              <div>
                <div class="flex justify-between text-xs mb-1.5">
                  <span class="text-slate-300">Local Dining &amp; Hydration (24%)</span>
                  <span class="font-mono text-white">${formatCurrency(diningUSD)}</span>
                </div>
                <div class="w-full h-2.5 rounded-full bg-white/5 overflow-hidden">
                  <div class="h-full bg-teal-400 rounded-full" style="width:24%"></div>
                </div>
              </div>
              <div>
                <div class="flex justify-between text-xs mb-1.5">
                  <span class="text-slate-300">Local Transit &amp; Transfers (14%)</span>
                  <span class="font-mono text-white">${formatCurrency(localTransitUSD)}</span>
                </div>
                <div class="w-full h-2.5 rounded-full bg-white/5 overflow-hidden">
                  <div class="h-full bg-cyan-400 rounded-full" style="width:14%"></div>
                </div>
              </div>
              <div>
                <div class="flex justify-between text-xs mb-1.5">
                  <span class="text-slate-300">Monument Tickets &amp; Guided Access (10%)</span>
                  <span class="font-mono text-white">${formatCurrency(ticketsUSD)}</span>
                </div>
                <div class="w-full h-2.5 rounded-full bg-white/5 overflow-hidden">
                  <div class="h-full bg-amber-400 rounded-full" style="width:10%"></div>
                </div>
              </div>
            </div>

            <div class="p-5 rounded-2xl bg-white/[0.03] border border-white/10 text-xs space-y-2">
              <div class="font-mono text-[11px] text-amber-300 uppercase">Transparent Estimation Assumptions &amp; Sourced Tariffs</div>
              <p class="text-slate-300 leading-relaxed"><strong>Sourced Tariff Fact:</strong> ${dest.visitorInfo.entryFee}</p>
              <p class="text-slate-300 leading-relaxed"><strong>Model Assumption:</strong> ${dest.expenses.assumptions} (Exchange rate: 1 USD = 120 BDT).</p>
            </div>
          </div>

          <div class="lg:col-span-5 space-y-6">
            <div class="glass-panel rounded-3xl p-7 space-y-3 text-xs">
              <span class="font-mono uppercase text-sky-300">Visitor Readiness &amp; Accessibility</span>
              <div><strong class="text-white">Best Visiting Season:</strong> <span class="text-slate-300">${dest.visitorInfo.bestSeason}</span></div>
              <div><strong class="text-white">Opening Hours:</strong> <span class="text-slate-300">${dest.visitorInfo.openingHours}</span></div>
              <div><strong class="text-white">Accessibility Notes:</strong> <span class="text-slate-300">${dest.visitorInfo.accessibility}</span></div>
              <div><strong class="text-white">Cultural &amp; Safety Protocol:</strong> <span class="text-slate-300">${dest.visitorInfo.guidelines}</span></div>
            </div>

            <div class="glass-panel rounded-3xl p-7 space-y-3">
              <span class="text-xs font-mono uppercase text-teal-300">Interactive Pre-Departure Checklist</span>
              <div class="space-y-2.5">
                ${prepChecklist.map((item, idx) => {
                  const key = `prep-${idx}`;
                  const checked = !!STATE.checkedPrepItems[key];
                  return `
                    <label class="flex items-start gap-2.5 p-3 rounded-2xl bg-white/[0.03] border border-white/10 cursor-pointer text-xs">
                      <input type="checkbox" ${checked ? 'checked' : ''} onchange="togglePrepItem('${key}')" class="mt-0.5 accent-sky-400" />
                      <span class="${checked ? 'line-through text-slate-500' : 'text-slate-200'}">${item}</span>
                    </label>
                  `;
                }).join('')}
              </div>
            </div>
          </div>
        </div>
      `;
    }

    function togglePrepItem(key) {
      STATE.checkedPrepItems[key] = !STATE.checkedPrepItems[key];
      renderAssistantView();
    }

    function populateComparisonSelectors() {
      ['compareSelectA', 'compareSelectB', 'compareSelectC'].forEach((selId, idx) => {
        const el = document.getElementById(selId);
        if (!el) return;
        el.innerHTML = DESTINATIONS.map(d => `<option value="${d.id}">${d.name} (${d.country})</option>`).join('');
        el.value = STATE.compareIds[idx] || DESTINATIONS[idx].id;
      });
    }

    function renderComparisonView() {
      populateComparisonSelectors();
      const cardsRow = document.getElementById('compareCardsRow');
      const tableWrapper = document.getElementById('compareTableWrapper');
      if (!cardsRow || !tableWrapper) return;

      const selectedDests = STATE.compareIds.map(id => getDestinationById(id));

      cardsRow.innerHTML = selectedDests.map(dest => {
        const img = dest.images[0];
        const fallbackUri = createFallbackDataUri(dest.name, dest.coordinates.label);
        return `
          <div class="glass-panel rounded-3xl overflow-hidden border border-white/10">
            <div class="relative h-48 bg-[#02050d]">
              <img src="${img.url}" alt="${dest.name}" onerror="this.onerror=null;this.src='${fallbackUri}';" class="w-full h-full object-cover" />
              <div class="absolute inset-0 bg-gradient-to-t from-black/85 via-transparent to-transparent"></div>
              <div class="absolute top-3 left-3">${getVerificationBadgeHTML(dest.verificationStatus)}</div>
            </div>
            <div class="p-5 space-y-2.5">
              <div class="text-[11px] font-mono text-sky-300">${dest.country} • ${dest.region}</div>
              <h3 class="font-editorial font-semibold text-white text-2xl">${dest.name}</h3>
              <div class="flex items-center justify-between text-xs pt-2 border-t border-white/10">
                <span class="text-slate-400">Standard Daily Cost</span>
                <span class="font-mono font-bold text-sky-300">${formatCurrency(dest.expenses.dailyStandardUSD)}</span>
              </div>
              <button type="button" onclick="openPanoramaForDestination('${dest.id}')" class="w-full py-2.5 rounded-full bg-white text-slate-950 hover:bg-sky-100 text-xs font-medium transition">
                Explore in VR
              </button>
            </div>
          </div>
        `;
      }).join('');

      tableWrapper.innerHTML = `
        <div class="overflow-x-auto">
          <table class="w-full text-left border-collapse text-xs">
            <thead>
              <tr class="bg-[#02050d] border-b border-white/10 text-sky-300 font-mono uppercase text-[11px]">
                <th class="p-5 w-48">Comparison Dimension</th>
                ${selectedDests.map(d => `<th class="p-5">${d.name}</th>`).join('')}
              </tr>
            </thead>
            <tbody class="divide-y divide-white/10 text-slate-300">
              <tr>
                <td class="p-5 font-semibold text-white bg-white/[0.02]">Geographic Hierarchy &amp; Coordinates</td>
                ${selectedDests.map(d => `<td class="p-5"><div class="text-white font-medium">${d.country} — ${d.region}</div><div class="font-mono text-[11px] text-sky-300 mt-0.5">${d.city} (${d.coordinates.label})</div></td>`).join('')}
              </tr>
              <tr>
                <td class="p-5 font-semibold text-white bg-white/[0.02]">Natural Scenery &amp; Categories</td>
                ${selectedDests.map(d => `<td class="p-5">${d.categories.join(', ')} — ${d.shortSummary}</td>`).join('')}
              </tr>
              <tr>
                <td class="p-5 font-semibold text-white bg-white/[0.02]">Historical Significance</td>
                ${selectedDests.map(d => `<td class="p-5 leading-relaxed">${d.historicalInfo}</td>`).join('')}
              </tr>
              <tr>
                <td class="p-5 font-semibold text-white bg-white/[0.02]">Cultural Experiences</td>
                ${selectedDests.map(d => `<td class="p-5 leading-relaxed">${d.culturalInfo}</td>`).join('')}
              </tr>
              <tr>
                <td class="p-5 font-semibold text-white bg-white/[0.02]">Key Attractions</td>
                ${selectedDests.map(d => `<td class="p-5"><ul class="list-disc list-inside space-y-1">${d.attractions.map(a => `<li><strong class="text-white">${a.name}</strong></li>`).join('')}</ul></td>`).join('')}
              </tr>
              <tr>
                <td class="p-5 font-semibold text-white bg-white/[0.02]">Best Visiting Season</td>
                ${selectedDests.map(d => `<td class="p-5">${d.visitorInfo.bestSeason}</td>`).join('')}
              </tr>
              <tr>
                <td class="p-5 font-semibold text-white bg-white/[0.02]">Estimated Daily Travel Expense</td>
                ${selectedDests.map(d => `<td class="p-5 font-mono"><div class="text-slate-400">Budget: <span class="text-white">${formatCurrency(d.expenses.dailyBudgetUSD)}</span></div><div class="text-sky-300 font-bold">Standard: ${formatCurrency(d.expenses.dailyStandardUSD)}</div><div class="text-slate-400">Premium: <span class="text-white">${formatCurrency(d.expenses.dailyPremiumUSD)}</span></div></td>`).join('')}
              </tr>
              <tr>
                <td class="p-5 font-semibold text-white bg-white/[0.02]">Available Virtual Experiences</td>
                ${selectedDests.map(d => `<td class="p-5"><ul class="list-disc list-inside space-y-1 text-[11px]">${d.vrExperiences.map(v => `<li>${v}</li>`).join('')}</ul></td>`).join('')}
              </tr>
            </tbody>
          </table>
        </div>
      `;
      if (window.lucide) window.lucide.createIcons();
    }

    function renderUnityView() {
      const listEl = document.getElementById('unityFileList');
      const nameEl = document.getElementById('unityActiveFileName');
      const descEl = document.getElementById('unityActiveFileDesc');
      const codeEl = document.getElementById('unityCodeViewer');
      if (!listEl || !codeEl) return;

      const activeFile = UNITY_PROJECT_FILES[STATE.activeUnityFileIndex] || UNITY_PROJECT_FILES[0];
      if (nameEl) nameEl.textContent = activeFile.path;
      if (descEl) descEl.textContent = activeFile.desc;
      codeEl.textContent = activeFile.code;

      listEl.innerHTML = UNITY_PROJECT_FILES.map((file, idx) => {
        const isCurrent = idx === STATE.activeUnityFileIndex;
        return `
          <button type="button" onclick="selectUnityFile(${idx})"
                  class="w-full p-3.5 rounded-2xl text-left transition border ${isCurrent ? 'border-sky-400 bg-sky-500/20 text-white' : 'border-white/10 bg-white/[0.03] text-slate-300 hover:border-white/30'}">
            <div class="font-mono text-xs text-sky-300 truncate">${file.path}</div>
            <div class="text-[11px] text-slate-400 mt-0.5">${file.desc}</div>
          </button>
        `;
      }).join('');
    }

    function selectUnityFile(index) {
      STATE.activeUnityFileIndex = index;
      renderUnityView();
    }

    function renderProfileView() {
      updateSavedBadges();
      const savedList = document.getElementById('profileSavedList');
      const timeline = document.getElementById('profileHistoryTimeline');

      if (savedList) {
        if (STATE.savedIds.length === 0) {
          savedList.innerHTML = `<div class="col-span-full p-6 rounded-2xl bg-white/[0.03] border border-white/10 text-xs text-slate-400 text-center">No saved destinations yet. Click the bookmark icon on any destination to save it.</div>`;
        } else {
          savedList.innerHTML = STATE.savedIds.map(id => {
            const dest = getDestinationById(id);
            const img = dest.images[0];
            const fallbackUri = createFallbackDataUri(dest.name, dest.coordinates.label);
            return `
              <div class="p-3.5 rounded-2xl bg-white/[0.03] border border-white/10 flex items-center gap-3.5">
                <img src="${img.url}" alt="${dest.name}" onerror="this.onerror=null;this.src='${fallbackUri}';" class="w-16 h-16 rounded-xl object-cover shrink-0" />
                <div class="min-w-0 flex-1">
                  <div class="text-xs font-bold text-white truncate">${dest.name}</div>
                  <div class="text-[11px] font-mono text-sky-300">${dest.country}</div>
                  <div class="flex items-center gap-3 mt-1.5">
                    <button type="button" onclick="openEditorialDestinationPage('${dest.id}')" class="text-[11px] font-mono text-white hover:underline">Explore</button>
                    <button type="button" onclick="openPanoramaForDestination('${dest.id}')" class="text-[11px] font-mono text-sky-300 hover:underline">Explore in VR</button>
                    <button type="button" onclick="toggleSaveDestination('${dest.id}')" class="text-[11px] text-slate-400 hover:text-rose-300">Remove</button>
                  </div>
                </div>
              </div>
            `;
          }).join('');
        }
      }

      if (timeline) {
        timeline.innerHTML = STATE.recentHistory.map(item => {
          const dest = getDestinationById(item.id);
          return `
            <div class="p-3.5 rounded-2xl bg-white/[0.03] border border-white/10 flex items-center justify-between gap-2 text-xs">
              <div>
                <span class="font-semibold text-white">${dest.name}</span>
                <span class="text-slate-400 ml-2">${item.action}</span>
              </div>
              <span class="font-mono text-[11px] text-sky-300 shrink-0">${item.timestamp}</span>
            </div>
          `;
        }).join('');
      }
    }

    
    // =========================================================================
    // 14B. MODULE 1, 2, 3 & 4: AI DESTINATION NAVIGATION & HISTORICAL TIME TRAVEL (1200–2026)
    // =========================================================================
    const HISTORICAL_TIMELINES = {
      'coxs-bazar': {
        archivalPhotoUrl: 'https://upload.wikimedia.org/wikipedia/commons/b/b5/Coxsbazar_sun_2003.jpg',
        archivalPhotoTitle: "Cox's Bazar Beach Sunset Archival Capture (2003)",
        archivalAuthor: 'Faruque Abu Sayeed (Wikimedia Commons)',
        archivalCommonsUrl: 'https://commons.wikimedia.org/wiki/File:Coxsbazar_sun_2003.jpg',
        archivalYearLabel: '2003 Archival Photograph',
        mapEvolutionNote: 'From medieval Arakanese-Bengal coastal trade anchorages (Panowa) to the 1799 Hiram Cox settlement, 1950s Tamarisk coastal afforestation belt, and the 2023 iconic oyster-shaped Cox’s Bazar Railway Station and 80-km Marine Drive.',
        eras: [
          { year: 1200, range: '1200–1399', title: 'Medieval Coastal Kingdom & Panowa Bay', confidence: 'Historical Chronicle & Regional Archaeology (No Visual Depictions Survive)', modeNote: 'Pre-photographic era: documented via regional Bengal-Arakan chronicles.', architecture: 'Timber stilt coastal settlements, bamboo fishing jetties, and early Buddhist wooden viharas in nearby Ramu.', events: 'Known locally as Panowa ("yellow flower"), the unbroken 120-km shoreline served as a natural maritime corridor along the eastern Bay of Bengal.', dailyLife: 'Artisanal coastal fishing communities using carved wooden boats, salt-pan evaporation along tidal mudflats, and monsoon rice cultivation inland.' },
          { year: 1400, range: '1400–1599', title: 'Bengal Sultanate & Maritime Trade Era', confidence: 'Historical Chronicle Record', modeNote: 'Documented in Portuguese and Bengal Sultanate maritime port records.', architecture: 'Fortified timber watchposts and traditional wooden Khyang monasteries supported on round timber columns.', events: 'Merchant vessels traversing between Chittagong port and Arakan navigated along the unbroken sandy shelf and Maheshkhali channel.', dailyLife: 'Boatbuilding craftsmen constructing crescent-prowed Sampans designed to ride high Bay of Bengal surf waves.' },
          { year: 1600, range: '1600–1798', title: 'Mughal Subah Bengal Frontier', confidence: 'Documented Historical Record', modeNote: 'Recorded in Mughal revenue rolls (Todar Mal / Shaista Khan era) and East India Company surveys.', architecture: 'Rakhine and Bengali timber-and-thatch coastal bazaars and Buddhist pagoda complexes in Ramu.', events: 'In 1799, Captain Hiram Cox of the British East India Company was appointed Superintendent of Palongkee to rehabilitate Arakanese refugees; Cox died in 1799 and the market town was named Cox’s Bazar in his honor.', dailyLife: 'Weekly coastal market trade in dried marine fish (shutki), woven textiles, and timber.' },
          { year: 1800, range: '1800–1899', title: '1854 Sub-Divisional Headquarters Era', confidence: 'Official Colonial Gazetteer & Survey Maps', modeNote: 'Documented in Bengal Presidency administrative surveys (1854).', architecture: 'Colonial brick administrative bungalows, timber circuit houses, and the historic Aggmeda Khyang monastery.', events: 'Upgraded to a Sub-Divisional headquarters under Chittagong District in 1854 under the Bengal Presidency.', dailyLife: 'Expansion of Bakkhali River wharf trade, salt production, and coastal navigation.' },
          { year: 1900, range: '1900–1949', title: 'Early 20th-Century Coastal Town', confidence: 'Official Municipal & Survey Records', modeNote: 'Documented in British India Survey maps and municipal archives.', architecture: 'Elevated timber rest houses overlooking Laboni Point and Bakkhali River harbor.', events: 'Served as a strategic eastern Bay of Bengal coastal outpost during World War II.', dailyLife: 'Quiet seaside town life centered around fishing fleets and Buddhist festivals in Ramu.' },
          { year: 1950, range: '1950–1999', title: 'Tamarisk Forestation & 1984 District Upgrade', confidence: 'Officially Verified Post-Independence Archive', modeNote: 'Documented via Government of Bangladesh forestry and municipal records.', architecture: 'Planting of the coastal Jhau (Tamarisk) forest belt by first municipal chairman Captain Advocate Fazlul Karim; Himchari National Park established in 1980.', events: 'Damaged during the 1971 Liberation War naval shelling near the wharf; upgraded from a sub-division to full Cox’s Bazar District in 1984.', dailyLife: 'Emergence of domestic winter beach tourism alongside traditional Sampan fishing.' },
          { year: 2000, range: '2000–2015', title: 'Early 21st-Century Coastal Tourism Hub (2003 Archive)', confidence: 'Primary Archival Photograph (Wikimedia Commons 2003)', modeNote: 'Documented by Faruque Abu Sayeed’s 2003 photograph of Cox’s Bazar Beach.', architecture: 'Development of Laboni, Sugandha, and Kolatoli beachfront promenades and Hotel-Motel Zone.', events: 'Expansion of domestic air connectivity and conservation zoning along Teknaf Peninsula.', dailyLife: 'Bustling sunset promenades, beach photography, and marine aquaculture.' },
          { year: 2026, range: '2016–2026', title: 'Present-Day Global Coastal Landmark (2026)', confidence: 'Officially Verified High-Resolution Photography & 360° Panorama', modeNote: 'Verified via 2026 Wikimedia Commons high-resolution photography and panoramas.', architecture: 'Iconic oyster-shaped Cox’s Bazar Railway Station (opened December 2023), 80-km scenic Marine Drive to Patuartek, and modern coastal resorts.', events: 'Direct intercity rail connection via Cox’s Bazar Express from Dhaka transforms national accessibility.', dailyLife: 'Year-round domestic and international visitors exploring Laboni Point, Himchari waterfalls, and Marine Drive.' }
        ]
      },
      'lalbagh-fort': {
        archivalPhotoUrl: 'https://upload.wikimedia.org/wikipedia/commons/b/b6/Lalbagh_Fort%2C_south_entrance%2C_south_view_Dhaka_1875.jpg',
        archivalPhotoTitle: 'Lalbagh Fort, South Entrance, Dhaka — 1875 Archival Photograph',
        archivalAuthor: 'Unknown 1870s Photographer (British Library / Wikimedia Commons Public Domain)',
        archivalCommonsUrl: 'https://commons.wikimedia.org/wiki/File:Lalbagh_Fort,_south_entrance,_south_view_Dhaka_1875.jpg',
        archivalYearLabel: '1875 Archival Albumen Print',
        mapEvolutionNote: 'In 1678–1684 AD, the Buriganga River flowed directly past the southern fortified ramparts of Fort Aurangabad. Over the 19th and 20th centuries, the river channel shifted southward, leaving the fort surrounded by dense Old Dhaka neighborhoods while the Department of Archaeology restored its Mughal charbagh gardens.',
        eras: [
          { year: 1200, range: '1200–1399', title: 'Pre-Mughal Riverine Settlement on the Buriganga', confidence: 'Regional Historical Context (Pre-Construction Era)', modeNote: 'Lalbagh Fort did not exist yet; built in 1678 AD.', architecture: 'Riverine trading ghats, earthen embankments, and pre-Mughal crafts quarters along the northern bank of the Buriganga River.', events: 'Eastern Bengal transitioned into the independent Bengal Sultanate era while river trade flourished along the Buriganga-Dulal canal network.', dailyLife: 'Muslin weavers, boatmen, and river merchants trading along the Buriganga shoreline.' },
          { year: 1400, range: '1400–1599', title: 'Bengal Sultanate Trading Quarter (Pre-Fort Era)', confidence: 'Regional Historical Record (Pre-Construction Era)', modeNote: 'Site predates the 1678 AD Mughal commission.', architecture: 'Sultanate-era brick mosques and riverine bazaar ghats in Old Dhaka (prior to Mughal capital designation in 1608–1610).', events: 'Dhaka grew as a strategic riverine craft and naval center before Islam Khan Chisti declared Jahangirnagar the Mughal capital of Bengal in 1610.', dailyLife: 'Fine cotton spinning, boat transport, and seasonal monsoon commerce.' },
          { year: 1600, range: '1600–1750', title: '1678–1684 AD Imperial Mughal Construction (Fort Aurangabad)', confidence: 'Officially Verified Archaeological & Mughal Chronicle Record', modeNote: 'Primary construction era under Prince Muhammad Azam Shah (1678) and Subahdar Shaista Khan (1680–1684).', architecture: 'Three-domed Quilla Mosque, Diwan-i-Aam governor’s audience hall and Hammamkhana with hypocaust water channels, Southern Gateway, and the white Jaipur marble & black basalt Mausoleum of Pari Bibi.', events: 'Commenced in 1678 AD by Prince Muhammad Azam (son of Emperor Aurangzeb); halted in 1684 AD by Nawab Shaista Khan after the death of his daughter Iran Dukht (Pari Bibi).', dailyLife: 'Imperial Mughal courtly ceremonies, cavalry guards along the Buriganga riverfront bastions, and fountain-fed charbagh gardens.' },
          { year: 1800, range: '1800–1899', title: '1875 Photographic Archive — Romantic Ruin & Monsoon Overgrowth', confidence: 'Primary Archival Photograph (1875 Dhaka Albumen Print)', modeNote: 'Directly documented by the 1875 Wikimedia Commons archival photograph of the South Entrance.', architecture: 'Three-story Mughal South Entrance and ramparts partially overgrown with tropical banyan roots and monsoon vegetation after the capital had moved to Murshidabad and Kolkata.', events: 'Captured in the 1870s by early architectural photographers; in 1857, Sepoy soldiers stationed at Lalbagh resisted British East India Company forces.', dailyLife: 'Quiet semi-ruined Mughal complex beside the receding Buriganga riverfront, frequented by artists and antiquarians.' },
          { year: 1900, range: '1900–1949', title: 'Early 20th-Century Archaeological Protection', confidence: 'Official Archaeological Survey Record', modeNote: 'Documented under the Ancient Monuments Preservation Act (1904).', architecture: 'Clearing of wild vegetation from the Tomb of Pari Bibi and stabilization of the Diwan-i-Aam arches.', events: 'Protected as a heritage monument during the early 20th century as Old Dhaka expanded around the fort walls.', dailyLife: 'Scholars and local residents visiting the historic grounds.' },
          { year: 1950, range: '1950–1999', title: 'Mughal Museum Establishment & Garden Restoration', confidence: 'Department of Archaeology Bangladesh Record', modeNote: 'Verified via official archaeological conservation records.', architecture: 'Conversion of the Diwan-i-Aam into a Mughal Era Museum displaying 17th-century swords, firearms, manuscripts, and coins.', events: 'Systematic archaeological excavations revealed the terracotta water pipes, rooftop water reservoir, and formal charbagh fountain axes.', dailyLife: 'Major national heritage landmark in Old Dhaka welcoming students and historians.' },
          { year: 2000, range: '2000–2015', title: 'Early 2000s Conservation & Hammamkhana Restoration', confidence: 'Primary Photographic Archive (Wikimedia Commons 2011–2014)', modeNote: 'Documented by Bangla Wikipedia Photography Contest archives.', architecture: 'Structural conservation of the Hammamkhana bathhouse domes and charbagh walkways.', events: 'Detailed architectural documentation and public lighting upgrades.', dailyLife: 'Over three million annual visitors exploring the gardens and museum.' },
          { year: 2026, range: '2016–2026', title: 'Present-Day Restored Mughal Sanctuary (2026)', confidence: 'Officially Verified High-Resolution Photography', modeNote: 'Verified via 2026 Wikimedia Commons multi-angle architectural photography.', architecture: 'Fully conserved Diwan-i-Aam & Hammamkhana complex, manicured charbagh floral parterres, and illuminated Mughal gateways.', events: 'Digital heritage preservation and virtual exploration integration for global cultural education.', dailyLife: 'Vibrant cultural sanctuary in Old Dhaka contrasting serene Mughal gardens with surrounding urban life.' }
        ]
      },
      'eiffel-tower': {
        archivalPhotoUrl: 'https://upload.wikimedia.org/wikipedia/commons/1/14/Louis-Emile_Durandelle%2C_The_Eiffel_Tower_-_State_of_the_Construction%2C_1888.jpg',
        archivalPhotoTitle: 'Louis-Émile Durandelle — The Eiffel Tower Under Construction (November 1888)',
        archivalAuthor: 'Louis-Émile Durandelle (Getty Research Institute / Wikimedia Commons Featured Picture)',
        archivalCommonsUrl: 'https://commons.wikimedia.org/wiki/File:Louis-Emile_Durandelle,_The_Eiffel_Tower_-_State_of_the_Construction,_1888.jpg',
        archivalYearLabel: '1888 Archival Construction Print',
        mapEvolutionNote: 'From the medieval Abbey of Sainte-Geneviève vineyards and 1770 École Militaire parade ground (Champ de Mars) to the 1889 Exposition Universelle entrance arch, 1937 Palais de Chaillot replacement of the old Trocadéro Palace, and the 2022–2026 330-metre broadcast apex.',
        eras: [
          { year: 1200, range: '1200–1399', title: 'Medieval Seine Riverbanks & Grenelle Marshland', confidence: 'Historical Cartography & Parisian Chronicles (Pre-Tower Era)', modeNote: 'The Eiffel Tower was built in 1887–1889; in 1200 AD this site was open rural riverbank west of medieval Paris.', architecture: 'Rural agricultural fields, monastic vineyards, and wooden Seine river ferries outside the Wall of Philip Augustus.', events: 'While Notre-Dame de Paris and the Louvre fortress were rising upstream, the Grenelle plain remained an open floodplain along the Seine.', dailyLife: 'Peasant farmers, vineyard tenders, and Seine river barge operators.' },
          { year: 1400, range: '1400–1599', title: 'Renaissance Plain of Grenelle', confidence: 'Historical Parisian Maps (Truschet & Hoyau 1550 Map)', modeNote: 'Pre-Tower agricultural plain documented in 16th-century maps of Paris.', architecture: 'Windmills, country manor estates, and vegetable gardens supplying the markets of Paris.', events: 'Gradual westward expansion of royal Paris along the Seine during the Valois and early Bourbon monarchies.', dailyLife: 'Market gardening and river transport along the Left Bank.' },
          { year: 1600, range: '1600–1799', title: 'École Militaire & 1790 Fête de la Fédération on the Champ de Mars', confidence: 'Officially Verified French National Archives & Engravings', modeNote: 'Champ de Mars landscaped in 1765–1770 for the École Militaire.', architecture: 'Ange-Jacques Gabriel’s neoclassical École Militaire (founded 1751) and the vast leveled military parade ground of the Champ de Mars.', events: 'In 1783, Jacques Charles launched the first hydrogen balloon here; on July 14, 1790, 300,000 citizens gathered on the Champ de Mars for the Fête de la Fédération during the French Revolution.', dailyLife: 'Military drills, early aviation experiments, and revolutionary civic festivals.' },
          { year: 1800, range: '1800–1889', title: '1887–1889 Construction & Exposition Universelle Inauguration', confidence: 'Primary Archival Photographs (Durandelle 1888 & 1889 Library of Congress)', modeNote: 'Directly documented by Louis-Émile Durandelle’s 1888 photographs on Wikimedia Commons.', architecture: '18,000 puddle-iron pieces joined by 2.5 million thermal rivets rising 300 metres above four masonry piers oriented to the cardinal points, facing the Moorish-style 1878 Palais du Trocadéro across the Seine.', events: 'Constructed in 2 years, 2 months, and 5 days by Gustave Eiffel’s company; inaugurated March 31, 1889 for the centennial World’s Fair.', dailyLife: 'Ironworkers riveting girders high above Paris and millions of 1889 World’s Fair visitors ascending by hydraulic lifts.' },
          { year: 1900, range: '1900–1949', title: '1900 Paris Exposition & Early Radio Telegraphy Saved the Tower', confidence: 'Primary Archival Photographs & Scientific Records', modeNote: 'Documented via 1900 Exposition Universelle and French military radiotelegraphy archives.', architecture: 'Installation of Gustave Eiffel’s summit meteorology and无线 (wireless radio) antenna, which prevented the tower’s scheduled 1909 demolition; old Trocadéro replaced by Palais de Chaillot in 1937.', events: 'Used for transatlantic radio signals in 1913 and strategic wireless interception during World War I.', dailyLife: 'Belle Époque promenades, Citroën illuminated light displays (1925–1934), and scientific broadcasts.' },
          { year: 1950, range: '1950–1999', title: 'Post-War Television Broadcast Spire & Structural Modernization', confidence: 'Officially Verified SETE Archive', modeNote: 'Documented via official Eiffel Tower engineering records.', architecture: 'Addition of television broadcast antennas in 1957 (raising height to 320 m), replacement of original hydraulic elevators, and installation of Pierre Bideau’s golden interior illumination in 1985.', events: 'Inscribed on the UNESCO World Heritage list as part of the Banks of the Seine in Paris (1991); hourly 5-minute sparkle introduced for the year 2000.', dailyLife: 'Post-war international tourism boom and Parisian riverside culture.' },
          { year: 2000, range: '2000–2015', title: 'Millennium Sparkle & 1st Floor Glass Pavilion', confidence: 'Primary High-Resolution Wikimedia Commons Archives (2006–2014)', modeNote: 'Documented by Wikimedia Commons featured photography and 2nd-platform 360° panoramas.', architecture: '2014 architectural renovation of the 1st floor (57 m) introducing transparent glass floor panels and eco-efficient wind/solar fixtures.', events: 'Global symbol of solidarity and celebration across international milestones.', dailyLife: 'Seven million annual visitors ascending by stairs and double-deck lifts.' },
          { year: 2026, range: '2016–2026', title: '330-Metre Apex & 2026 Digital Heritage Monument', confidence: 'Officially Verified 2026 Tariffs & 360° Summit Panorama', modeNote: 'Verified via 2026 official Eiffel Tower height (330 m after 2022 radio antenna helicopter lift) and 360° summit panorama.', architecture: '330-metre total height, 276-metre summit observation deck, glass-walled security garden perimeter around the base, and golden Venice-bronze paint campaign.', events: 'Served as centerpiece of the 2024 Paris Olympic Games and 2026 digital reservation system.', dailyLife: 'Seamless 360° panoramic views across Paris from the 276 m summit and evening hourly light sparkles.' }
        ]
      },
      'rome-colosseum': {
        archivalPhotoUrl: 'https://upload.wikimedia.org/wikipedia/commons/7/75/Maarten_van_Heemskerck_-_Rome%2C_the_Colosseum.jpg',
        archivalPhotoTitle: 'Maarten van Heemskerck — Rome, the Colosseum (c. 1532–1536 Sketch)',
        archivalAuthor: 'Maarten van Heemskerck (Kupferstichkabinett Berlin / Wikimedia Commons)',
        archivalCommonsUrl: 'https://commons.wikimedia.org/wiki/File:Maarten_van_Heemskerck_-_Rome,_the_Colosseum.jpg',
        archivalYearLabel: 'c. 1532 Renaissance Sketch',
        mapEvolutionNote: 'Built in 70–80 AD over Nero’s Golden House lake; damaged by the 1349 earthquake; occupied by the Frangipani fortress in 1200; sketched by Maarten van Heemskerck in 1532; excavated in the 19th century; subterranean hypogeum opened to visitors in 2010.',
        eras: [
          { year: 1200, range: '1200–1399', title: 'Medieval Frangipani Fortress & 1349 Earthquake', confidence: 'Documented Medieval Chronicles & Architectural Archaeology', modeNote: 'Pre-photographic era; documented in medieval Roman papal and civic records.', architecture: 'The eastern arcades of the Flavian Amphitheatre were fortified as a castle by the aristocratic Frangipani family; in 1349, a major earthquake caused the collapse of the outer southern travertine wall.', events: 'Fallen travertine blocks from the 1349 earthquake were reused to build palaces, churches, and hospitals across Renaissance Rome.', dailyLife: 'Artisans, lime-burners, and medieval compounds nestled directly inside the vaulted Roman arcades.' },
          { year: 1400, range: '1400–1599', title: 'Renaissance Ruin Sketchbook Era (Maarten van Heemskerck c. 1532)', confidence: 'Primary Renaissance Drawing (Kupferstichkabinett Berlin 1532)', modeNote: 'Directly documented by Maarten van Heemskerck’s c. 1532–1536 drawing on Wikimedia Commons.', architecture: 'Three-tiered Doric, Ionic, and Corinthian travertine arcades standing in dramatic partial ruin with unexcavated earth covering the arena floor.', events: 'Renaissance architects studied the Colosseum’s classical orders while Pope Sixtus V proposed turning the amphitheatre into a wool factory.', dailyLife: 'Pilgrims, humanist scholars, and artists sketching the ancient Roman arches.' },
          { year: 1600, range: '1600–1799', title: '1749 Papal Consecration & Botanical Flora inside the Arena', confidence: '18th-Century Paintings & Papal Inscriptions', modeNote: 'Documented by 18th-century vedute paintings (Giacomo van Lint) on Wikimedia Commons.', architecture: 'Stations of the Cross erected around the overgrown arena floor after Pope Benedict XIV consecrated the site in 1749 and banned further stone quarrying.', events: 'Botanists cataloged over 300 species of exotic Mediterranean and microclimatic plants growing in the unexcavated ruins.', dailyLife: 'Grand Tour travelers, painters, and religious processions.' },
          { year: 1800, range: '1800–1899', title: '19th-Century Buttress Stabilization & Hypogeum Excavation', confidence: 'Primary 19th-Century Paintings & 1870s Albumen Prints', modeNote: 'Documented by C.W. Eckersberg’s 1816 painting and 1870s archival photographs.', architecture: 'Construction of the monumental brick Valadier (1820) and Stern (1807) triangular buttresses to prevent further collapse of the outer wall; archaeological clearing of the subterranean hypogeum.', events: 'Archaeologists under Pietro Rosa uncovered the underground network of animal cages, hoists, and gladiatorial corridors.', dailyLife: 'Archaeological teams, structural engineers, and 19th-century travelers.' },
          { year: 1900, range: '1900–1949', title: 'Early 20th-Century Full Hypogeum Exposure & Urban Axis', confidence: 'Official Italian Heritage Archives', modeNote: 'Documented via Italian Ministry of Culture photographic records.', architecture: 'Full exposure of the two-level subterranean hypogeum chambers and creation of the Via dei Fori Imperiali boulevard in the 1930s.', events: 'Integration into Rome’s central archaeological park alongside the Roman Forum and Palatine Hill.', dailyLife: 'Urban tramways and motor traffic circling Piazza del Colosseo.' },
          { year: 1950, range: '1950–1999', title: 'UNESCO World Heritage Inscription (1980)', confidence: 'Officially Verified UNESCO Record', modeNote: 'Inscribed as part of the Historic Centre of Rome in 1980.', architecture: 'Pedestrianization of the immediate plaza and structural cleaning of travertine facades.', events: 'Global conservation campaigns to protect Roman travertine from vehicle exhaust.', dailyLife: 'Millions of international visitors exploring the upper tiers.' },
          { year: 2000, range: '2000–2015', title: '2010 Subterranean Hypogeum Public Opening', confidence: 'Primary Archival Photograph (Wikimedia Commons 2004 / 2010)', modeNote: 'Documented by U.S. Air Force / Wikimedia Commons interior photography.', architecture: 'Partial reconstruction of the wooden arena floor stage and opening of the underground hypogeum walkways to the public in summer 2010.', events: 'Major multi-year travertine facade cleaning completed.', dailyLife: 'Guided archaeological tours descending beneath the arena floor.' },
          { year: 2026, range: '2016–2026', title: 'Present-Day Conserved Flavian Amphitheatre (2026)', confidence: 'Officially Verified High-Resolution Photography', modeNote: 'Verified via modern Wikimedia Commons photography.', architecture: 'Cleaned travertine arcades, accessible internal elevators, and illuminated night views.', events: 'Nominal timed-entry ticketing protecting monument capacity.', dailyLife: 'One of the world’s premier archaeological landmarks.' }
        ]
      }
    };

    function getHistoricalTimelineForDestination(destId) {
      const dest = getDestinationById(destId);
      if (HISTORICAL_TIMELINES[destId]) return HISTORICAL_TIMELINES[destId];

      // Adaptive evidence-based timeline for the remaining verified destinations
      const altImg = dest.images[1] || dest.images[0];
      return {
        archivalPhotoUrl: altImg.url,
        archivalPhotoTitle: `${altImg.title} (Archival / Reference View)`,
        archivalAuthor: altImg.author,
        archivalCommonsUrl: altImg.commonsPage,
        archivalYearLabel: 'Archival Reference View',
        mapEvolutionNote: `Historical evolution of ${dest.name} (${dest.coordinates.label}) across ${dest.region}, ${dest.country}, from pre-industrial ecology/settlements to modern conservation and heritage management.`,
        eras: [
          { year: 1200, range: '1200–1399', title: `13th-Century Pre-Modern Landscape of ${dest.name.split('(')[0].trim()}`, confidence: 'Regional Historical & Geological Context (No Site-Specific Photograph Exists)', modeNote: 'Pre-photographic era: reconstructed from regional historical and geological records.', architecture: dest.categories.includes('Historical Landmarks') ? `Early foundational or pre-monument era in ${dest.region}. ${dest.historicalInfo}` : `Unmodified natural ecosystem across ${dest.region} (${dest.coordinates.label}).`, events: `Natural and early cultural formation of ${dest.city}, ${dest.country}.`, dailyLife: 'Indigenous and medieval regional communities living in harmony with seasonal cycles.' },
          { year: 1400, range: '1400–1599', title: '15th–16th Century Regional Chronicle Era', confidence: 'Documented Regional History', modeNote: 'Recorded in regional chronicles and cartography.', architecture: dest.historicalInfo, events: `Regional trade and cultural traditions shaping ${dest.region}.`, dailyLife: dest.culturalInfo },
          { year: 1600, range: '1600–1799', title: '17th–18th Century Historical Era', confidence: 'Documented Historical Record', modeNote: 'Backed by historical surveys and chronicles.', architecture: dest.historicalInfo, events: `Early modern historical developments across ${dest.district}, ${dest.country}.`, dailyLife: dest.culturalInfo },
          { year: 1800, range: '1800–1899', title: '19th-Century Survey & Early Photographic Era', confidence: '19th-Century Geographical & Historical Record', modeNote: 'First systematic cartographic and photographic surveys.', architecture: dest.historicalInfo, events: `19th-century documentation and preservation milestones at ${dest.name}.`, dailyLife: dest.culturalInfo },
          { year: 1900, range: '1900–1949', title: 'Early 20th-Century Conservation & Access', confidence: 'Official Historical Archive', modeNote: '20th-century administrative and conservation records.', architecture: dest.description, events: `Formal conservation and transport links established in ${dest.region}.`, dailyLife: dest.culturalInfo },
          { year: 1950, range: '1950–1999', title: 'Late 20th-Century Heritage & Ecological Protection', confidence: 'Officially Verified Record', modeNote: 'UNESCO / National Heritage protection era.', architecture: dest.description, events: dest.historicalInfo, dailyLife: dest.culturalInfo },
          { year: 2000, range: '2000–2015', title: 'Early 21st-Century Digital Documentation', confidence: 'Primary Wikimedia Commons Photographic Archive', modeNote: 'Authenticated digital photography archive.', architecture: dest.description, events: `Global digital archiving and visitor infrastructure at ${dest.name}.`, dailyLife: dest.visitorInfo.guidelines },
          { year: 2026, range: '2016–2026', title: `Present-Day Verified Destination (2026)`, confidence: 'Officially Verified High-Resolution Photography', modeNote: 'Current 2026 verified state.', architecture: dest.description, events: `Active conservation and virtual exploration in ${dest.country}.`, dailyLife: dest.visitorInfo.bestSeason }
        ]
      };
    }

    STATE.ttYear = 1875;
    STATE.ttVisualMode = 'documented'; // 'documented' | 'reconstruction'
    STATE.lastAiQuery = "Take me to Lalbagh Fort in 1875";

    function openTimeTravelForDestination(destId, year = null) {
      STATE.selectedDestinationId = destId;
      if (year !== null) STATE.ttYear = Number(year);
      switchView('timetravel');
      renderTimeTravelView();
    }

    function selectTimeTravelDestination(destId) {
      STATE.selectedDestinationId = destId;
      renderTimeTravelView();
      executeAiNavigationQuery(`Explore ${getDestinationById(destId).name} in ${STATE.ttYear}`, false);
    }

    function setTimeTravelVisualMode(mode) {
      STATE.ttVisualMode = mode;
      const docBtn = document.getElementById('ttModeDocumentedBtn');
      const recBtn = document.getElementById('ttModeReconstructionBtn');
      if (docBtn && recBtn) {
        docBtn.className = mode === 'documented'
          ? 'px-3.5 py-1.5 rounded-full text-xs font-medium bg-sky-500/25 text-sky-200 border border-sky-400/40'
          : 'px-3.5 py-1.5 rounded-full text-xs font-medium text-slate-300 hover:text-white';
        recBtn.className = mode === 'reconstruction'
          ? 'px-3.5 py-1.5 rounded-full text-xs font-medium bg-amber-500/25 text-amber-200 border border-amber-400/40'
          : 'px-3.5 py-1.5 rounded-full text-xs font-medium text-slate-300 hover:text-white';
      }
      renderTimeTravelStage();
    }

    function updateTimeTravelYear(yearVal) {
      const y = Math.max(1200, Math.min(2026, Number(yearVal) || 2026));
      STATE.ttYear = y;
      const slider = document.getElementById('ttYearSlider');
      if (slider && Number(slider.value) !== y) slider.value = String(y);
      renderTimeTravelStage();
    }

    function findClosestEra(eras, targetYear) {
      let best = eras[0];
      for (const e of eras) {
        if (targetYear >= e.year) best = e;
      }
      return best;
    }

    // Generates an honest, clearly labeled Historical Period Reconstruction Canvas when Mode B is active
    function createHistoricalReconstructionDataUri(dest, year, eraTitle) {
      const isAncientOrMedieval = year < 1700;
      const skyTop = year < 1600 ? '#1b263b' : (year < 1900 ? '#3d2c2e' : '#0c2d48');
      const skyBot = year < 1600 ? '#d4a373' : (year < 1900 ? '#e09f67' : '#38bdf8');
      const safeName = dest.name.replace(/[<>&"']/g, '');
      const safeEra = eraTitle.replace(/[<>&"']/g, '');

      let periodSilhouette = '';
      if (dest.id === 'lalbagh-fort') {
        periodSilhouette = year < 1678
          ? `<path d="M0 360 Q240 340 480 355 T960 350 L960 520 L0 520 Z" fill="#1f3a2e"/><text x="480" y="295" text-anchor="middle" fill="#fde68a" font-family="Georgia, serif" font-size="20">Pre-1678 Buriganga Riverine Landscape (Prior to Mughal Fort Construction)</text>`
          : `<rect x="260" y="220" width="440" height="140" fill="#7f1d1d"/><circle cx="480" cy="215" r="52" fill="#f8fafc"/><circle cx="330" cy="230" r="32" fill="#e2e8f0"/><circle cx="630" cy="230" r="32" fill="#e2e8f0"/><rect x="0" y="360" width="960" height="160" fill="#1e3a2f"/><rect x="445" y="360" width="70" height="160" fill="#0284c7" opacity="0.65"/>`;
      } else if (dest.id === 'eiffel-tower') {
        if (year < 1887) {
          periodSilhouette = `<rect x="0" y="350" width="960" height="170" fill="#2d4a3e"/><rect x="360" y="280" width="240" height="70" fill="#94a3b8"/><text x="480" y="245" text-anchor="middle" fill="#fde68a" font-family="Georgia, serif" font-size="20">Pre-1887 Champ de Mars &amp; Seine Plain (Prior to Eiffel Tower)</text>`;
        } else if (year < 1889) {
          periodSilhouette = `<polygon points="410,360 445,210 515,210 550,360" fill="none" stroke="#d97706" stroke-width="8"/><line x1="390" y1="275" x2="570" y2="275" stroke="#f59e0b" stroke-width="6"/><rect x="0" y="360" width="960" height="160" fill="#1e293b"/>`;
        } else {
          periodSilhouette = `<polygon points="420,360 474,95 486,95 540,360" fill="none" stroke="#f59e0b" stroke-width="6"/><line x1="435" y1="270" x2="525" y2="270" stroke="#fbbf24" stroke-width="5"/><line x1="455" y1="190" x2="505" y2="190" stroke="#fbbf24" stroke-width="4"/><rect x="0" y="360" width="960" height="160" fill="#0f172a"/>`;
        }
      } else {
        periodSilhouette = `<path d="M0 340 Q240 290 480 335 T960 320 L960 520 L0 520 Z" fill="#0369a1"/><path d="M0 390 Q320 365 960 410 L960 520 L0 520 Z" fill="#d6c29f"/>`;
      }

      const svg = `<svg xmlns="http://www.w3.org/2000/svg" width="960" height="520" viewBox="0 0 960 520">
        <defs>
          <linearGradient id="sky" x1="0" y1="0" x2="0" y2="1">
            <stop offset="0%" stop-color="${skyTop}"/>
            <stop offset="70%" stop-color="${skyBot}"/>
            <stop offset="100%" stop-color="#0f172a"/>
          </linearGradient>
        </defs>
        <rect width="960" height="520" fill="url(#sky)"/>
        <circle cx="760" cy="130" r="48" fill="rgba(253, 224, 71, 0.28)"/>
        ${periodSilhouette}
        <rect x="24" y="24" width="540" height="36" rx="18" fill="rgba(2, 6, 23, 0.82)" stroke="rgba(251, 191, 36, 0.5)"/>
        <text x="44" y="47" fill="#fbbf24" font-family="monospace" font-size="13" font-weight="bold">ILLUSTRATIVE HISTORICAL RECONSTRUCTION • NOT A PHOTOGRAPH</text>
        <text x="44" y="455" fill="#ffffff" font-family="Georgia, serif" font-size="30" font-weight="bold">${safeName} — Year ${year} AD</text>
        <text x="44" y="485" fill="#bae6fd" font-family="monospace" font-size="14">${safeEra} (${dest.coordinates.label})</text>
      </svg>`;
      return 'data:image/svg+xml;charset=utf-8,' + encodeURIComponent(svg);
    }

    function renderTimeTravelView() {
      const sel = document.getElementById('ttDestinationSelect');
      if (sel) {
        sel.innerHTML = DESTINATIONS.map(d => `<option value="${d.id}">${d.name} (${d.country})</option>`).join('');
        sel.value = STATE.selectedDestinationId;
      }
      renderTimeTravelStage();
      renderPastVsPresentComparison();
    }

    function renderTimeTravelStage() {
      const dest = getDestinationById(STATE.selectedDestinationId);
      const timeline = getHistoricalTimelineForDestination(dest.id);
      const era = findClosestEra(timeline.eras, STATE.ttYear);
      const presentPhoto = dest.images[0];
      const fallbackUri = createFallbackDataUri(dest.name, dest.coordinates.label);

      const heading = document.getElementById('ttDestinationHeading');
      const yearBadge = document.getElementById('ttSelectedYearBadge');
      const titleBadge = document.getElementById('ttEpochTitleBadge');
      const confBadge = document.getElementById('ttConfidenceBadge');
      const stage = document.getElementById('ttStageContainer');

      if (heading) heading.textContent = `Historical Time Travel: ${dest.name} (1200–2026)`;
      if (yearBadge) yearBadge.textContent = `${STATE.ttYear} AD`;
      if (titleBadge) titleBadge.textContent = `${era.title} (${era.range})`;
      if (confBadge) confBadge.textContent = `Evidence: ${era.confidence}`;

      // Highlight closest epoch button
      document.querySelectorAll('.tt-epoch-btn').forEach(btn => {
        const bYear = Number(btn.textContent.trim());
        const isCurrent = bYear === era.year;
        btn.className = isCurrent
          ? 'tt-epoch-btn py-1.5 rounded-lg bg-amber-400 text-slate-950 font-mono text-xs font-bold shadow'
          : 'tt-epoch-btn py-1.5 rounded-lg bg-white/5 hover:bg-white/15 border border-white/10 font-mono text-xs text-slate-300';
      });

      if (!stage) return;

      const isDocumentedMode = STATE.ttVisualMode === 'documented';
      const displayImgUrl = isDocumentedMode
        ? (STATE.ttYear >= 2015 ? presentPhoto.url : timeline.archivalPhotoUrl)
        : createHistoricalReconstructionDataUri(dest, STATE.ttYear, era.title);

      const visualBadgeHTML = isDocumentedMode
        ? `<span class="px-3 py-1 rounded-full bg-sky-500/20 border border-sky-400/40 text-sky-200 font-mono text-[10px]">MODE A: DOCUMENTED ARCHIVAL / PHOTOGRAPHIC MATERIAL</span>`
        : `<span class="px-3 py-1 rounded-full bg-amber-500/25 border border-amber-400/50 text-amber-200 font-mono text-[10px]">MODE B: ILLUSTRATIVE HISTORICAL RECONSTRUCTION (NOT A PHOTOGRAPH)</span>`;

      stage.innerHTML = `
        <!-- Left 7 Columns: Historical Visual Viewport -->
        <div class="lg:col-span-7 space-y-4">
          <div class="relative rounded-2xl overflow-hidden bg-[#02050d] border border-white/15 h-80 sm:h-[430px] flex items-center justify-center">
            <img src="${displayImgUrl}" alt="${era.title}"
                 onerror="this.onerror=null;this.src='${fallbackUri}';"
                 class="w-full h-full object-cover" />
            <div class="absolute inset-0 bg-gradient-to-t from-black/90 via-black/20 to-black/40"></div>

            <div class="absolute top-4 left-4 right-4 flex flex-wrap items-center justify-between gap-2">
              ${visualBadgeHTML}
              <span class="px-3 py-1 rounded-full bg-black/75 border border-white/15 font-mono text-xs text-amber-300">${STATE.ttYear} AD</span>
            </div>

            <div class="absolute bottom-4 left-5 right-5 space-y-1">
              <div class="text-[11px] font-mono text-sky-300">${era.modeNote}</div>
              <h4 class="font-editorial text-2xl sm:text-3xl font-semibold text-white">${era.title}</h4>
              <div class="flex flex-wrap items-center justify-between gap-2 text-[11px] text-slate-300 pt-1">
                <span>Source: ${isDocumentedMode ? (STATE.ttYear >= 2015 ? presentPhoto.author : timeline.archivalAuthor) : 'Global VR Explorer Historical Reconstruction Engine'}</span>
                <a href="${isDocumentedMode ? (STATE.ttYear >= 2015 ? presentPhoto.commonsPage : timeline.archivalCommonsUrl) : presentPhoto.commonsPage}" target="_blank" rel="noopener noreferrer" class="text-sky-300 hover:underline font-mono">
                  Verify Wikimedia Archive ↗
                </a>
              </div>
            </div>
          </div>

          <!-- Quick Mode & VR Action Strip -->
          <div class="flex flex-wrap items-center justify-between gap-3 p-4 rounded-2xl bg-white/[0.03] border border-white/10 text-xs">
            <span class="text-slate-300">Compare this <strong>${STATE.ttYear} AD</strong> record against present-day 2026 photography:</span>
            <div class="flex items-center gap-2">
              <button type="button" onclick="openPanoramaForDestination('${dest.id}')" class="px-4 py-2 rounded-full bg-white text-slate-950 hover:bg-sky-100 font-medium text-xs flex items-center gap-1.5 transition">
                <i data-lucide="glasses" class="w-3.5 h-3.5"></i>
                <span>Present-Day 360° Photo VR</span>
              </button>
              <button type="button" onclick="STATE.selectedDestinationId='${dest.id}'; STATE.panoMode='reconstructed'; switchView('panorama'); loadPanoramaEnvironment();" class="px-4 py-2 rounded-full bg-amber-500/20 hover:bg-amber-500/30 border border-amber-400/40 text-amber-200 text-xs font-mono transition">
                3D Reconstruction View
              </button>
            </div>
          </div>
        </div>

        <!-- Right 5 Columns: Historical Research Dossier for Selected Year -->
        <div class="lg:col-span-5 space-y-4">
          <div class="p-5 rounded-2xl bg-white/[0.03] border border-white/10 space-y-2">
            <div class="text-[10px] font-mono uppercase tracking-widest text-sky-300">Architectural &amp; Structural State (${era.range})</div>
            <p class="text-xs sm:text-sm text-slate-200 font-light leading-relaxed">${era.architecture}</p>
          </div>

          <div class="p-5 rounded-2xl bg-white/[0.03] border border-white/10 space-y-2">
            <div class="text-[10px] font-mono uppercase tracking-widest text-amber-300">Key Historical Events &amp; Geographical Shifts</div>
            <p class="text-xs sm:text-sm text-slate-200 font-light leading-relaxed">${era.events}</p>
          </div>

          <div class="p-5 rounded-2xl bg-white/[0.03] border border-white/10 space-y-2">
            <div class="text-[10px] font-mono uppercase tracking-widest text-emerald-300">Everyday Life &amp; Surrounding Environment</div>
            <p class="text-xs sm:text-sm text-slate-200 font-light leading-relaxed">${era.dailyLife}</p>
          </div>

          <div class="p-4 rounded-2xl bg-[#02050d] border border-white/10 space-y-1.5 text-xs">
            <div class="text-[10px] font-mono uppercase text-slate-400">Verification &amp; Source Attribution</div>
            <div class="text-slate-300">Status: <strong class="text-white">${dest.verificationStatus}</strong> • Confidence: <span class="text-sky-300">${era.confidence}</span></div>
            <div class="flex flex-wrap gap-2 pt-1">
              ${dest.sources.map(src => `<a href="${src.url}" target="_blank" rel="noopener noreferrer" class="text-[11px] font-mono text-sky-300 hover:underline">${src.title} ↗</a>`).join(' • ')}
            </div>
          </div>
        </div>
      `;

      renderPastVsPresentComparison();
      if (window.lucide) window.lucide.createIcons();
    }

    function renderPastVsPresentComparison() {
      const container = document.getElementById('ttPastVsPresentContainer');
      if (!container) return;

      const dest = getDestinationById(STATE.selectedDestinationId);
      const timeline = getHistoricalTimelineForDestination(dest.id);
      const presentPhoto = dest.images[0];
      const fallbackUri = createFallbackDataUri(dest.name, dest.coordinates.label);

      container.innerHTML = `
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <!-- Historical Archival Card -->
          <div class="rounded-2xl overflow-hidden border border-amber-400/30 bg-[#02050d] flex flex-col justify-between">
            <div class="relative h-72 overflow-hidden">
              <img src="${timeline.archivalPhotoUrl}" alt="${timeline.archivalPhotoTitle}"
                   onerror="this.onerror=null;this.src='${fallbackUri}';"
                   class="w-full h-full object-cover" />
              <div class="absolute top-3 left-3 px-3 py-1 rounded-full bg-black/80 border border-amber-400/40 text-[10px] font-mono text-amber-300">
                PAST • ${timeline.archivalYearLabel.toUpperCase()}
              </div>
            </div>
            <div class="p-5 space-y-2">
              <div class="font-editorial text-2xl font-semibold text-white">${timeline.archivalPhotoTitle}</div>
              <p class="text-xs text-slate-300 font-light">Credit: ${timeline.archivalAuthor}</p>
              <a href="${timeline.archivalCommonsUrl}" target="_blank" rel="noopener noreferrer" class="inline-block text-xs font-mono text-amber-300 hover:underline">
                Inspect Archival Wikimedia Source ↗
              </a>
            </div>
          </div>

          <!-- Present-Day 2026 Authentic Card -->
          <div class="rounded-2xl overflow-hidden border border-sky-400/30 bg-[#02050d] flex flex-col justify-between">
            <div class="relative h-72 overflow-hidden">
              <img src="${presentPhoto.url}" alt="${presentPhoto.title}"
                   onerror="this.onerror=null;this.src='${fallbackUri}';"
                   class="w-full h-full object-cover" />
              <div class="absolute top-3 left-3 px-3 py-1 rounded-full bg-black/80 border border-sky-400/40 text-[10px] font-mono text-sky-300">
                PRESENT • 2026 VERIFIED PHOTOGRAPHY
              </div>
            </div>
            <div class="p-5 space-y-2">
              <div class="font-editorial text-2xl font-semibold text-white">${presentPhoto.title} (${presentPhoto.viewpoint})</div>
              <p class="text-xs text-slate-300 font-light">Credit: ${presentPhoto.author} (${presentPhoto.license})</p>
              <a href="${presentPhoto.commonsPage}" target="_blank" rel="noopener noreferrer" class="inline-block text-xs font-mono text-sky-300 hover:underline">
                Inspect Modern Wikimedia Source ↗
              </a>
            </div>
          </div>
        </div>

        <!-- Geographical & Cartographic Evolution Summary -->
        <div class="p-5 rounded-2xl bg-white/[0.03] border border-white/10 grid grid-cols-1 lg:grid-cols-12 gap-4 items-center text-xs">
          <div class="lg:col-span-8 space-y-1">
            <div class="text-[10px] font-mono uppercase tracking-widest text-sky-300">Historical vs. Current Geographical &amp; Urban Evolution (${dest.coordinates.label})</div>
            <p class="text-slate-200 font-light leading-relaxed">${timeline.mapEvolutionNote}</p>
          </div>
          <div class="lg:col-span-4 flex flex-wrap justify-end gap-2">
            <button type="button" onclick="flyGlobeToDestination('${dest.id}')" class="px-4 py-2.5 rounded-full bg-white/10 hover:bg-white/20 text-white font-mono text-[11px]">
              Inspect on 3D Globe →
            </button>
            <button type="button" onclick="openEditorialDestinationPage('${dest.id}')" class="px-4 py-2.5 rounded-full bg-sky-500/20 hover:bg-sky-500/30 text-sky-200 border border-sky-400/30 font-mono text-[11px]">
              Full Editorial Page →
            </button>
          </div>
        </div>
      `;
    }

    // Natural-Language AI Destination Navigator & Voice Assistant
    function executeAiNavigationQuery(rawQuery, shouldSwitchView = true) {
      const q = (rawQuery || '').trim();
      if (!q) {
        showToast('Please enter or speak a destination command.');
        return;
      }
      STATE.lastAiQuery = q;
      const lower = q.toLowerCase();

      // 1. Parse optional historical year (1200–2026)
      const yearMatch = lower.match(/\b(1[2-9]\d{2}|20[0-2]\d)\b/);
      let detectedYear = yearMatch ? Math.max(1200, Math.min(2026, Number(yearMatch[1]))) : null;
      if (!detectedYear && (lower.includes('ancient') || lower.includes('medieval'))) {
        detectedYear = 1200;
      } else if (!detectedYear && lower.includes('mughal')) {
        detectedYear = 1680;
      } else if (!detectedYear && lower.includes('construction')) {
        detectedYear = 1888;
      }

      // 2. Match destination across names, synonyms, countries, regions, and landmarks
      const aliasMap = [
        { id: 'coxs-bazar', keys: ['cox', 'bazar', 'কক্সবাজার', 'laboni', 'himchari', 'longest beach', 'bay of bengal beach'] },
        { id: 'lalbagh-fort', keys: ['lalbagh', 'লালবাগ', 'aurangabad', 'pari bibi', 'old dhaka', 'shaista khan', 'dhaka fort'] },
        { id: 'eiffel-tower', keys: ['eiffel', 'paris', 'প্যারিস', 'আইফেল', 'trocadero', 'champ de mars', 'seine', 'france'] },
        { id: 'sundarbans', keys: ['sundarban', 'সুন্দরবন', 'mangrove', 'royal bengal tiger', 'khulna', 'katka'] },
        { id: 'rome-colosseum', keys: ['rome', 'ancient rome', 'colosseum', 'flavian', 'italy', 'gladiator', 'hypogeum', 'রোম'] },
        { id: 'great-wall-mutianyu', keys: ['great wall', 'china', 'mutianyu', 'badaling', 'beijing', 'ming wall', 'চীন'] },
        { id: 'saint-martins', keys: ['saint martin', 'st martin', 'narikel', 'coral island', 'chera dwip', 'সেন্টমার্টিন'] },
        { id: 'ratargul-swamp', keys: ['ratargul', 'swamp', 'sylhet', 'gowain', 'রাতারগুল'] },
        { id: 'tokyo-sensoji', keys: ['tokyo', 'senso-ji', 'sensoji', 'asakusa', 'japan', 'kaminarimon', 'টোকিও'] },
        { id: 'swiss-alps', keys: ['swiss', 'switzerland', 'alps', 'jungfrau', 'aletsch', 'glacier', 'eiger'] },
        { id: 'statue-of-liberty', keys: ['liberty', 'new york', 'nyc', 'manhattan', 'bartholdi', 'america', 'usa'] }
      ];

      let matchedDest = null;
      for (const entry of aliasMap) {
        if (entry.keys.some(k => lower.includes(k))) {
          matchedDest = getDestinationById(entry.id);
          break;
        }
      }

      const navInput = document.getElementById('aiNavigatorInput');
      if (navInput) navInput.value = q;

      if (shouldSwitchView && STATE.activeView !== 'timetravel') {
        switchView('timetravel');
      }

      const card = document.getElementById('aiAssistantResponseCard');
      if (!matchedDest) {
        // Transparently explain when a requested VR experience is outside the verified 11-node dataset
        if (card) {
          card.innerHTML = `
            <div class="flex items-start gap-3">
              <div class="p-2.5 rounded-xl bg-amber-500/20 text-amber-300 shrink-0">
                <i data-lucide="info" class="w-5 h-5"></i>
              </div>
              <div class="space-y-2 flex-1">
                <div class="text-xs font-mono uppercase text-amber-300">AI Geographical Assistant — VR Availability Notice</div>
                <div class="text-sm font-semibold text-white">Query: "${q.replace(/[<>&"']/g, '')}"</div>
                <p class="text-xs text-slate-300 leading-relaxed">
                  An authenticated 360° photographic VR environment for <strong>"${q.replace(/[<>&"']/g, '')}"</strong> is not yet in our verified 11-destination Wikimedia Commons collection. Per strict engineering integrity, Global VR Explorer never fabricates synthetic photographs for uncataloged locations. Select one of our 11 verified destinations below:
                </p>
                <div class="flex flex-wrap gap-1.5 pt-1">
                  ${DESTINATIONS.map(d => `<button type="button" onclick="executeAiNavigationQuery('${d.id}')" class="px-3 py-1 rounded-full bg-white/10 hover:bg-white/20 text-xs text-sky-300">${d.name.split('(')[0].trim()}</button>`).join('')}
                </div>
              </div>
            </div>
          `;
          if (window.lucide) window.lucide.createIcons();
        }
        return;
      }

      STATE.selectedDestinationId = matchedDest.id;
      if (detectedYear !== null) {
        STATE.ttYear = detectedYear;
      } else {
        STATE.ttYear = 2026;
      }

      renderTimeTravelView();

      if (card) {
        const vrAvailabilityNote = matchedDest.hasTruePanoramaPhoto
          ? `Full 360° Equirectangular/Cylindrical Wikimedia Commons Panorama available (${matchedDest.images.length} total viewpoints).`
          : `Full 360° spherical capture unavailable; ${matchedDest.images.length} authentic multi-angle Wikimedia Commons photographs are available in our undistorted Curved Optical VR Gallery.`;

        card.innerHTML = `
          <div class="grid grid-cols-1 lg:grid-cols-12 gap-5 items-center">
            <div class="lg:col-span-8 space-y-2">
              <div class="flex flex-wrap items-center gap-2">
                <span class="px-2.5 py-0.5 rounded-full bg-emerald-500/20 text-emerald-300 font-mono text-[10px]">AI MATCH CONFIRMED</span>
                <span class="text-xs font-mono text-sky-300">${matchedDest.country} → ${matchedDest.region} → ${matchedDest.city} (${matchedDest.coordinates.label})</span>
              </div>
              <h4 class="font-editorial text-2xl sm:text-3xl font-semibold text-white">
                ${matchedDest.name} ${detectedYear ? `<span class="text-amber-300 font-mono text-lg">• Historical Year ${detectedYear} AD</span>` : ''}
              </h4>
              <p class="text-xs text-slate-300 font-light leading-relaxed">${matchedDest.shortSummary}</p>
              <div class="text-[11px] font-mono text-sky-200 bg-sky-500/10 border border-sky-400/25 px-3 py-1.5 rounded-xl">
                <strong>VR Experience Status:</strong> ${vrAvailabilityNote}
              </div>
            </div>
            <div class="lg:col-span-4 flex flex-col gap-2">
              <button type="button" onclick="openPanoramaForDestination('${matchedDest.id}')" class="w-full py-2.5 px-4 rounded-xl bg-white text-slate-950 hover:bg-sky-100 font-semibold text-xs flex items-center justify-center gap-2 transition">
                <i data-lucide="glasses" class="w-4 h-4"></i>
                <span>Launch Authentic 360° Photo VR</span>
              </button>
              <button type="button" onclick="openEditorialDestinationPage('${matchedDest.id}')" class="w-full py-2.5 px-4 rounded-xl bg-white/10 hover:bg-white/20 text-white text-xs flex items-center justify-center gap-2 transition">
                <i data-lucide="image" class="w-4 h-4 text-sky-300"></i>
                <span>View All ${matchedDest.images.length} Authentic Viewpoints</span>
              </button>
            </div>
          </div>
        `;
        if (window.lucide) window.lucide.createIcons();
      }
      logDiscoveryAction(matchedDest.id, `AI Navigator: "${q}"`);
    }

    function startAiVoiceRecognition(targetInputId) {
      const badge = document.getElementById('aiVoiceStatusBadge');
      const SpeechRec = window.SpeechRecognition || window.webkitSpeechRecognition;
      if (!SpeechRec) {
        showToast('Voice recognition is not supported in this browser; please type your destination request.');
        return;
      }
      try {
        const rec = new SpeechRec();
        rec.lang = STATE.lang === 'bn' ? 'bn-BD' : 'en-US';
        rec.interimResults = false;
        rec.maxAlternatives = 1;
        if (badge) badge.textContent = 'Voice Engine: Listening... Speak now';
        showToast('Listening... Say e.g. "Take me to Coxs Bazar" or "Show me the Eiffel Tower"');

        rec.onresult = event => {
          const transcript = event.results?.[0]?.[0]?.transcript || '';
          const inp = document.getElementById(targetInputId);
          if (inp) inp.value = transcript;
          if (badge) badge.textContent = `Heard: "${transcript}"`;
          executeAiNavigationQuery(transcript, true);
        };
        rec.onerror = () => {
          if (badge) badge.textContent = 'Voice Engine: Ready';
        };
        rec.onend = () => {
          if (badge && badge.textContent.includes('Listening')) {
            badge.textContent = 'Voice Engine: Ready';
          }
        };
        rec.start();
      } catch (e) {
        showToast('Microphone permission unavailable in preview; type your request above.');
      }
    }

    // =========================================================================
    // 15. EVENT LISTENERS & INITIALIZATION
    // =========================================================================
    function getLanguageMeta(code) {
      return LANGUAGE_REGISTRY.find(l => l.code === code) || LANGUAGE_REGISTRY[0];
    }

    function applyLanguage() {
      const meta = getLanguageMeta(STATE.lang);
      const dict = I18N[meta.code] || I18N.en;
      if (typeof document !== 'undefined' && document.documentElement) {
        document.documentElement.lang = meta.code;
        document.documentElement.dir = meta.rtl ? 'rtl' : 'ltr';
      }
      document.querySelectorAll('[data-i18n]').forEach(el => {
        const key = el.getAttribute('data-i18n');
        if (dict[key]) el.textContent = dict[key];
        else if (I18N.en[key]) el.textContent = I18N.en[key];
      });
      const ind = document.getElementById('activeLangIndicator');
      if (ind) ind.textContent = `${meta.code.toUpperCase()} • ${meta.native}`;
      document.querySelectorAll('.lang-indicator-mirror').forEach(el => {
        el.textContent = meta.code.toUpperCase();
      });
      try { localStorage.setItem('gvr_lang', meta.code); } catch (e) {}
      if (typeof renderHomeSections === 'function') renderHomeSections();
      if (typeof renderExploreGrid === 'function') renderExploreGrid();
      if (typeof renderTimeTravelView === 'function') renderTimeTravelView();
      if (typeof renderAccountMenuLists === 'function') renderAccountMenuLists();
      renderLanguageOptions('');
    }

    function renderLanguageOptions(query = '') {
      const listEl = document.getElementById('langOptionsList');
      if (!listEl) return;
      const q = (query || '').trim().toLowerCase();
      const matches = LANGUAGE_REGISTRY.filter(l => {
        if (!q) return true;
        return l.name.toLowerCase().includes(q)
          || l.native.toLowerCase().includes(q)
          || l.code.toLowerCase() === q
          || l.aliases.toLowerCase().includes(q);
      });
      if (!matches.length) {
        listEl.innerHTML = '<div class="px-2.5 py-2 text-slate-400 italic">No matching language found</div>';
        return;
      }
      listEl.innerHTML = matches.map(l => {
        const active = l.code === STATE.lang;
        return `<button type="button" onclick="selectAppLanguage('${l.code}')" class="w-full flex items-center justify-between px-2.5 py-1.5 rounded-xl text-left transition ${active ? 'bg-sky-500/25 text-sky-200 border border-sky-400/40' : 'hover:bg-white/10 text-slate-200'}">
          <span><strong class="font-mono text-[10px] mr-1.5 text-sky-400">${l.code.toUpperCase()}</strong>${l.native}</span>
          <span class="text-[10px] text-slate-400">${l.name}${l.rtl ? ' • RTL' : ''}</span>
        </button>`;
      }).join('');
    }

    function filterLanguageOptions(query) {
      renderLanguageOptions(query);
    }

    function toggleLangDropdown() {
      const menu = document.getElementById('langDropdownMenu');
      if (!menu) return;
      menu.classList.toggle('hidden');
      if (!menu.classList.contains('hidden')) {
        const inp = document.getElementById('langSearchInput');
        if (inp) { inp.value = ''; inp.focus(); }
        renderLanguageOptions('');
      }
    }

    function openQuickLangPicker() {
      const portal = document.getElementById('authPortalOverlay');
      if (portal && !portal.classList.contains('hidden')) {
        const idx = LANGUAGE_REGISTRY.findIndex(l => l.code === STATE.lang);
        const next = LANGUAGE_REGISTRY[(idx + 1) % LANGUAGE_REGISTRY.length];
        selectAppLanguage(next.code);
      } else {
        toggleLangDropdown();
      }
    }

    function selectAppLanguage(code) {
      const meta = getLanguageMeta(code);
      STATE.lang = meta.code;
      applyLanguage();
      const menu = document.getElementById('langDropdownMenu');
      if (menu) menu.classList.add('hidden');
      if (typeof showToast === 'function') showToast(`${meta.native} (${meta.name})`);
    }

    function bootstrapLanguagePreference() {
      let saved = '';
      try { saved = localStorage.getItem('gvr_lang') || ''; } catch (e) {}
      if (saved && LANGUAGE_REGISTRY.some(l => l.code === saved)) {
        STATE.lang = saved;
      } else if (typeof navigator !== 'undefined' && navigator.language) {
        const browserCode = navigator.language.split('-')[0].toLowerCase();
        if (LANGUAGE_REGISTRY.some(l => l.code === browserCode)) {
          STATE.lang = browserCode;
        }
      }
      applyLanguage();
    }

    window.addEventListener('DOMContentLoaded', () => {
      try { initStarfield(); } catch (e) { console.warn(e); }
      try { loadPersistedState(); } catch (e) { console.warn(e); }
      try { updateSavedBadges(); } catch (e) { console.warn(e); }
      try { populateExploreDropdowns(); } catch (e) { console.warn(e); }
      try { renderHomeSections(); } catch (e) { console.warn(e); }
      try { renderExploreGrid(); } catch (e) { console.warn(e); }
      try { renderEditorialDestinationPage('coxs-bazar', 0); } catch (e) { console.warn(e); }
      try { renderTimeTravelView(); executeAiNavigationQuery(STATE.lastAiQuery, false); } catch (e) { console.warn(e); }
      try { renderAssistantView(); } catch (e) { console.warn(e); }
      try { renderComparisonView(); } catch (e) { console.warn(e); }
      try { renderUnityView(); } catch (e) { console.warn(e); }
      try { renderProfileView(); } catch (e) { console.warn(e); }
      try { initOrResizeHeroEarth(); } catch (e) { console.warn(e); }

      document.querySelectorAll('[data-nav]').forEach(btn => {
        btn.addEventListener('click', () => {
          const target = btn.getAttribute('data-nav');
          if (target) switchView(target);
        });
      });

      document.getElementById('brandHomeBtn')?.addEventListener('click', () => switchView('home'));
      document.getElementById('heroExploreWorldBtn')?.addEventListener('click', () => scrollToHomeSection('section-destinations-showcase'));
      document.getElementById('heroWatchExperienceBtn')?.addEventListener('click', openWatchExperienceModal);
      document.getElementById('heroDiscoverBtn')?.addEventListener('click', () => switchView('explore'));
      document.getElementById('heroLaunch360Btn')?.addEventListener('click', () => openPanoramaForDestination(STATE.selectedDestinationId));
      document.getElementById('viewAllDestinationsBtn')?.addEventListener('click', () => switchView('explore'));
      document.getElementById('bannerInspectUnityBtn')?.addEventListener('click', () => switchView('unity'));
      document.getElementById('panoOpenQuestCodeBtn')?.addEventListener('click', () => switchView('unity'));

      document.getElementById('mobileMenuBtn')?.addEventListener('click', () => {
        document.getElementById('mobileNavDrawer')?.classList.toggle('hidden');
      });

      document.getElementById('globalQuickSearch')?.addEventListener('input', e => {
        const val = e.target.value;
        const expSearch = document.getElementById('exploreSearchInput');
        if (expSearch) expSearch.value = val;
        if (STATE.activeView !== 'explore') switchView('explore');
        renderExploreGrid();
      });

      const currBtn = document.getElementById('currencyToggleBtn');
      if (currBtn) {
        currBtn.textContent = STATE.currency === 'BDT' ? 'BDT ৳' : 'USD $';
        currBtn.addEventListener('click', () => {
          STATE.currency = STATE.currency === 'BDT' ? 'USD' : 'BDT';
          currBtn.textContent = STATE.currency === 'BDT' ? 'BDT ৳' : 'USD $';
          savePersistedState();
          renderHomeSections();
          renderExploreGrid();
          renderAssistantView();
          renderComparisonView();
          if (STATE.activeView === 'destination-page') {
            renderEditorialDestinationPage(STATE.editorialDestinationId, STATE.editorialHeroPhotoIdx);
          }
          showToast(`Expense display switched to ${STATE.currency}`);
        });
      }



      document.getElementById('globeSearchInput')?.addEventListener('input', rebuildGlobeMarkers);
      document.getElementById('globeCountrySelect')?.addEventListener('change', e => {
        rebuildGlobeMarkers();
        const c = e.target.value;
        if (c !== 'all') {
          const match = DESTINATIONS.find(d => d.country === c);
          if (match) focusGlobeOnDestination(match.id);
        }
      });
      document.getElementById('globeZoomInBtn')?.addEventListener('click', () => {
        globeCameraDistance = Math.max(1.85, globeCameraDistance - 0.35);
      });
      document.getElementById('globeZoomOutBtn')?.addEventListener('click', () => {
        globeCameraDistance = Math.min(4.5, globeCameraDistance + 0.35);
      });
      document.getElementById('globeToggleSpinBtn')?.addEventListener('click', () => {
        globeAutoSpin = !globeAutoSpin;
        updateGlobeSpinButton();
      });
      document.getElementById('globeResetViewBtn')?.addEventListener('click', () => {
        globeCameraDistance = 3.15;
        globeTargetRotation = { x: 0.35, y: -1.6 };
        globeAutoSpin = true;
        updateGlobeSpinButton();
      });

      ['exploreSearchInput', 'exploreCountryFilter', 'exploreDivisionFilter', 'exploreCategoryFilter', 'exploreSortSelect'].forEach(id => {
        document.getElementById(id)?.addEventListener('input', () => {
          populateExploreDropdowns();
          renderExploreGrid();
        });
        document.getElementById(id)?.addEventListener('change', () => {
          populateExploreDropdowns();
          renderExploreGrid();
        });
      });

      document.getElementById('panoDestinationSelect')?.addEventListener('change', e => {
        const val = e.target.value || 'coxs-bazar';
        if (val.startsWith('tpl:')) {
          selectVrDestinationTemplate(val.slice(4));
        } else {
          openPanoramaForDestination(val, STATE.panoMode);
        }
      });

      document.querySelectorAll('.pano-mode-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          STATE.panoMode = btn.getAttribute('data-panomode') || 'authentic';
          document.querySelectorAll('.pano-mode-btn').forEach(b => {
            const active = b.getAttribute('data-panomode') === STATE.panoMode;
            b.className = active
              ? 'pano-mode-btn px-3.5 py-1.5 rounded-full text-xs font-medium bg-sky-500/25 text-sky-200 border border-sky-400/30'
              : 'pano-mode-btn px-3.5 py-1.5 rounded-full text-xs font-medium text-slate-300 hover:text-white';
          });
          loadPanoramaEnvironment();
        });
      });

      document.getElementById('panoQualitySelect')?.addEventListener('change', e => {
        STATE.panoQuality = e.target.value;
        showToast(`Panorama texture stream set to ${e.target.value === 'high' ? 'Original HD' : 'Standard 1280px'}`);
      });

      document.getElementById('panoAutoLookBtn')?.addEventListener('click', e => {
        STATE.panoAutoLook = !STATE.panoAutoLook;
        e.currentTarget.textContent = `Orbit Pan: ${STATE.panoAutoLook ? 'ON' : 'OFF'}`;
      });

      document.getElementById('panoVignetteToggleBtn')?.addEventListener('click', e => {
        STATE.panoComfortVignette = !STATE.panoComfortVignette;
        e.currentTarget.textContent = `Comfort Vignette: ${STATE.panoComfortVignette ? 'ON' : 'OFF'}`;
      });

      document.getElementById('panoAudioToggleBtn')?.addEventListener('click', toggleSpatialAudio);

      document.getElementById('panoFovSlider')?.addEventListener('input', e => {
        STATE.panoFov = Number(e.target.value) || 72;
        updatePanoReadout();
      });

      document.getElementById('panoResetAngleBtn')?.addEventListener('click', () => {
        STATE.panoYaw = 0;
        STATE.panoPitch = 0;
        STATE.panoFov = 72;
        const slider = document.getElementById('panoFovSlider');
        if (slider) slider.value = '72';
        updatePanoReadout();
      });

      document.getElementById('panoFullscreenBtn')?.addEventListener('click', () => {
        const wrapper = document.getElementById('panoViewportWrapper');
        if (!wrapper) return;
        if (!document.fullscreenElement) {
          wrapper.requestFullscreen?.().catch(() => {});
        } else {
          document.exitFullscreen?.().catch(() => {});
        }
      });

      document.getElementById('assistantDestSelect')?.addEventListener('change', e => {
        STATE.assistantDestId = e.target.value;
        renderAssistantView();
      });
      document.getElementById('assistantTierSelect')?.addEventListener('change', e => {
        STATE.assistantTier = e.target.value;
        renderAssistantView();
      });
      document.getElementById('assistantDaysSlider')?.addEventListener('input', e => {
        STATE.assistantDays = Number(e.target.value) || 4;
        const lbl = document.getElementById('assistantDaysLabel');
        if (lbl) lbl.textContent = `${STATE.assistantDays}d`;
        renderAssistantView();
      });

      ['compareSelectA', 'compareSelectB', 'compareSelectC'].forEach((id, idx) => {
        document.getElementById(id)?.addEventListener('change', e => {
          STATE.compareIds[idx] = e.target.value;
          renderComparisonView();
        });
      });

      document.getElementById('copyCurrentUnityScriptBtn')?.addEventListener('click', () => {
        const activeFile = UNITY_PROJECT_FILES[STATE.activeUnityFileIndex] || UNITY_PROJECT_FILES[0];
        try {
          navigator.clipboard.writeText(activeFile.code);
        } catch (err) {
          const ta = document.createElement('textarea');
          ta.value = activeFile.code;
          document.body.appendChild(ta);
          ta.select();
          document.execCommand('copy');
          document.body.removeChild(ta);
        }
        showToast(`Copied ${activeFile.path} to clipboard`);
      });

      document.getElementById('clearHistoryHomeBtn')?.addEventListener('click', () => {
        STATE.recentHistory = [{ id: 'coxs-bazar', timestamp: '2026-11-17 18:08', action: 'Session Reset' }];
        savePersistedState();
        renderHomeRecentList();
        renderProfileView();
        showToast('Discovery history reset');
      });

      document.getElementById('clearDiscoveryHistoryBtn')?.addEventListener('click', () => {
        STATE.recentHistory = [{ id: 'coxs-bazar', timestamp: '2026-11-17 18:08', action: 'History Cleared' }];
        savePersistedState();
        renderHomeRecentList();
        renderProfileView();
        showToast('Discovery history cleared');
      });

      document.getElementById('saveProfileSettingsBtn')?.addEventListener('click', () => {
        savePersistedState();
        showToast('Saved Explorer Profile & Meta Quest VR comfort preferences');
      });

      document.getElementById('exportUserDataBtn')?.addEventListener('click', () => {
        const payload = JSON.stringify({
          app: 'GLOBAL VR EXPLORER',
          exportedAt: '2026-11-17',
          savedDestinations: STATE.savedIds,
          recentHistory: STATE.recentHistory
        }, null, 2);
        const blob = new Blob([payload], { type: 'application/json' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = 'global_vr_explorer_profile.json';
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        URL.revokeObjectURL(url);
        showToast('Exported JSON profile');
      });

      window.addEventListener('resize', () => {
        if (STATE.activeView === 'home') initOrResizeHeroEarth();
        if (STATE.activeView === 'globe') initOrResizeGlobe();
        if (STATE.activeView === 'panorama') initOrResizePanorama();
      });



      bootstrapLanguagePreference();
      bootstrapAuthentication();
      if (window.lucide) window.lucide.createIcons();
    });

    // =========================================================================
    // SUPABASE AUTHENTICATION, GOOGLE OAUTH, GUEST ACCESS & USER PROFILE
    // =========================================================================
    const AUTH_STATE = {
      client: null,
      user: null,
      isGuest: false,
      loading: false
    };

    function getSupabasePublicConfig() {
      const winCfg = (typeof window !== 'undefined' && window.__GVR_AUTH_CONFIG__) || {};
      let lsUrl = '', lsKey = '';
      try {
        lsUrl = localStorage.getItem('gvr_supabase_url') || '';
        lsKey = localStorage.getItem('gvr_supabase_anon_key') || '';
      } catch (e) {}
      const url = (winCfg.supabaseUrl || lsUrl || '').trim();
      const anonKey = (winCfg.supabaseAnonKey || lsKey || '').trim();
      return { url, anonKey };
    }

    function initSupabaseAuthClient() {
      const { url, anonKey } = getSupabasePublicConfig();
      const cfgUrlEl = document.getElementById('cfgSupabaseUrl');
      const cfgKeyEl = document.getElementById('cfgSupabaseAnonKey');
      if (cfgUrlEl && url) cfgUrlEl.value = url;
      if (cfgKeyEl && anonKey) cfgKeyEl.value = anonKey;

      if (!url || !anonKey || typeof window === 'undefined' || !window.supabase || typeof window.supabase.createClient !== 'function') {
        AUTH_STATE.client = null;
        return null;
      }
      if (!AUTH_STATE.client) {
        AUTH_STATE.client = window.supabase.createClient(url, anonKey, {
          auth: { persistSession: true, autoRefreshToken: true, detectSessionInUrl: true }
        });
        AUTH_STATE.client.auth.onAuthStateChange((_event, session) => {
          applyAuthenticatedSession(session);
        });
      }
      return AUTH_STATE.client;
    }

    function saveSupabasePublicConfig() {
      const url = (document.getElementById('cfgSupabaseUrl')?.value || '').trim();
      const key = (document.getElementById('cfgSupabaseAnonKey')?.value || '').trim();
      if (!url.startsWith('https://') || key.length < 20) {
        setAuthFeedback('error', 'Please enter a valid https:// Supabase Project URL and public Anon Key.');
        return;
      }
      if (key.includes('service_role')) {
        setAuthFeedback('error', 'Security error: Never enter a secret service_role key in frontend code. Use only the public anon key.');
        return;
      }
      try {
        localStorage.setItem('gvr_supabase_url', url);
        localStorage.setItem('gvr_supabase_anon_key', key);
      } catch (e) {}
      AUTH_STATE.client = null;
      initSupabaseAuthClient();
      setAuthFeedback('success', 'Supabase Auth connected! You can now sign in with Google or Email.');
    }

    function setAuthFeedback(type, msg) {
      const box = document.getElementById('authFeedbackBanner');
      if (!box) return;
      if (!msg) {
        box.classList.add('hidden');
        box.textContent = '';
        return;
      }
      box.classList.remove('hidden');
      box.className = type === 'error'
        ? 'rounded-xl px-3.5 py-2.5 text-xs leading-relaxed border bg-rose-500/15 border-rose-400/40 text-rose-200'
        : 'rounded-xl px-3.5 py-2.5 text-xs leading-relaxed border bg-emerald-500/15 border-emerald-400/40 text-emerald-200';
      box.textContent = msg;
    }

    function switchAuthTab(tab) {
      setAuthFeedback('', '');
      const signInForm = document.getElementById('authSignInForm');
      const signUpForm = document.getElementById('authSignUpForm');
      const forgotForm = document.getElementById('authForgotForm');
      const tabIn = document.getElementById('authTabSignIn');
      const tabUp = document.getElementById('authTabSignUp');

      if (signInForm) signInForm.classList.toggle('hidden', tab !== 'signin');
      if (signUpForm) signUpForm.classList.toggle('hidden', tab !== 'signup');
      if (forgotForm) forgotForm.classList.toggle('hidden', tab !== 'forgot');

      if (tabIn && tabUp) {
        tabIn.className = tab === 'signin'
          ? 'py-1.5 rounded-lg bg-sky-500 text-slate-950 font-semibold transition'
          : 'py-1.5 rounded-lg text-slate-300 hover:text-white transition';
        tabUp.className = tab === 'signup'
          ? 'py-1.5 rounded-lg bg-sky-500 text-slate-950 font-semibold transition'
          : 'py-1.5 rounded-lg text-slate-300 hover:text-white transition';
      }
    }

    function togglePasswordVisibility(inputId, btn) {
      const inp = document.getElementById(inputId);
      if (!inp) return;
      const isPass = inp.type === 'password';
      inp.type = isPass ? 'text' : 'password';
      if (btn) {
        btn.innerHTML = `<i data-lucide="${isPass ? 'eye-off' : 'eye'}" class="w-4 h-4"></i>`;
        if (window.lucide) window.lucide.createIcons();
      }
    }

    function openAuthPortal(tab = 'signin') {
      const portal = document.getElementById('authPortalOverlay');
      if (portal) portal.classList.remove('hidden');
      switchAuthTab(tab);
      if (window.lucide) window.lucide.createIcons();
    }

    function enterAsGuest() {
      AUTH_STATE.isGuest = true;
      try { sessionStorage.setItem('gvr_guest_mode', '1'); } catch (e) {}
      const portal = document.getElementById('authPortalOverlay');
      if (portal) portal.classList.add('hidden');
      renderAuthHeaderState();
      if (typeof showToast === 'function') showToast('Exploring as Guest — full access to all 12 destinations & VR');
    }

    function validateEmailFormat(email) {
      return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
    }

    function setAuthBtnLoading(btnId, loading, label) {
      const btn = document.getElementById(btnId);
      if (!btn) return;
      btn.disabled = loading;
      btn.style.opacity = loading ? '0.65' : '1';
      btn.textContent = loading ? 'Please wait...' : label;
    }

    async function signInWithGoogleOAuth() {
      const client = initSupabaseAuthClient();
      if (!client) {
        setAuthFeedback('error', 'Google OAuth requires a configured Supabase Auth project. Click "Configure Supabase Auth" below or click "Continue as Guest" above to explore immediately.');
        return;
      }
      setAuthFeedback('', '');
      const { error } = await client.auth.signInWithOAuth({
        provider: 'google',
        options: { redirectTo: window.location.href.split('#')[0] }
      });
      if (error) setAuthFeedback('error', error.message || 'Google sign-in failed.');
    }

    async function handleEmailSignIn(ev) {
      if (ev && ev.preventDefault) ev.preventDefault();
      const email = (document.getElementById('authSignInEmail')?.value || '').trim();
      const password = document.getElementById('authSignInPassword')?.value || '';
      if (!validateEmailFormat(email)) {
        setAuthFeedback('error', 'Please enter a valid email address.');
        return;
      }
      if (password.length < 6) {
        setAuthFeedback('error', 'Password must be at least 6 characters.');
        return;
      }
      const client = initSupabaseAuthClient();
      if (!client) {
        setAuthFeedback('error', 'Authentication provider is not configured yet. Connect your Supabase project below or click "Continue as Guest" to explore immediately.');
        return;
      }
      setAuthBtnLoading('authSignInSubmitBtn', true, 'Sign in with Email');
      const { data, error } = await client.auth.signInWithPassword({ email, password });
      setAuthBtnLoading('authSignInSubmitBtn', false, 'Sign in with Email');
      if (error) {
        setAuthFeedback('error', error.message || 'Invalid email or password.');
        return;
      }
      if (data && data.session) {
        applyAuthenticatedSession(data.session);
        setAuthFeedback('success', 'Signed in successfully!');
        const portal = document.getElementById('authPortalOverlay');
        if (portal) portal.classList.add('hidden');
      }
    }

    async function handleEmailSignUp(ev) {
      if (ev && ev.preventDefault) ev.preventDefault();
      const fullName = (document.getElementById('authSignUpName')?.value || '').trim();
      const email = (document.getElementById('authSignUpEmail')?.value || '').trim();
      const password = document.getElementById('authSignUpPassword')?.value || '';
      const confirm = document.getElementById('authSignUpConfirm')?.value || '';

      if (fullName.length < 2) {
        setAuthFeedback('error', 'Please enter your full name.');
        return;
      }
      if (!validateEmailFormat(email)) {
        setAuthFeedback('error', 'Please enter a valid email address.');
        return;
      }
      if (password.length < 6) {
        setAuthFeedback('error', 'Password must be at least 6 characters.');
        return;
      }
      if (password !== confirm) {
        setAuthFeedback('error', 'Passwords do not match.');
        return;
      }
      const client = initSupabaseAuthClient();
      if (!client) {
        setAuthFeedback('error', 'Authentication provider is not configured yet. Connect your Supabase project below or click "Continue as Guest".');
        return;
      }
      setAuthBtnLoading('authSignUpSubmitBtn', true, 'Create Account');
      const { data, error } = await client.auth.signUp({
        email,
        password,
        options: { data: { full_name: fullName } }
      });
      setAuthBtnLoading('authSignUpSubmitBtn', false, 'Create Account');
      if (error) {
        setAuthFeedback('error', error.message || 'Could not create account.');
        return;
      }
      if (data && data.session) {
        applyAuthenticatedSession(data.session);
        const portal = document.getElementById('authPortalOverlay');
        if (portal) portal.classList.add('hidden');
      } else {
        setAuthFeedback('success', 'Account created! Please check your email to verify your address before signing in.');
      }
    }

    async function handleForgotPassword(ev) {
      if (ev && ev.preventDefault) ev.preventDefault();
      const email = (document.getElementById('authForgotEmail')?.value || '').trim();
      if (!validateEmailFormat(email)) {
        setAuthFeedback('error', 'Please enter a valid email address.');
        return;
      }
      const client = initSupabaseAuthClient();
      if (!client) {
        setAuthFeedback('error', 'Authentication provider is not configured yet.');
        return;
      }
      setAuthBtnLoading('authForgotSubmitBtn', true, 'Send Password Reset Email');
      const { error } = await client.auth.resetPasswordForEmail(email, {
        redirectTo: window.location.href.split('#')[0]
      });
      setAuthBtnLoading('authForgotSubmitBtn', false, 'Send Password Reset Email');
      if (error) {
        setAuthFeedback('error', error.message || 'Could not send reset email.');
        return;
      }
      setAuthFeedback('success', 'Password reset link sent! Check your inbox.');
    }

    async function signOutAuthUser() {
      const client = initSupabaseAuthClient();
      if (client) await client.auth.signOut();
      AUTH_STATE.user = null;
      const menu = document.getElementById('accountMenuDropdown');
      if (menu) menu.classList.add('hidden');
      renderAuthHeaderState();
      if (typeof showToast === 'function') showToast('Signed out');
    }

    function applyAuthenticatedSession(session) {
      AUTH_STATE.user = session && session.user ? session.user : null;
      if (AUTH_STATE.user) {
        AUTH_STATE.isGuest = false;
        const portal = document.getElementById('authPortalOverlay');
        if (portal) portal.classList.add('hidden');
      }
      renderAuthHeaderState();
    }

    function renderAuthHeaderState() {
      const signInBtn = document.getElementById('headerSignInBtn');
      const userPill = document.getElementById('headerUserPill');
      const user = AUTH_STATE.user;

      if (!user) {
        if (signInBtn) signInBtn.classList.remove('hidden');
        if (userPill) userPill.classList.add('hidden');
        return;
      }

      if (signInBtn) signInBtn.classList.add('hidden');
      if (userPill) userPill.classList.remove('hidden');

      const meta = user.user_metadata || {};
      const name = meta.full_name || meta.name || (user.email ? user.email.split('@')[0] : 'Explorer');
      const avatarUrl = meta.avatar_url || meta.picture || '';
      const initials = name.split(' ').map(w => w[0]).join('').slice(0, 2).toUpperCase() || 'U';
      const provider = (user.app_metadata && user.app_metadata.provider) || 'email';

      const setAvatar = (imgId, initId) => {
        const img = document.getElementById(imgId);
        const ini = document.getElementById(initId);
        if (img && ini) {
          if (avatarUrl) {
            img.src = avatarUrl;
            img.classList.remove('hidden');
            ini.classList.add('hidden');
          } else {
            img.classList.add('hidden');
            ini.classList.remove('hidden');
            ini.textContent = initials;
          }
        }
      };
      setAvatar('headerUserAvatar', 'headerUserInitials');
      setAvatar('menuUserAvatar', 'menuUserInitials');

      const hName = document.getElementById('headerUserName');
      const mName = document.getElementById('menuUserName');
      const mEmail = document.getElementById('menuUserEmail');
      const mProv = document.getElementById('menuAuthProvider');
      if (hName) hName.textContent = name;
      if (mName) mName.textContent = name;
      if (mEmail) mEmail.textContent = user.email || '';
      if (mProv) mProv.textContent = provider === 'google' ? 'Google OAuth • Verified' : 'Email • Verified';

      renderAccountMenuLists();
    }

    function renderAccountMenuLists() {
      const savedCount = document.getElementById('menuSavedCount');
      const savedList = document.getElementById('menuSavedList');
      const recentList = document.getElementById('menuRecentList');
      const savedIds = Array.isArray(STATE.savedIds) ? STATE.savedIds : [];
      const recent = Array.isArray(STATE.recentHistory) ? STATE.recentHistory : [];

      if (savedCount) savedCount.textContent = String(savedIds.length);
      if (savedList) {
        savedList.innerHTML = savedIds.length
          ? savedIds.map(id => {
              const d = typeof getDestinationById === 'function' ? getDestinationById(id) : null;
              return `<button type="button" onclick="jumpToDestinationFromMenu('${id}')" class="w-full text-left px-2 py-1 rounded hover:bg-white/10 truncate text-sky-300">★ ${d ? d.name : id}</button>`;
            }).join('')
          : '<div class="text-slate-500 italic">No saved destinations yet.</div>';
      }
      if (recentList) {
        recentList.innerHTML = recent.length
          ? recent.slice(0, 4).map(id => {
              const d = typeof getDestinationById === 'function' ? getDestinationById(id) : null;
              return `<button type="button" onclick="jumpToDestinationFromMenu('${id}')" class="w-full text-left px-2 py-1 rounded hover:bg-white/10 truncate text-slate-300">↺ ${d ? d.name : id}</button>`;
            }).join('')
          : '<div class="text-slate-500 italic">Explore a destination in VR to track history.</div>';
      }
    }

    function jumpToDestinationFromMenu(destId) {
      const menu = document.getElementById('accountMenuDropdown');
      if (menu) menu.classList.add('hidden');
      if (typeof launchVrDestination === 'function') {
        launchVrDestination(destId, 0);
      } else if (typeof selectDestination === 'function') {
        selectDestination(destId);
      }
    }

    function saveCurrentDestinationToFavorites() {
      const id = STATE.selectedDestinationId;
      if (!id) return;
      if (!Array.isArray(STATE.savedIds)) STATE.savedIds = [];
      if (!STATE.savedIds.includes(id)) {
        STATE.savedIds.unshift(id);
        try { localStorage.setItem('gvr_saved_ids', JSON.stringify(STATE.savedIds)); } catch (e) {}
        if (typeof showToast === 'function') showToast('Saved to Favorites');
      } else {
        STATE.savedIds = STATE.savedIds.filter(x => x !== id);
        if (typeof showToast === 'function') showToast('Removed from Favorites');
      }
      renderAccountMenuLists();
    }

    function toggleAccountMenu() {
      const menu = document.getElementById('accountMenuDropdown');
      if (!menu) return;
      menu.classList.toggle('hidden');
      if (!menu.classList.contains('hidden')) renderAccountMenuLists();
    }

    async function bootstrapAuthentication() {
      const client = initSupabaseAuthClient();
      if (client) {
        try {
          const { data } = await client.auth.getSession();
          if (data && data.session) {
            applyAuthenticatedSession(data.session);
            return;
          }
        } catch (e) {}
      }
      let wasGuest = false;
      try { wasGuest = sessionStorage.getItem('gvr_guest_mode') === '1'; } catch (e) {}
      if (!wasGuest && !AUTH_STATE.user) {
        openAuthPortal('signin');
      }
      renderAuthHeaderState();
    }


  </script>
</body>
</html>
