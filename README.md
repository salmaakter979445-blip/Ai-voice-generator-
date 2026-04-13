<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>AR Labs - AI Voice Generator</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: { extend: { colors: { brand: {50:'#eef2ff',100:'#e0e7ff',200:'#c7d2fe',300:'#a5b4fc',400:'#818cf8',500:'#6366f1',600:'#4f46e5',700:'#4338ca',800:'#3730a3',900:'#312e81',950:'#1e1b4b'} } } }
        }
    </script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&display=swap');
        *{font-family:'Inter',sans-serif;-webkit-tap-highlight-color:transparent;box-sizing:border-box}
        html,body{height:100dvh;overflow:hidden;overscroll-behavior-y:none;margin:0;padding:0}
        @keyframes pulse-glow{0%,100%{transform:scale(1);filter:drop-shadow(0 0 15px rgba(99,102,241,.4))}50%{transform:scale(1.08);filter:drop-shadow(0 0 30px rgba(99,102,241,.7))}}
        .splash-pulse{animation:pulse-glow 1.5s ease-in-out infinite}
        @keyframes spin-icon{to{transform:rotate(360deg)}}
        .spin-anim{animation:spin-icon 3s linear infinite}
        @keyframes glow-wheel{0%,100%{filter:drop-shadow(0 0 8px rgba(251,191,36,.3))}50%{filter:drop-shadow(0 0 20px rgba(251,191,36,.6))}}
        .wheel-glow{animation:glow-wheel 2s ease-in-out infinite}
        .glass{backdrop-filter:blur(20px);-webkit-backdrop-filter:blur(20px)}
        .line-clamp-2{display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical;overflow:hidden}
        ::-webkit-scrollbar{width:3px}::-webkit-scrollbar-thumb{background:rgba(99,102,241,.3);border-radius:10px}
        textarea:focus,input:focus{outline:none}
        .modal{display:none}.modal.open{display:flex}
    </style>
</head>
<body class="bg-gray-50 dark:bg-gray-950 text-gray-900 dark:text-white">

<!-- ===== SPLASH ===== -->
<div id="splash" class="fixed inset-0 z-[9999] bg-gradient-to-br from-brand-950 via-brand-900 to-purple-950 flex flex-col items-center justify-center transition-opacity duration-700">
    <div class="splash-pulse mb-6">
        <div class="w-24 h-24 rounded-3xl bg-gradient-to-br from-brand-500 to-purple-600 flex items-center justify-center shadow-2xl">
            <i class="fas fa-microphone-lines text-white text-4xl"></i>
        </div>
    </div>
    <h1 class="text-3xl font-bold text-white mb-2">AR Labs</h1>
    <p class="text-brand-300 text-sm">AI Voice Generator</p>
    <div class="mt-8"><div class="w-8 h-8 border-[3px] border-brand-400 border-t-transparent rounded-full animate-spin"></div></div>
</div>

<!-- ===== APP ===== -->
<div id="app" class="h-[100dvh] max-w-lg mx-auto flex flex-col relative overflow-hidden bg-gray-50 dark:bg-gray-950 shadow-2xl" style="opacity:0">

    <!-- HEADER -->
    <header class="glass sticky top-0 z-40 bg-white/70 dark:bg-gray-900/70 border-b border-gray-200/50 dark:border-white/5">
        <div class="flex items-center justify-between px-4 py-3">
            <button id="menuBtn" class="w-10 h-10 rounded-xl flex items-center justify-center hover:bg-gray-100 dark:hover:bg-white/5 active:scale-90 transition-all"><i class="fas fa-bars text-lg"></i></button>
            <div class="flex items-center gap-2">
                <div class="w-8 h-8 rounded-lg bg-gradient-to-br from-brand-500 to-purple-600 flex items-center justify-center"><i class="fas fa-microphone-lines text-white text-sm"></i></div>
                <span class="text-lg font-bold bg-gradient-to-r from-brand-600 to-purple-600 bg-clip-text text-transparent">AR Labs</span>
            </div>
            <div class="flex items-center gap-1.5 bg-brand-500/10 dark:bg-brand-500/20 px-3 py-1.5 rounded-full">
                <i class="fas fa-bolt text-brand-500 text-xs"></i>
                <span id="quotaChip" class="text-sm font-bold text-brand-600 dark:text-brand-400">5</span>
            </div>
        </div>
    </header>

    <!-- MAIN -->
    <main class="flex-1 overflow-y-auto px-4 py-4 pb-40">
        <!-- Script Card -->
        <div class="bg-white dark:bg-gray-900 rounded-3xl shadow-lg border border-gray-100 dark:border-white/5 overflow-hidden">
            <div class="flex items-center justify-between px-5 pt-4 pb-2">
                <div class="flex items-center gap-2"><i class="fas fa-pen-fancy text-brand-500 text-sm"></i><span class="font-semibold text-sm">Your Script</span></div>
                <div class="flex items-center gap-2">
                    <button id="magicBtn" class="flex items-center gap-1.5 px-3 py-1.5 rounded-xl bg-purple-500/10 text-purple-600 dark:text-purple-400 text-xs font-medium active:scale-95 transition-all"><i class="fas fa-wand-magic-sparkles text-[10px]"></i>Magic Fill</button>
                    <button id="clearBtn" class="w-8 h-8 rounded-xl flex items-center justify-center hover:bg-gray-100 dark:hover:bg-white/5 active:scale-90 transition-all"><i class="fas fa-eraser text-gray-400 text-sm"></i></button>
                </div>
            </div>
            <div class="px-5 pb-2">
                <textarea id="scriptInput" rows="7" maxlength="5000" placeholder="Type or paste your script here... ✍️" class="w-full bg-transparent resize-none text-[15px] leading-relaxed placeholder:text-gray-400 dark:placeholder:text-gray-600 border-0 p-0"></textarea>
            </div>
            <div class="flex items-center justify-between px-5 py-3 bg-gray-50 dark:bg-white/[0.02] border-t border-gray-100 dark:border-white/5">
                <span class="text-xs text-gray-400"><span id="charCount">0</span> / 5,000</span>
                <span class="text-xs text-gray-400"><i class="fas fa-globe text-[10px] mr-1"></i>Multi-language</span>
            </div>
        </div>

        <!-- Audio Player -->
        <div id="player" class="hidden mt-4 bg-white dark:bg-gray-900 rounded-3xl shadow-lg border border-gray-100 dark:border-white/5 p-5">
            <div class="flex items-center justify-between mb-4">
                <div class="flex items-center gap-3">
                    <div class="w-11 h-11 rounded-2xl bg-gradient-to-br from-brand-500 to-purple-600 flex items-center justify-center shadow-lg shadow-brand-500/30"><i class="fas fa-wave-square text-white"></i></div>
                    <div><div class="font-semibold text-sm">Generated Audio</div><div class="text-xs text-gray-500">Ready to play</div></div>
                </div>
                <div class="flex items-center gap-1">
                    <button id="dlBtn" class="w-9 h-9 rounded-xl flex items-center justify-center hover:bg-gray-100 dark:hover:bg-white/5 active:scale-90"><i class="fas fa-download text-green-500 text-sm"></i></button>
                    <button id="closePlayerBtn" class="w-9 h-9 rounded-xl flex items-center justify-center hover:bg-gray-100 dark:hover:bg-white/5 active:scale-90"><i class="fas fa-xmark text-gray-400"></i></button>
                </div>
            </div>
            <div id="progBar" class="w-full h-2 bg-gray-200 dark:bg-gray-800 rounded-full cursor-pointer mb-3 overflow-hidden"><div id="progFill" class="h-full bg-gradient-to-r from-brand-500 to-purple-500 rounded-full" style="width:0"></div></div>
            <div class="flex justify-between mb-4"><span id="curTime" class="text-xs text-gray-500 font-mono">0:00</span><span id="durTime" class="text-xs text-gray-500 font-mono">0:00</span></div>
            <div class="flex justify-center">
                <button id="playBtn" class="w-14 h-14 rounded-2xl bg-gradient-to-br from-brand-500 to-purple-600 flex items-center justify-center shadow-lg shadow-brand-500/30 active:scale-90 transition-all"><i class="fas fa-play text-white text-lg ml-0.5"></i></button>
            </div>
        </div>

        <!-- Tip -->
        <div class="mt-4 p-4 rounded-2xl bg-brand-500/5 dark:bg-brand-500/10 border border-brand-200/30 dark:border-brand-500/10">
            <div class="flex items-start gap-3">
                <div class="w-8 h-8 rounded-xl bg-brand-500/10 flex items-center justify-center flex-shrink-0"><i class="fas fa-lightbulb text-brand-500 text-sm"></i></div>
                <div><p class="text-xs font-semibold text-brand-600 dark:text-brand-400 mb-1">Pro Tip</p><p class="text-xs text-gray-600 dark:text-gray-400 leading-relaxed">Add punctuation for natural pauses. Commas, periods, and exclamation marks improve results!</p></div>
            </div>
        </div>
    </main>

    <!-- BOTTOM BAR -->
    <div class="fixed bottom-0 left-0 right-0 z-30"><div class="max-w-lg mx-auto">
        <div id="voiceDrop" class="hidden mx-4 mb-2 bg-white/95 dark:bg-gray-900/95 backdrop-blur-xl rounded-3xl shadow-2xl border border-gray-200/50 dark:border-white/10 overflow-hidden">
            <div class="px-4 pt-4 pb-2"><p class="text-xs font-semibold text-gray-500 uppercase tracking-wider">Select Voice</p></div>
            <div id="voiceList" class="max-h-[240px] overflow-y-auto px-2 pb-3 space-y-1"></div>
        </div>
        <div class="glass bg-white/80 dark:bg-gray-900/80 border-t border-gray-200/50 dark:border-white/5 px-4 py-3" style="padding-bottom:max(.75rem,env(safe-area-inset-bottom))">
            <div class="flex items-center gap-3">
                <button id="voiceSelBtn" class="flex-1 flex items-center gap-3 px-4 py-3 rounded-2xl bg-gray-100 dark:bg-white/5 border border-gray-200/50 dark:border-white/5 active:scale-[.98] transition-all">
                    <div id="vBtnGrad" class="w-9 h-9 rounded-xl bg-gradient-to-br from-yellow-400 to-orange-500 flex items-center justify-center"><i id="vBtnIcon" class="fas fa-face-smile-beam text-white text-sm"></i></div>
                    <div class="text-left flex-1"><div id="vBtnName" class="font-semibold text-sm">Zephyr</div><div class="text-[10px] text-gray-500">Tap to change</div></div>
                    <i class="fas fa-chevron-up text-gray-400 text-xs"></i>
                </button>
                <button id="createBtn" class="px-6 py-3 rounded-2xl bg-gradient-to-r from-brand-500 to-purple-600 text-white font-bold text-sm shadow-lg shadow-brand-500/30 active:scale-95 transition-all whitespace-nowrap"><i class="fas fa-wand-magic-sparkles mr-2"></i>Create</button>
            </div>
        </div>
    </div></div>
</div>

<!-- SIDE MENU -->
<div id="menuOverlay" class="fixed inset-0 z-50 hidden">
    <div id="menuBg" class="absolute inset-0 bg-black/50 opacity-0 transition-opacity duration-300"></div>
    <div id="menuPanel" class="absolute left-0 top-0 bottom-0 w-[80%] max-w-[320px] bg-white dark:bg-gray-950 -translate-x-full transition-transform duration-300 flex flex-col shadow-2xl">
        <div class="p-6 bg-gradient-to-br from-brand-600 to-purple-700">
            <button id="profileMenuBtn" class="flex items-center gap-4 w-full text-left">
                <div class="usr-av w-14 h-14 rounded-2xl bg-brand-500/50 flex items-center justify-center ring-2 ring-white/20 overflow-hidden"><span class="text-white font-bold text-lg">U</span></div>
                <div class="flex-1"><div class="usr-name text-white font-bold">User</div><div class="text-brand-200 text-xs mt-0.5">Tap to edit profile</div></div>
                <i class="fas fa-chevron-right text-white/50 text-sm"></i>
            </button>
        </div>
        <div class="flex-1 overflow-y-auto py-4 px-3 space-y-1">
            <button id="spinMenuBtn" class="w-full flex items-center gap-4 px-4 py-4 rounded-2xl bg-gradient-to-r from-amber-500/10 to-orange-500/10 border border-amber-300/30 dark:border-amber-500/20 active:scale-[.98] transition-all">
                <div class="w-11 h-11 rounded-xl bg-gradient-to-br from-amber-400 to-orange-600 flex items-center justify-center shadow-lg shadow-orange-500/30"><i class="fas fa-dharmachakra text-white text-lg spin-anim"></i></div>
                <div class="text-left flex-1"><div class="font-bold text-sm text-amber-700 dark:text-amber-400">Daily Spin & Win</div><div class="text-[10px] text-amber-600/70 mt-0.5">Get Free Voices!</div></div>
                <div class="w-6 h-6 rounded-full bg-red-500 flex items-center justify-center"><span class="text-white text-[9px] font-bold">!</span></div>
            </button>
            <button id="histMenuBtn" class="w-full flex items-center gap-4 px-4 py-3.5 rounded-2xl hover:bg-gray-100 dark:hover:bg-white/5 transition-colors active:scale-[.98]">
                <div class="w-10 h-10 rounded-xl bg-blue-500/10 flex items-center justify-center"><i class="fas fa-clock-rotate-left text-blue-500"></i></div><span class="font-medium text-sm">Generation History</span>
            </button>
            <button id="aboutMenuBtn" class="w-full flex items-center gap-4 px-4 py-3.5 rounded-2xl hover:bg-gray-100 dark:hover:bg-white/5 transition-colors active:scale-[.98]">
                <div class="w-10 h-10 rounded-xl bg-purple-500/10 flex items-center justify-center"><i class="fas fa-circle-info text-purple-500"></i></div><span class="font-medium text-sm">About App</span>
            </button>
            <button id="reportMenuBtn" class="w-full flex items-center gap-4 px-4 py-3.5 rounded-2xl hover:bg-gray-100 dark:hover:bg-white/5 transition-colors active:scale-[.98]">
                <div class="w-10 h-10 rounded-xl bg-red-500/10 flex items-center justify-center"><i class="fas fa-flag text-red-500"></i></div><span class="font-medium text-sm">Report Issue</span>
            </button>
            <div class="h-px bg-gray-200 dark:bg-white/5 mx-4 my-2"></div>
            <div class="flex items-center gap-4 px-4 py-3.5">
                <div class="w-10 h-10 rounded-xl bg-gray-500/10 flex items-center justify-center"><i class="fas fa-moon text-gray-500"></i></div>
                <span class="font-medium text-sm flex-1">Dark Mode</span>
                <label class="relative inline-flex items-center cursor-pointer"><input id="themeToggle" type="checkbox" class="sr-only peer"><div class="w-11 h-6 bg-gray-300 rounded-full peer dark:bg-gray-700 peer-checked:after:translate-x-full after:content-[''] after:absolute after:top-[2px] after:start-[2px] after:bg-white after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-brand-500"></div></label>
            </div>
        </div>
        <div class="p-4 border-t border-gray-200 dark:border-white/5"><p class="text-[10px] text-gray-400 text-center">AR Labs v1.0</p></div>
    </div>
</div>

<!-- PROFILE MODAL -->
<div id="profileModal" class="modal fixed inset-0 z-[60] items-center justify-center p-6">
    <div class="absolute inset-0 bg-black/60 backdrop-blur-sm modal-close"></div>
    <div class="relative bg-white dark:bg-gray-900 rounded-[2rem] shadow-2xl w-full max-w-sm overflow-hidden">
        <div class="relative bg-gradient-to-br from-brand-500 to-purple-600 pt-8 pb-16 px-6">
            <button class="absolute top-4 right-4 w-8 h-8 rounded-full bg-white/20 flex items-center justify-center modal-close"><i class="fas fa-xmark text-white text-sm"></i></button>
            <h3 class="text-white font-bold text-lg">Edit Profile</h3>
        </div>
        <div class="flex justify-center -mt-12 mb-4">
            <button id="avUploadBtn" class="usr-av w-24 h-24 rounded-3xl bg-gradient-to-br from-brand-400 to-purple-500 flex items-center justify-center ring-4 ring-white dark:ring-gray-900 shadow-xl overflow-hidden relative group">
                <span class="text-white font-bold text-3xl">U</span>
                <div class="absolute inset-0 bg-black/40 opacity-0 group-hover:opacity-100 transition-opacity flex items-center justify-center"><i class="fas fa-camera text-white text-lg"></i></div>
            </button>
            <input id="avFileInput" type="file" accept="image/*" class="hidden">
        </div>
        <div class="px-6 pb-6 space-y-4">
            <div><label class="text-xs font-semibold text-gray-500 mb-1.5 block">Display Name</label><input id="profNameInput" type="text" placeholder="Your name" class="w-full px-4 py-3 rounded-2xl bg-gray-100 dark:bg-white/5 border border-gray-200 dark:border-white/10 text-sm font-medium focus:ring-2 focus:ring-brand-500/50"></div>
            <div><label class="text-xs font-semibold text-gray-500 mb-1.5 block">Email</label><div class="w-full px-4 py-3 rounded-2xl bg-gray-100 dark:bg-white/5 border border-gray-200 dark:border-white/10 text-sm text-gray-500" id="profEmail">Anonymous</div></div>
            <button id="saveProfileBtn" class="w-full py-3.5 rounded-2xl bg-gradient-to-r from-brand-500 to-purple-600 text-white font-bold text-sm shadow-lg active:scale-95 transition-all"><i class="fas fa-check mr-2"></i>Save Changes</button>
        </div>
    </div>
</div>

<!-- SPIN MODAL -->
<div id="spinModal" class="modal fixed inset-0 z-[60] items-center justify-center p-4">
    <div class="absolute inset-0 bg-black/70 backdrop-blur-sm modal-close"></div>
    <div class="relative bg-white dark:bg-gray-900 rounded-[2rem] shadow-2xl w-full max-w-sm overflow-hidden">
        <div class="bg-gradient-to-br from-amber-500 to-orange-600 p-5 text-center relative">
            <button class="absolute top-4 right-4 w-8 h-8 rounded-full bg-white/20 flex items-center justify-center modal-close"><i class="fas fa-xmark text-white text-sm"></i></button>
            <i class="fas fa-dharmachakra text-white/30 text-5xl mb-2 spin-anim"></i>
            <h3 class="text-white font-bold text-xl">Spin & Win!</h3>
            <p class="text-amber-100 text-xs mt-1">Win free voice credits</p>
        </div>
        <div class="p-6 flex flex-col items-center">
            <div class="mb-1"><i class="fas fa-caret-down text-red-500 text-3xl"></i></div>
            <div class="wheel-glow mb-6">
                <div id="wheel" class="w-56 h-56 rounded-full border-4 border-amber-400 shadow-xl relative" style="background:conic-gradient(from 0deg,#ef4444 0deg 51.43deg,#f59e0b 51.43deg 102.86deg,#10b981 102.86deg 154.29deg,#6366f1 154.29deg 205.71deg,#ec4899 205.71deg 257.14deg,#8b5cf6 257.14deg 308.57deg,#14b8a6 308.57deg 360deg)">
                    <div class="absolute inset-0 flex items-center justify-center"><div class="w-14 h-14 rounded-full bg-white dark:bg-gray-800 shadow-inner flex items-center justify-center border-4 border-amber-400 z-10"><i class="fas fa-star text-amber-500 text-lg"></i></div></div>
                </div>
            </div>
            <button id="spinBtn" class="w-full py-4 rounded-2xl font-bold text-lg bg-gradient-to-r from-amber-500 to-orange-600 text-white shadow-lg active:scale-95 transition-all"><i class="fas fa-play mr-2"></i>SPIN NOW!</button>
        </div>
    </div>
</div>

<!-- HISTORY MODAL -->
<div id="histModal" class="modal fixed inset-0 z-[60] items-end justify-center">
    <div class="absolute inset-0 bg-black/60 backdrop-blur-sm modal-close"></div>
    <div class="relative bg-white dark:bg-gray-950 rounded-t-[2rem] shadow-2xl w-full max-w-lg max-h-[85vh] flex flex-col">
        <div class="flex items-center justify-between px-6 pt-6 pb-4">
            <div><h3 class="font-bold text-lg">History</h3><p class="text-xs text-gray-500 mt-0.5">This session</p></div>
            <button class="w-9 h-9 rounded-xl bg-gray-100 dark:bg-white/5 flex items-center justify-center active:scale-90 modal-close"><i class="fas fa-xmark text-gray-500"></i></button>
        </div>
        <div id="histList" class="flex-1 overflow-y-auto px-6 pb-6 space-y-3"></div>
    </div>
</div>

<!-- ABOUT MODAL -->
<div id="aboutModal" class="modal fixed inset-0 z-[60] items-center justify-center p-6">
    <div class="absolute inset-0 bg-black/60 backdrop-blur-sm modal-close"></div>
    <div class="relative bg-white dark:bg-gray-900 rounded-[2rem] shadow-2xl w-full max-w-sm overflow-hidden">
        <div class="p-8 text-center">
            <div class="w-20 h-20 rounded-3xl bg-gradient-to-br from-brand-500 to-purple-600 flex items-center justify-center mx-auto mb-4 shadow-xl"><i class="fas fa-microphone-lines text-white text-3xl"></i></div>
            <h3 class="font-bold text-xl mb-1">AR Labs</h3><p class="text-xs text-gray-500 mb-4">Version 1.0.0</p>
            <p class="text-sm text-gray-600 dark:text-gray-400 leading-relaxed mb-6">Premium AI text-to-speech. Generate natural voices with cutting-edge technology.</p>
            <div class="space-y-2 text-xs text-gray-500"><p><i class="fas fa-brain mr-2 text-brand-500"></i>Powered by Google Gemini</p><p><i class="fas fa-shield-halved mr-2 text-green-500"></i>Firebase Cloud</p><p><i class="fas fa-heart mr-2 text-red-500"></i>Made with love</p></div>
            <button class="mt-6 w-full py-3 rounded-2xl bg-gray-100 dark:bg-white/5 font-semibold text-sm active:scale-95 modal-close">Close</button>
        </div>
    </div>
</div>

<!-- REPORT MODAL -->
<div id="reportModal" class="modal fixed inset-0 z-[60] items-center justify-center p-6">
    <div class="absolute inset-0 bg-black/60 backdrop-blur-sm modal-close"></div>
    <div class="relative bg-white dark:bg-gray-900 rounded-[2rem] shadow-2xl w-full max-w-sm overflow-hidden">
        <div class="p-6">
            <div class="flex items-center justify-between mb-4"><h3 class="font-bold text-lg">Report Issue</h3><button class="w-8 h-8 rounded-xl bg-gray-100 dark:bg-white/5 flex items-center justify-center active:scale-90 modal-close"><i class="fas fa-xmark text-gray-500 text-sm"></i></button></div>
            <textarea id="reportText" rows="4" placeholder="Describe the issue..." class="w-full px-4 py-3 rounded-2xl bg-gray-100 dark:bg-white/5 border border-gray-200 dark:border-white/10 text-sm resize-none focus:ring-2 focus:ring-brand-500/50 mb-4"></textarea>
            <button id="submitReportBtn" class="w-full py-3 rounded-2xl bg-gradient-to-r from-brand-500 to-purple-600 text-white font-bold text-sm shadow-lg active:scale-95 transition-all"><i class="fas fa-paper-plane mr-2"></i>Submit</button>
        </div>
    </div>
</div>

<!-- TOASTS -->
<div id="toasts" class="fixed bottom-24 left-0 right-0 z-[9998] flex flex-col items-center gap-2 pointer-events-none px-4"></div>

<!-- FIREBASE COMPAT SDK -->
<script src="https://www.gstatic.com/firebasejs/10.14.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.14.1/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.14.1/firebase-firestore-compat.js"></script>

<script>
(function() {
    'use strict';

    // ========== CONFIG ==========
    var firebaseConfig = {
        apiKey: "AIzaSyBWnbZZ3Nq7oaBMb9KCew6VRcEcxMj95Ko",
        authDomain: "calling-607a0.firebaseapp.com",
        databaseURL: "https://calling-607a0-default-rtdb.firebaseio.com",
        projectId: "calling-607a0",
        storageBucket: "calling-607a0.firebasestorage.app",
        messagingSenderId: "321129152309",
        appId: "1:321129152309:web:6d20d29b32f45fe7f8d67c"
    };
    var GEMINI_KEY = "AIzaSyA5nWR8dpijqprJmqGnGqjWn4vvX5WW9L0";
    var IMGBB_KEY = "ffc74bcba2a9e9af2359cd684e6beef6";
    var APP_ID = "arlabs_tts_v1";

    // ========== FIREBASE ==========
    var fbApp, fbAuth, fbDb;
    try {
        fbApp = firebase.initializeApp(firebaseConfig);
        fbAuth = firebase.auth();
        fbDb = firebase.firestore();
    } catch(e) {
        console.error("Firebase init error:", e);
    }

    // ========== STATE ==========
    var user = null;
    var ud = { displayName: "User", email: "", photoURL: "", quota: 5, lastResetDate: "", lastSpinDate: "" };
    var history = [];
    var curBlob = null;
    var aCtx = null, aSrc = null, aBuf = null;
    var playing = false, pStart = 0, pOff = 0, aFrame = null;
    var dark = false;
    var selVoice = null;
    var ddOpen = false;
    var isSpinning = false;

    var voices = [
        { id:"Zephyr", name:"Zephyr", desc:"Bright & cheerful", icon:"fa-face-smile-beam", color:"from-yellow-400 to-orange-500" },
        { id:"Puck", name:"Puck", desc:"Playful & energetic", icon:"fa-face-grin-stars", color:"from-pink-400 to-rose-500" },
        { id:"Charon", name:"Charon", desc:"Deep & mysterious", icon:"fa-mask", color:"from-purple-500 to-indigo-600" },
        { id:"Kore", name:"Kore", desc:"Warm & gentle", icon:"fa-heart", color:"from-red-400 to-pink-500" },
        { id:"Fenrir", name:"Fenrir", desc:"Strong & bold", icon:"fa-bolt", color:"from-blue-500 to-cyan-500" },
        { id:"Leda", name:"Leda", desc:"Smooth & calm", icon:"fa-feather", color:"from-teal-400 to-green-500" },
        { id:"Orus", name:"Orus", desc:"Rich & resonant", icon:"fa-microphone", color:"from-amber-500 to-yellow-600" },
        { id:"Aoede", name:"Aoede", desc:"Musical & melodic", icon:"fa-music", color:"from-violet-400 to-purple-500" }
    ];
    selVoice = voices[0];

    var samples = [
        "Welcome to AR Labs, your premium AI voice generator!",
        "The future of content creation is here. Transform text into speech instantly.",
        "Good morning everyone! Let's explore the world of AI today.",
        "Hey there! Thanks for tuning in to my channel.",
        "In a world where technology meets creativity, AR Labs leads the way.",
        "Once upon a time, there lived a brilliant inventor who dreamed big."
    ];

    // ========== HELPERS ==========
    function E(id) { return document.getElementById(id); }
    function todayStr() { return new Date().toISOString().split('T')[0]; }

    function toast(msg, type) {
        var c = E('toasts');
        var t = document.createElement('div');
        var bg = 'bg-indigo-600', ic = 'fa-info-circle';
        if (type === 'success') { bg = 'bg-green-600'; ic = 'fa-check-circle'; }
        if (type === 'error') { bg = 'bg-red-600'; ic = 'fa-exclamation-circle'; }
        if (type === 'warning') { bg = 'bg-amber-600'; ic = 'fa-exclamation-triangle'; }
        t.className = 'flex items-center gap-3 px-5 py-3.5 rounded-2xl ' + bg + ' text-white shadow-2xl text-sm font-medium transform translate-y-10 opacity-0 transition-all duration-300 max-w-[90vw]';
        t.innerHTML = '<i class="fas ' + ic + ' text-lg"></i><span class="flex-1">' + msg + '</span>';
        c.appendChild(t);
        setTimeout(function() { t.classList.remove('translate-y-10', 'opacity-0'); }, 20);
        setTimeout(function() { t.classList.add('translate-y-10', 'opacity-0'); setTimeout(function() { if (t.parentNode) t.remove(); }, 400); }, 3000);
    }

    // ========== THEME ==========
    function initTheme() {
        var s = localStorage.getItem('arlabs-theme');
        dark = s ? s === 'dark' : window.matchMedia('(prefers-color-scheme:dark)').matches;
        applyTheme();
    }
    function applyTheme() {
        document.documentElement.classList.toggle('dark', dark);
        localStorage.setItem('arlabs-theme', dark ? 'dark' : 'light');
        var t = E('themeToggle');
        if (t) t.checked = dark;
    }

    // ========== SPLASH ==========
    function hideSplash() {
        var s = E('splash');
        var a = E('app');
        if (s) {
            s.style.opacity = '0';
            setTimeout(function() {
                if (s.parentNode) s.parentNode.removeChild(s);
                a.style.opacity = '1';
            }, 700);
        } else {
            a.style.opacity = '1';
        }
    }

    // ========== FIRESTORE ==========
    function getDocRef() {
        if (!user || !fbDb) return null;
        return fbDb.collection('artifacts').doc(APP_ID).collection('users').doc(user.uid).collection('profile').doc('data');
    }

    function loadData(cb) {
        var ref = getDocRef();
        if (!ref) { if (cb) cb(); return; }
        ref.get().then(function(snap) {
            if (snap.exists()) {
                var d = snap.data();
                ud.displayName = d.displayName || ud.displayName;
                ud.email = d.email || ud.email;
                ud.photoURL = d.photoURL || ud.photoURL;
                ud.quota = (d.quota !== undefined) ? d.quota : ud.quota;
                ud.lastResetDate = d.lastResetDate || "";
                ud.lastSpinDate = d.lastSpinDate || "";
            }
            // Daily reset
            if (ud.lastResetDate !== todayStr()) {
                ud.quota = 5;
                ud.lastResetDate = todayStr();
                saveData();
            }
            updateUI();
            if (cb) cb();
        }).catch(function(e) {
            console.error("Load error:", e);
            if (cb) cb();
        });
    }

    function saveData() {
        var ref = getDocRef();
        if (!ref) return;
        ref.set({
            displayName: ud.displayName,
            email: ud.email || "",
            photoURL: ud.photoURL || "",
            quota: ud.quota,
            lastResetDate: ud.lastResetDate,
            lastSpinDate: ud.lastSpinDate || ""
        }, { merge: true }).catch(function(e) { console.error("Save error:", e); });
    }

    function updateUI() {
        E('quotaChip').textContent = ud.quota;
        var nameEls = document.querySelectorAll('.usr-name');
        nameEls.forEach(function(el) { el.textContent = ud.displayName || 'User'; });
        var avEls = document.querySelectorAll('.usr-av');
        avEls.forEach(function(el) {
            if (ud.photoURL) {
                el.innerHTML = '<img src="' + ud.photoURL + '" class="w-full h-full object-cover rounded-full">';
            } else {
                var init = (ud.displayName || 'U').charAt(0).toUpperCase();
                el.innerHTML = '<span class="text-white font-bold text-lg">' + init + '</span>';
            }
        });
        updateSpinBtn();
    }

    // ========== AUTH ==========
    function initAuth() {
        if (!fbAuth) {
            console.error("No auth");
            hideSplash();
            return;
        }
        fbAuth.onAuthStateChanged(function(u) {
            if (u) {
                user = u;
                ud.displayName = u.displayName || "User";
                ud.email = u.email || "Anonymous";
                ud.photoURL = u.photoURL || "";
                loadData(function() {
                    hideSplash();
                });
            } else {
                fbAuth.signInAnonymously().catch(function(e) {
                    console.error("Auth error:", e);
                    toast("Authentication failed", "error");
                    hideSplash();
                });
            }
        });
    }

    // ========== MENU ==========
    function openMenu() {
        E('menuOverlay').classList.remove('hidden');
        setTimeout(function() {
            E('menuPanel').classList.remove('-translate-x-full');
            E('menuBg').classList.remove('opacity-0');
        }, 20);
    }
    function closeMenu() {
        E('menuPanel').classList.add('-translate-x-full');
        E('menuBg').classList.add('opacity-0');
        setTimeout(function() { E('menuOverlay').classList.add('hidden'); }, 300);
    }

    // ========== MODALS ==========
    function openModal(id) { closeMenu(); E(id).classList.add('open'); }
    function closeModalEl(el) {
        var modal = el.closest('.modal');
        if (modal) modal.classList.remove('open');
    }

    document.addEventListener('click', function(e) {
        if (e.target.closest('.modal-close')) {
            closeModalEl(e.target);
        }
    });

    // ========== VOICES ==========
    function renderVoices() {
        var html = '';
        voices.forEach(function(v) {
            var active = selVoice.id === v.id;
            html += '<button data-vid="' + v.id + '" class="flex items-center gap-3 w-full px-4 py-3 hover:bg-gray-100 dark:hover:bg-white/5 rounded-xl transition-colors ' + (active ? 'bg-indigo-500/20 ring-1 ring-indigo-400' : '') + '">';
            html += '<div class="w-10 h-10 rounded-xl bg-gradient-to-br ' + v.color + ' flex items-center justify-center shadow-lg"><i class="fas ' + v.icon + ' text-white text-sm"></i></div>';
            html += '<div class="text-left flex-1"><div class="font-semibold text-sm">' + v.name + '</div><div class="text-xs text-gray-500">' + v.desc + '</div></div>';
            if (active) html += '<i class="fas fa-check-circle text-indigo-400"></i>';
            html += '</button>';
        });
        E('voiceList').innerHTML = html;
    }

    function toggleDD() {
        ddOpen = !ddOpen;
        E('voiceDrop').classList.toggle('hidden', !ddOpen);
    }

    E('voiceList').addEventListener('click', function(e) {
        var btn = e.target.closest('[data-vid]');
        if (!btn) return;
        var v = voices.find(function(x) { return x.id === btn.dataset.vid; });
        if (v) {
            selVoice = v;
            E('vBtnName').textContent = v.name;
            E('vBtnIcon').className = 'fas ' + v.icon + ' text-white text-sm';
            renderVoices();
            toggleDD();
        }
    });

    // ========== TTS ==========
    function pcmToWav(b64, sr) {
        sr = sr || 24000;
        var bin = atob(b64);
        var pcm = new Uint8Array(bin.length);
        for (var i = 0; i < bin.length; i++) pcm[i] = bin.charCodeAt(i);
        var nc = 1, bps = 16, br = sr * nc * (bps / 8), ba = nc * (bps / 8);
        var ds = pcm.length, hs = 44, ts = hs + ds;
        var buf = new ArrayBuffer(ts);
        var dv = new DataView(buf);
        function ws(o, s) { for (var j = 0; j < s.length; j++) dv.setUint8(o + j, s.charCodeAt(j)); }
        ws(0, 'RIFF'); dv.setUint32(4, ts - 8, true); ws(8, 'WAVE'); ws(12, 'fmt ');
        dv.setUint32(16, 16, true); dv.setUint16(20, 1, true); dv.setUint16(22, nc, true);
        dv.setUint32(24, sr, true); dv.setUint32(28, br, true); dv.setUint16(32, ba, true);
        dv.setUint16(34, bps, true); ws(36, 'data'); dv.setUint32(40, ds, true);
        new Uint8Array(buf, hs).set(pcm);
        return new Blob([buf], { type: 'audio/wav' });
    }

    function doGenerate() {
        var text = E('scriptInput').value.trim();
        if (!text) { toast("Please enter some text first", "warning"); return; }
        if (text.length > 5000) { toast("Text too long (max 5000)", "warning"); return; }
        if (ud.quota <= 0) { toast("No credits left! Spin the wheel 🎡", "error"); return; }

        var btn = E('createBtn');
        btn.disabled = true;
        btn.innerHTML = '<i class="fas fa-spinner fa-spin mr-2"></i>Generating...';

        var url = 'https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-tts:generateContent?key=' + GEMINI_KEY;
        var body = {
            contents: [{ parts: [{ text: text }] }],
            generationConfig: {
                response_modalities: ["AUDIO"],
                speech_config: { voiceConfig: { prebuiltVoiceConfig: { voiceName: selVoice.id } } }
            }
        };

        var retries = 0;
        function tryFetch() {
            fetch(url, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(body)
            }).then(function(res) {
                if (res.ok) return res.json();
                if ((res.status === 429 || res.status >= 500) && retries < 3) {
                    retries++;
                    setTimeout(tryFetch, Math.pow(2, retries) * 1000);
                    return null;
                }
                return res.json().then(function(d) {
                    throw new Error((d.error && d.error.message) || 'API Error ' + res.status);
                });
            }).then(function(data) {
                if (!data) return;
                var audioData = null;
                if (data.candidates && data.candidates[0] && data.candidates[0].content && data.candidates[0].content.parts) {
                    var parts = data.candidates[0].content.parts;
                    for (var i = 0; i < parts.length; i++) {
                        if (parts[i].inlineData && parts[i].inlineData.data) {
                            audioData = parts[i].inlineData.data;
                            break;
                        }
                    }
                }
                if (!audioData) throw new Error("No audio data received");

                var wav = pcmToWav(audioData, 24000);
                curBlob = wav;

                if (!aCtx) aCtx = new (window.AudioContext || window.webkitAudioContext)();
                return wav.arrayBuffer().then(function(ab) {
                    return aCtx.decodeAudioData(ab);
                }).then(function(decoded) {
                    aBuf = decoded;
                    pOff = 0;
                    playing = false;
                    showPlayer();

                    ud.quota = Math.max(0, ud.quota - 1);
                    saveData();
                    updateUI();

                    history.unshift({
                        id: Date.now(), voice: selVoice.name, voiceIcon: selVoice.icon,
                        voiceColor: selVoice.color, text: text.substring(0, 100),
                        timestamp: new Date().toLocaleTimeString(), blob: wav
                    });

                    toast("Voice generated! 🎉", "success");
                    btn.disabled = false;
                    btn.innerHTML = '<i class="fas fa-wand-magic-sparkles mr-2"></i>Create';
                });
            }).catch(function(e) {
                console.error("TTS Error:", e);
                toast(e.message || "Generation failed", "error");
                btn.disabled = false;
                btn.innerHTML = '<i class="fas fa-wand-magic-sparkles mr-2"></i>Create';
            });
        }
        tryFetch();
    }

    // ========== AUDIO PLAYER ==========
    function showPlayer() { E('player').classList.remove('hidden'); updTime(); }
    function hidePlayer() { stopAudio(); E('player').classList.add('hidden'); curBlob = null; aBuf = null; }

    function togglePlay() { if (!aBuf) return; playing ? pauseA() : playA(); }

    function playA(off) {
        if (!aBuf || !aCtx) return;
        if (aCtx.state === 'suspended') aCtx.resume();
        if (aSrc) try { aSrc.stop(); } catch(e) {}
        aSrc = aCtx.createBufferSource();
        aSrc.buffer = aBuf;
        aSrc.connect(aCtx.destination);
        var o = (off !== undefined) ? off : pOff;
        pStart = aCtx.currentTime - o;
        aSrc.start(0, o);
        playing = true;
        aSrc.onended = function() {
            if (playing) { playing = false; pOff = 0; updPlayIcon(); updTime(); cancelAnimationFrame(aFrame); }
        };
        updPlayIcon();
        animProg();
    }

    function pauseA() {
        if (aSrc && playing) {
            pOff = aCtx.currentTime - pStart;
            try { aSrc.stop(); } catch(e) {}
            playing = false;
            updPlayIcon();
            cancelAnimationFrame(aFrame);
        }
    }

    function stopAudio() {
        if (aSrc) try { aSrc.stop(); } catch(e) {}
        playing = false; pOff = 0;
        cancelAnimationFrame(aFrame);
        updPlayIcon();
    }

    function updPlayIcon() {
        E('playBtn').innerHTML = playing
            ? '<i class="fas fa-pause text-white text-lg"></i>'
            : '<i class="fas fa-play text-white text-lg ml-0.5"></i>';
    }

    function animProg() {
        if (!playing || !aBuf) return;
        var el = aCtx.currentTime - pStart;
        var dur = aBuf.duration;
        var p = Math.min(el / dur, 1);
        E('progFill').style.width = (p * 100) + '%';
        E('curTime').textContent = fmtT(el);
        E('durTime').textContent = fmtT(dur);
        if (p < 1) aFrame = requestAnimationFrame(animProg);
    }

    function updTime() {
        if (!aBuf) return;
        E('curTime').textContent = fmtT(pOff);
        E('durTime').textContent = fmtT(aBuf.duration);
        E('progFill').style.width = (pOff / aBuf.duration * 100) + '%';
    }

    function seekA(e) {
        if (!aBuf) return;
        var bar = E('progBar');
        var rect = bar.getBoundingClientRect();
        var x = e.clientX || (e.touches && e.touches[0].clientX);
        var r = Math.max(0, Math.min(1, (x - rect.left) / rect.width));
        pOff = r * aBuf.duration;
        if (playing) playA(pOff); else updTime();
    }

    function fmtT(s) { var m = Math.floor(s / 60); var sec = Math.floor(s % 60); return m + ':' + (sec < 10 ? '0' : '') + sec; }

    // ========== DOWNLOAD ==========
    function dlAudio(blob, name) {
        if (!blob) { toast("No audio", "warning"); return; }
        var reader = new FileReader();
        reader.onloadend = function() {
            var a = document.createElement('a');
            a.href = reader.result;
            a.download = name || 'ARLabs_' + selVoice.name + '_' + Date.now() + '.wav';
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            toast("Download started! 📥", "success");
        };
        reader.readAsDataURL(blob);
    }

    // ========== PROFILE ==========
    function openProfile() {
        E('profNameInput').value = ud.displayName || '';
        E('profEmail').textContent = ud.email || 'Anonymous';
        openModal('profileModal');
    }

    function saveProfile() {
        var n = E('profNameInput').value.trim();
        if (!n) { toast("Name cannot be empty", "warning"); return; }
        ud.displayName = n;
        if (user) {
            user.updateProfile({ displayName: n }).catch(function() {});
        }
        saveData();
        updateUI();
        E('profileModal').classList.remove('open');
        toast("Profile updated! ✨", "success");
    }

    function uploadAvatar(file) {
        if (!file) return;
        if (file.size > 5 * 1024 * 1024) { toast("Image too large (5MB max)", "error"); return; }
        toast("Uploading...", "info");
        var fd = new FormData();
        fd.append('image', file);
        fd.append('key', IMGBB_KEY);
        fetch('https://api.imgbb.com/1/upload', { method: 'POST', body: fd })
            .then(function(r) { return r.json(); })
            .then(function(d) {
                if (d.success) {
                    ud.photoURL = d.data.url;
                    if (user) user.updateProfile({ photoURL: d.data.url }).catch(function() {});
                    saveData(); updateUI();
                    toast("Photo updated! 📸", "success");
                } else throw new Error("Failed");
            }).catch(function() { toast("Upload failed", "error"); });
    }

    // ========== SPIN ==========
    var prizes = [7, 6, 1, 5, 4, 2, 0];

    function updateSpinBtn() {
        var btn = E('spinBtn');
        if (!btn) return;
        if (ud.lastSpinDate === todayStr()) {
            btn.disabled = true;
            btn.innerHTML = '<i class="fas fa-clock mr-2"></i>SPIN TOMORROW';
            btn.className = 'w-full py-4 rounded-2xl font-bold text-lg bg-gray-500 text-gray-300 cursor-not-allowed';
        } else {
            btn.disabled = false;
            btn.innerHTML = '<i class="fas fa-play mr-2"></i>SPIN NOW!';
            btn.className = 'w-full py-4 rounded-2xl font-bold text-lg bg-gradient-to-r from-amber-500 to-orange-600 text-white shadow-lg active:scale-95 transition-all';
        }
    }

    function doSpin() {
        if (isSpinning) return;
        if (ud.lastSpinDate === todayStr()) { toast("Already spun today!", "warning"); return; }
        isSpinning = true;
        var idx = Math.floor(Math.random() * prizes.length);
        var prize = prizes[idx];
        var seg = 360 / prizes.length;
        var target = 360 - (idx * seg + seg / 2);
        var total = 360 * 6 + target;
        var wheel = E('wheel');
        wheel.style.transition = 'transform 4s cubic-bezier(0.17,0.67,0.12,0.99)';
        wheel.style.transform = 'rotate(' + total + 'deg)';
        setTimeout(function() {
            ud.quota += prize;
            ud.lastSpinDate = todayStr();
            saveData(); updateUI();
            toast(prize > 0 ? ('🎉 Won ' + prize + ' credits!') : 'Better luck tomorrow! 😅', prize > 0 ? 'success' : 'info');
            isSpinning = false;
            updateSpinBtn();
        }, 4300);
    }

    // ========== HISTORY ==========
    function showHist() {
        var c = E('histList');
        if (history.length === 0) {
            c.innerHTML = '<div class="flex flex-col items-center py-16 text-gray-400"><i class="fas fa-clock-rotate-left text-5xl mb-4 opacity-40"></i><p class="font-medium">No generations yet</p></div>';
        } else {
            var html = '';
            history.forEach(function(h, i) {
                html += '<div class="p-4 rounded-2xl bg-white/60 dark:bg-white/5 border border-gray-200/50 dark:border-white/10">';
                html += '<div class="flex items-center gap-3 mb-2"><div class="w-9 h-9 rounded-xl bg-gradient-to-br ' + h.voiceColor + ' flex items-center justify-center"><i class="fas ' + h.voiceIcon + ' text-white text-xs"></i></div>';
                html += '<div class="flex-1"><div class="font-semibold text-sm">' + h.voice + '</div><div class="text-xs text-gray-500">' + h.timestamp + '</div></div></div>';
                html += '<p class="text-xs text-gray-600 dark:text-gray-400 mb-3 line-clamp-2">' + h.text + '</p>';
                html += '<div class="flex gap-2"><button data-hp="' + i + '" class="flex-1 py-2 rounded-xl bg-indigo-500/20 text-indigo-600 dark:text-indigo-400 text-sm font-medium"><i class="fas fa-play mr-1"></i>Play</button>';
                html += '<button data-hd="' + i + '" class="flex-1 py-2 rounded-xl bg-green-500/20 text-green-600 dark:text-green-400 text-sm font-medium"><i class="fas fa-download mr-1"></i>Save</button></div></div>';
            });
            c.innerHTML = html;
        }
        openModal('histModal');
    }

    E('histList').addEventListener('click', function(e) {
        var pb = e.target.closest('[data-hp]');
        var db = e.target.closest('[data-hd]');
        if (pb) {
            var item = history[parseInt(pb.dataset.hp)];
            if (!item || !item.blob) return;
            if (!aCtx) aCtx = new (window.AudioContext || window.webkitAudioContext)();
            if (aCtx.state === 'suspended') aCtx.resume();
            item.blob.arrayBuffer().then(function(ab) {
                return aCtx.decodeAudioData(ab);
            }).then(function(buf) {
                if (aSrc) try { aSrc.stop(); } catch(x) {}
                aSrc = aCtx.createBufferSource();
                aSrc.buffer = buf;
                aSrc.connect(aCtx.destination);
                aSrc.start(0);
                toast("Playing...", "info");
            });
        }
        if (db) {
            var item2 = history[parseInt(db.dataset.hd)];
            if (item2 && item2.blob) dlAudio(item2.blob, 'ARLabs_' + item2.voice + '_' + item2.id + '.wav');
        }
    });

    // ========== EVENTS ==========
    E('menuBtn').onclick = openMenu;
    E('menuBg').onclick = closeMenu;
    E('voiceSelBtn').onclick = toggleDD;
    E('scriptInput').oninput = function() { E('charCount').textContent = this.value.length; };
    E('clearBtn').onclick = function() { E('scriptInput').value = ''; E('charCount').textContent = '0'; };
    E('magicBtn').onclick = function() {
        E('scriptInput').value = samples[Math.floor(Math.random() * samples.length)];
        E('charCount').textContent = E('scriptInput').value.length;
        toast("Sample loaded! ✨", "success");
    };
    E('createBtn').onclick = doGenerate;
    E('playBtn').onclick = togglePlay;
    E('progBar').onclick = seekA;
    E('dlBtn').onclick = function() { dlAudio(curBlob); };
    E('closePlayerBtn').onclick = hidePlayer;
    E('profileMenuBtn').onclick = openProfile;
    E('spinMenuBtn').onclick = function() { openModal('spinModal'); updateSpinBtn(); };
    E('histMenuBtn').onclick = showHist;
    E('aboutMenuBtn').onclick = function() { openModal('aboutModal'); };
    E('reportMenuBtn').onclick = function() { openModal('reportModal'); };
    E('saveProfileBtn').onclick = saveProfile;
    E('avUploadBtn').onclick = function() { E('avFileInput').click(); };
    E('avFileInput').onchange = function() { uploadAvatar(this.files[0]); };
    E('spinBtn').onclick = doSpin;
    E('submitReportBtn').onclick = function() {
        var t = E('reportText').value.trim();
        if (!t) { toast("Please describe the issue", "warning"); return; }
        toast("Report submitted! 🙏", "success");
        E('reportText').value = '';
        E('reportModal').classList.remove('open');
    };
    E('themeToggle').onchange = function() { dark = !dark; applyTheme(); };

    // ========== INIT ==========
    initTheme();
    renderVoices();

    // Start auth after a small delay to ensure Firebase SDK is ready
    setTimeout(function() {
        try {
            initAuth();
        } catch(e) {
            console.error("Init auth error:", e);
            hideSplash();
        }
    }, 500);

    // Failsafe: if splash is still showing after 5s, force hide
    setTimeout(function() {
        var s = E('splash');
        if (s) {
            console.log("Failsafe: removing splash");
            s.style.opacity = '0';
            setTimeout(function() {
                if (s.parentNode) s.parentNode.removeChild(s);
                E('app').style.opacity = '1';
            }, 700);
        }
    }, 5000);

})();
</script>
</body>
</html>
