<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>きせかえウェザー - 気温別お出かけ服装ナビ</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&family=Zen+Maru+Gothic:wght@400;500;700&display=swap" rel="stylesheet">
    
    <style>
        body {
            font-family: 'Zen Maru Gothic', 'Plus Jakarta Sans', sans-serif;
            transition: background 0.6s ease, color 0.4s ease;
        }

        /* Custom range slider styling */
        input[type=range] {
            -webkit-appearance: none;
            width: 100%;
            background: transparent;
        }
        input[type=range]:focus {
            outline: none;
        }
        input[type=range]::-webkit-slider-thumb {
            -webkit-appearance: none;
            height: 28px;
            width: 28px;
            border-radius: 50%;
            background: #ffffff;
            cursor: pointer;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.25);
            margin-top: -10px;
            transition: transform 0.15s ease, box-shadow 0.15s ease;
        }
        input[type=range]::-webkit-slider-thumb:hover {
            transform: scale(1.15);
            box-shadow: 0 6px 14px rgba(0, 0, 0, 0.3);
        }
        input[type=range]::-webkit-slider-runnable-track {
            width: 100%;
            height: 8px;
            cursor: pointer;
            border-radius: 9999px;
            background: rgba(255, 255, 255, 0.45);
            backdrop-filter: blur(4px);
        }

        /* Glassmorphism Card Effect */
        .glass-panel {
            background: rgba(255, 255, 255, 0.85);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.5);
        }

        .glass-card {
            background: rgba(255, 255, 255, 0.92);
            backdrop-filter: blur(8px);
            border: 1px solid rgba(255, 255, 255, 0.6);
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .glass-card:hover {
            transform: translateY(-3px);
            box-shadow: 0 12px 24px -6px rgba(0, 0, 0, 0.08);
        }

        /* Smooth tab animations */
        .tab-btn {
            transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
        }

        /* Custom scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(0,0,0,0.05);
            border-radius: 8px;
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(0,0,0,0.15);
            border-radius: 8px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: rgba(0,0,0,0.25);
        }

        /* Custom subtle pulsing animation for active status */
        @keyframes subtlePulse {
            0%, 100% { opacity: 1; transform: scale(1); }
            50% { opacity: 0.85; transform: scale(1.02); }
        }
        .pulse-badge {
            animation: subtlePulse 3s infinite ease-in-out;
        }
    </style>
</head>
<body class="min-h-screen text-slate-800 antialiased selection:bg-indigo-500 selection:text-white pb-12" id="appBody">

    <!-- Header Navigation -->
    <header class="w-full max-w-5xl mx-auto px-4 pt-6 pb-2">
        <div class="glass-panel rounded-2xl px-5 py-4 flex flex-wrap justify-between items-center shadow-lg gap-3">
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-amber-400 to-rose-500 flex items-center justify-center text-white shadow-md">
                    <i data-lucide="shirt" class="w-6 h-6"></i>
                </div>
                <div>
                    <h1 class="font-bold text-lg leading-tight tracking-wide text-slate-900">きせかえウェザー</h1>
                    <p class="text-xs text-slate-500 font-medium">気温で迷わないお出かけ服ナビ</p>
                </div>
            </div>

            <!-- Header Action buttons -->
            <div class="flex items-center space-x-2">
                <button id="openFavoritesBtn" class="flex items-center space-x-1.5 px-3.5 py-2 rounded-xl bg-white/70 hover:bg-white text-slate-700 text-xs font-semibold shadow-sm border border-slate-200/60 transition-all active:scale-95">
                    <i data-lucide="bookmark" class="w-4 h-4 text-rose-500"></i>
                    <span>保存した服 (<span id="favCount">0</span>)</span>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="max-w-5xl mx-auto px-4 mt-4 space-y-6">

        <!-- Temperature Input & Dynamic Control Panel -->
        <section class="glass-panel rounded-3xl p-6 sm:p-8 shadow-xl relative overflow-hidden">
            <!-- Background Decorative Elements -->
            <div class="absolute -right-12 -top-12 w-48 h-48 rounded-full bg-white/30 blur-2xl pointer-events-none"></div>

            <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-center">
                
                <!-- Left: Big Temperature Display -->
                <div class="lg:col-span-5 flex flex-col items-center justify-center text-center p-4 rounded-2xl bg-white/40 border border-white/60 shadow-inner">
                    <div class="flex items-center justify-center space-x-2 mb-1">
                        <span id="seasonBadge" class="px-3 py-1 rounded-full text-xs font-bold uppercase tracking-wider text-white bg-slate-700 shadow-sm pulse-badge">
                            春・秋
                        </span>
                        <span id="timeContextBadge" class="px-2.5 py-1 rounded-full text-xs font-medium text-slate-600 bg-white/80 border border-slate-200">
                            昼間標準
                        </span>
                    </div>

                    <div class="flex items-baseline justify-center my-2 select-none">
                        <span id="tempValueDisplay" class="text-7xl sm:text-8xl font-black tracking-tighter text-slate-900">20</span>
                        <span class="text-3xl sm:text-4xl font-bold text-slate-600 ml-1">°C</span>
                    </div>

                    <!-- Feels like adjustment info -->
                    <div class="text-xs font-medium text-slate-600 flex items-center gap-1.5 bg-white/60 px-3 py-1.5 rounded-lg border border-white/80">
                        <i data-lucide="thermometer-snowflake" class="w-3.5 h-3.5 text-sky-500"></i>
                        <span>体感温度: 約 <strong id="feelsLikeDisplay" class="text-slate-800">20</strong>°C</span>
                    </div>
                </div>

                <!-- Right: Interactive Controls -->
                <div class="lg:col-span-7 space-y-6">
                    
                    <!-- Slider Block -->
                    <div class="space-y-3">
                        <div class="flex justify-between items-center text-sm font-bold text-slate-700">
                            <label for="tempSlider" class="flex items-center gap-1.5">
                                <i data-lucide="sliders" class="w-4 h-4 text-indigo-500"></i>
                                気温を調整する
                            </label>
                            <span class="text-xs font-normal text-slate-500">スライダーを左右に動かしてください</span>
                        </div>
                        <div class="relative pt-2 pb-1">
                            <input type="range" id="tempSlider" min="-5" max="38" value="20" step="1">
                        </div>
                        <div class="flex justify-between text-[11px] font-semibold text-slate-600 px-1">
                            <span>-5°C (極寒)</span>
                            <span>10°C</span>
                            <span>20°C</span>
                            <span>30°C</span>
                            <span>38°C (猛暑)</span>
                        </div>
                    </div>

                    <!-- Preset Temperature Range Buttons -->
                    <div>
                        <p class="text-xs font-bold text-slate-600 mb-2 flex items-center gap-1">
                            <i data-lucide="sparkles" class="w-3.5 h-3.5 text-amber-500"></i>
                            目安カテゴリから一括選択
                        </p>
                        <div class="grid grid-cols-2 sm:grid-cols-4 gap-2" id="presetButtons">
                            <!-- JS will generate or populate these -->
                        </div>
                    </div>

                    <!-- Situation Modifiers (Night, Wind) -->
                    <div class="pt-2 border-t border-slate-200/60 flex flex-wrap items-center justify-between gap-3">
                        <span class="text-xs font-bold text-slate-600 flex items-center gap-1">
                            <i data-lucide="cloud-sun" class="w-4 h-4 text-indigo-500"></i>
                            条件補正:
                        </span>
                        
                        <div class="flex flex-wrap items-center gap-2">
                            <!-- Time Toggle -->
                            <button id="toggleNight" class="px-3 py-1.5 rounded-xl text-xs font-semibold bg-white/70 hover:bg-white text-slate-700 border border-slate-200 flex items-center gap-1.5 transition-all">
                                <i data-lucide="moon" class="w-3.5 h-3.5 text-indigo-400"></i>
                                <span>夜間・早朝 (-2°C)</span>
                            </button>

                            <!-- Wind Toggle -->
                            <button id="toggleWind" class="px-3 py-1.5 rounded-xl text-xs font-semibold bg-white/70 hover:bg-white text-slate-700 border border-slate-200 flex items-center gap-1.5 transition-all">
                                <i data-lucide="wind" class="w-3.5 h-3.5 text-sky-400"></i>
                                <span>風が強い (-3°C)</span>
                            </button>
                        </div>
                    </div>

                </div>
            </div>
        </section>

        <!-- Dynamic Recommendation Content Header -->
        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3">
            <div>
                <h2 class="text-xl sm:text-2xl font-black text-slate-900 flex items-center gap-2">
                    <span id="outfitHeadingIcon">✨</span>
                    <span id="outfitHeadingTitle">快適な過ごしやすさ</span>
                </h2>
                <p id="outfitSummaryText" class="text-sm text-slate-600 font-medium mt-0.5">長袖シャツや軽いカーディガンがベストな季節です。</p>
            </div>
            
            <button id="saveCurrentOutfitBtn" class="px-4 py-2.5 rounded-2xl bg-slate-900 hover:bg-slate-800 text-white text-xs font-bold shadow-md hover:shadow-lg transition-all active:scale-95 flex items-center gap-2">
                <i data-lucide="heart" class="w-4 h-4 text-rose-400 fill-rose-400"></i>
                <span>このコーディネートを保存</span>
            </button>
        </div>

        <!-- Main Outfit Cards Grid -->
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4" id="cardsGrid">
            
            <!-- Tops Card -->
            <div class="glass-card rounded-2xl p-5 flex flex-col justify-between">
                <div>
                    <div class="flex items-center justify-between mb-3">
                        <span class="text-xs font-bold px-2.5 py-1 rounded-lg bg-indigo-50 text-indigo-700 border border-indigo-100 flex items-center gap-1">
                            <i data-lucide="shirt" class="w-3.5 h-3.5"></i> トップス
                        </span>
                        <span class="text-2xl" id="topsEmoji">👕</span>
                    </div>
                    <h3 class="font-bold text-slate-900 text-base mb-1" id="topsTitle">長袖シャツ / カットソー</h3>
                    <p class="text-xs text-slate-600 leading-relaxed" id="topsDesc">薄手の長袖トップス1枚で快適に過ごせる気温です。袖まくりで調整も可能。</p>
                </div>
                <div class="mt-4 pt-3 border-t border-slate-100 flex items-center gap-1.5 text-[11px] font-semibold text-indigo-600" id="topsTag">
                    <i data-lucide="check-circle-2" class="w-3.5 h-3.5"></i>
                    <span>さらっと羽織れる素材が◎</span>
                </div>
            </div>

            <!-- Bottoms Card -->
            <div class="glass-card rounded-2xl p-5 flex flex-col justify-between">
                <div>
                    <div class="flex items-center justify-between mb-3">
                        <span class="text-xs font-bold px-2.5 py-1 rounded-lg bg-emerald-50 text-emerald-700 border border-emerald-100 flex items-center gap-1">
                            <i data-lucide="scissors" class="w-3.5 h-3.5"></i> ボトムス
                        </span>
                        <span class="text-2xl" id="bottomsEmoji">👖</span>
                    </div>
                    <h3 class="font-bold text-slate-900 text-base mb-1" id="bottomsTitle">ジーンズ / チノパン</h3>
                    <p class="text-xs text-slate-600 leading-relaxed" id="bottomsDesc">標準的なロングパンツ全般が最適。スラックスやスカートでもちょうど良い季節。</p>
                </div>
                <div class="mt-4 pt-3 border-t border-slate-100 flex items-center gap-1.5 text-[11px] font-semibold text-emerald-600" id="bottomsTag">
                    <i data-lucide="check-circle-2" class="w-3.5 h-3.5"></i>
                    <span>通年素材のパンツがおすすめ</span>
                </div>
            </div>

            <!-- Outerwear Card -->
            <div class="glass-card rounded-2xl p-5 flex flex-col justify-between">
                <div>
                    <div class="flex items-center justify-between mb-3">
                        <span class="text-xs font-bold px-2.5 py-1 rounded-lg bg-amber-50 text-amber-700 border border-amber-100 flex items-center gap-1">
                            <i data-lucide="cloud-sun" class="w-3.5 h-3.5"></i> 羽織り・アウター
                        </span>
                        <span class="text-2xl" id="outerEmoji">🧥</span>
                    </div>
                    <h3 class="font-bold text-slate-900 text-base mb-1" id="outerTitle">カーディガン / 薄手パーカー</h3>
                    <p class="text-xs text-slate-600 leading-relaxed" id="outerDesc">朝晩の温度変化に備えて、鞄に入れて持ち運べる軽めの上着があると安心です。</p>
                </div>
                <div class="mt-4 pt-3 border-t border-slate-100 flex items-center gap-1.5 text-[11px] font-semibold text-amber-600" id="outerTag">
                    <i data-lucide="check-circle-2" class="w-3.5 h-3.5"></i>
                    <span>脱着しやすいものが重宝</span>
                </div>
            </div>

            <!-- Accessories & Layering Tip Card -->
            <div class="glass-card rounded-2xl p-5 flex flex-col justify-between">
                <div>
                    <div class="flex items-center justify-between mb-3">
                        <span class="text-xs font-bold px-2.5 py-1 rounded-lg bg-purple-50 text-purple-700 border border-purple-100 flex items-center gap-1">
                            <i data-lucide="sparkles" class="w-3.5 h-3.5"></i> 小物・インナー
                        </span>
                        <span class="text-2xl" id="accEmoji">🎒</span>
                    </div>
                    <h3 class="font-bold text-slate-900 text-base mb-1" id="accTitle">標準インナー・スニーカー</h3>
                    <p class="text-xs text-slate-600 leading-relaxed" id="accDesc">一般的な綿インナーで十分です。靴はソックス＋スニーカーなどで快適に過ごせます。</p>
                </div>
                <div class="mt-4 pt-3 border-t border-slate-100 flex items-center gap-1.5 text-[11px] font-semibold text-purple-600" id="accTag">
                    <i data-lucide="check-circle-2" class="w-3.5 h-3.5"></i>
                    <span>紫外線対策のサングラスも有効</span>
                </div>
            </div>

        </div>

        <!-- Layering Tips Detailed Banner -->
        <div class="glass-panel rounded-3xl p-6 shadow-lg border border-white/60">
            <div class="flex items-start gap-4">
                <div class="w-12 h-12 rounded-2xl bg-indigo-500 text-white flex items-center justify-center shrink-0 shadow-md">
                    <i data-lucide="layers" class="w-6 h-6"></i>
                </div>
                <div class="space-y-1">
                    <h4 class="font-bold text-slate-900 text-base flex items-center gap-2">
                        着こなしアドバイス & ポイント
                    </h4>
                    <p id="detailedTipText" class="text-xs sm:text-sm text-slate-600 leading-relaxed">
                        日中と朝晩の寒暖差が出やすい気温です。脱ぎ着しやすい前開きのカーディガンやライトアウターを持参すると、1日通して快適に過ごせます。
                    </p>
                </div>
            </div>
        </div>

    </main>

    <!-- Modal for Saved Outfits Log -->
    <div id="favoritesModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden transition-opacity duration-300 opacity-0">
        <div class="glass-panel w-full max-w-lg rounded-3xl shadow-2xl border border-white/80 overflow-hidden flex flex-col max-h-[85vh]">
            
            <!-- Modal Header -->
            <div class="px-6 py-4 border-b border-slate-200/60 flex items-center justify-between bg-white/50">
                <div class="flex items-center gap-2">
                    <i data-lucide="bookmark" class="w-5 h-5 text-rose-500"></i>
                    <h3 class="font-bold text-slate-900 text-base">お気に入りコーディネート一覧</h3>
                </div>
                <button id="closeFavoritesBtn" class="p-1.5 rounded-full hover:bg-slate-200/60 text-slate-500 transition-colors">
                    <i data-lucide="x" class="w-5 h-5"></i>
                </button>
            </div>

            <!-- Modal Content (List) -->
            <div id="favoritesList" class="p-6 overflow-y-auto space-y-3 flex-1">
                <!-- JS will inject saved cards here -->
            </div>

            <!-- Modal Footer -->
            <div class="px-6 py-3 border-t border-slate-200/60 bg-white/40 flex justify-between items-center">
                <button id="clearAllFavsBtn" class="text-xs font-semibold text-rose-500 hover:text-rose-700 transition-colors">
                    すべて削除
                </button>
                <button id="closeFavoritesFooterBtn" class="px-4 py-2 rounded-xl bg-slate-800 text-white text-xs font-bold hover:bg-slate-700 transition-all">
                    閉じる
                </button>
            </div>
        </div>
    </div>

    <!-- Notification Toast -->
    <div id="toast" class="fixed bottom-6 right-6 bg-slate-900 text-white px-4 py-3 rounded-2xl shadow-xl flex items-center gap-2 text-xs font-bold transition-all duration-300 transform translate-y-12 opacity-0 z-50 pointer-events-none">
        <i data-lucide="check-circle" class="w-4 h-4 text-emerald-400"></i>
        <span id="toastMessage">保存しました</span>
    </div>

    <!-- JavaScript Application Logic -->
    <script>
        // Data structure for temperature ranges and recommendations
        const OUTFIT_RULES = [
            {
                min: 31,
                max: 50,
                season: "酷暑・猛暑",
                seasonBg: "bg-red-600",
                themeGradient: "linear-gradient(135deg, #fecaca 0%, #fca5a5 50%, #f87171 100%)",
                icon: "🔥",
                title: "猛暑日！最軽量スタイル",
                summary: "熱中症注意。半袖・ノースリーブ等、通気性と吸水速乾性を最優先に。",
                tops: { title: "半袖Tシャツ・ノースリーブ", desc: "コットンやリネン、ドライ機能素材のトップス。", emoji: "👕", tag: "通気性・吸湿速乾重視" },
                bottoms: { title: "ショートパンツ / 薄手スカート", desc: "風通しの良いハーフパンツや涼しいリネン素材。", emoji: "🩳", tag: "涼しさと軽さ第一" },
                outer: { title: "不要（日焼け止め羽織）", desc: "基本不要。冷房対策や日焼け防止に極薄のUVカットシャツも可。", emoji: "☀️", tag: "屋外では着用レスが基本" },
                accessories: { title: "日傘・帽子・サングラス", desc: "熱中症対策・紫外線対策グッズが必須。水分補給を怠らず。", emoji: "🧢", tag: "熱中症予防グッズ推奨" },
                tip: "できるだけ軽い素材を着用し、室内での強いエアコン対策として薄い手拭いやUVカットパーカーを持参するのが賢明です。"
            },
            {
                min: 26,
                max: 30,
                season: "夏日",
                seasonBg: "bg-orange-500",
                themeGradient: "linear-gradient(135deg, #fed7aa 0%, #fdba74 50%, #fb923c 100%)",
                icon: "☀️",
                title: "夏服半袖シーズン",
                summary: "半袖1枚で1日快適。屋内エアコンの冷えに少し気を配る気温。",
                tops: { title: "半袖Tシャツ / ポロシャツ", desc: "1枚でサマになるカットソーやブラウスがおすすめ。", emoji: "👕", tag: "半袖トップスの本番" },
                bottoms: { title: "薄手のロングパンツ / 短パン", desc: "薄手デニム、スラックス、涼しげなワイドパンツ。", emoji: "👖", tag: "足元も軽快に" },
                outer: { title: "薄手カーディガン（冷房対策）", desc: "オフィスや電車内の冷え対策用にサッと羽織れるものを1枚。", emoji: "👔", tag: "室内冷え対策にあると安心" },
                accessories: { title: "サンダル / スニーカー", desc: "素足で履けるサンダルや通気性の良いスニーカー。", emoji: "👡", tag: "涼しい足元" },
                tip: "外は暑くても室内は冷房がしっかり効いていることが多いです。持ち運びやすい薄手のサマーニットやシャツがあると快適です。"
            },
            {
                min: 21,
                max: 25,
                season: "初夏・初秋",
                seasonBg: "bg-amber-500",
                themeGradient: "linear-gradient(135deg, #fef08a 0%, #fde047 50%, #facc15 100%)",
                icon: "🍃",
                title: "快適な長袖 / 半袖＋羽織り",
                summary: "過ごしやすい気候。半袖に軽い羽織り、または薄手長袖1枚がジャスト。",
                tops: { title: "長袖Tシャツ / 薄手シャツ", desc: "7分袖や長袖の軽やかなカットソーが気持ち良い気候。", emoji: "👔", tag: "1枚着でちょうど良い" },
                bottoms: { title: "チノパン / デニム / ロングスカート", desc: "一般的な通年履けるボトムスなら何でも合わせやすい。", emoji: "👖", tag: "お好みのロング丈" },
                outer: { title: "長袖シャツ / リネンジャケット", desc: "ボタンを開けて羽織れるシャツやライトカーディガン。", emoji: "🧥", tag: "体温調節しやすい着こなし" },
                accessories: { title: "スニーカー / ローファー", desc: "快適に歩ける靴全般。秋口ならソックスのおしゃれも。", emoji: "👟", tag: "快適な歩きやすさ" },
                tip: "1年の中で最も服選びが楽しい快適な気温です。昼間は半袖、夕方からシャツを羽織るようなフレキシブルな重ね着が向いています。"
            },
            {
                min: 16,
                max: 20,
                season: "秋・春",
                seasonBg: "bg-emerald-600",
                themeGradient: "linear-gradient(135deg, #a7f3d0 0%, #6ee7b7 50%, #34d399 100%)",
                icon: "🍁",
                title: "重ね着が楽しい秋・春服",
                summary: "少し肌寒さを感じる気温。カーディガンやパーカー、ライトアウターが必須。",
                tops: { title: "長袖スウェット / 薄手ニット", desc: "しっかりめの長袖ロンTや、軽いニット・トレーナー。", emoji: "👕", tag: "少し厚手の生地感" },
                bottoms: { title: "ジーンズ / 厚手ロングパンツ", desc: "標準〜ややしっかりめの生地のパンツ類。", emoji: "👖", tag: "冷えを防ぐロング丈" },
                outer: { title: "カーディガン / パーカー / マウンテンパーカー", desc: "サッと羽織れて風を防げるライトアウター。", emoji: "🧥", tag: "ライトアウター必須" },
                accessories: { title: "ソックス＋スニーカー / ブーツ", desc: "しっかり足元を温めるソックス選び。", emoji: "🧦", tag: "足元からの冷え対策" },
                tip: "朝晩と日中の気温差が一番大きいゾーンです。前開きのパーカーやジップジャケットでこまめに温度調節しましょう。"
            },
            {
                min: 12,
                max: 15,
                season: "晩秋・早春",
                seasonBg: "bg-teal-600",
                themeGradient: "linear-gradient(135deg, #99f6e4 0%, #2dd4bf 50%, #14b8a6 100%)",
                icon: "🍂",
                title: "しっかりめアウターの出番",
                summary: "秋から冬への移り変わり。トレンチコートやジャケットが活躍します。",
                tops: { title: "ニット / セーター / スウェット", desc: "中厚手のセーターやウォーム感のあるトップス。", emoji: "🧶", tag: "保温性のあるトップス" },
                bottoms: { title: "暖かパンツ / コーデュロイ", desc: "厚手のデニム、コーデュロイ、裏起毛パンツなど。", emoji: "👖", tag: "しっかりした生地" },
                outer: { title: "トレンチコート / レザージャケット / ブルゾン", desc: "秋・冬物アウターの本番。風を遮るしっかりしたアウター。", emoji: "🧥", tag: "本格アウター着用" },
                accessories: { title: "薄手ストール / 秋物帽子", desc: "首元を温める薄手のストールやマフラーも効果的。", emoji: "🧣", tag: "首元の防寒スタート" },
                tip: "風が吹くと一気に寒く感じます。インナーに保温性下着（ヒートテック等）を仕込み始めると安心です。"
            },
            {
                min: 7,
                max: 11,
                season: "初冬",
                seasonBg: "bg-sky-600",
                themeGradient: "linear-gradient(135deg, #bae6fd 0%, #38bdf8 50%, #0284c7 100%)",
                icon: "🌬️",
                title: "冬本番！冬用コート必須",
                summary: "寒さが本格化。ウールコートやチェスターコートで防寒対策を徹底。",
                tops: { title: "厚手ニット / タートルネック", desc: "しっかり温かいウールやカシミヤのセーター。", emoji: "🧶", tag: "首元まで暖かく" },
                bottoms: { title: "裏起毛スラックス / タイツ併用", desc: "厚手のパンツに、必要に応じてタイツやレギンスを重ねる。", emoji: "👖", tag: "下半身の防寒も意識" },
                outer: { title: "ウールコート / ショートダウン / チェスターコート", desc: "しっかりと保温効果のある冬物コート。", emoji: "🧥", tag: "冬用アウター絶対" },
                accessories: { title: "マフラー / 手袋", desc: "風を防ぐマフラーや手袋などの防寒小物をプラス。", emoji: "🧣", tag: "防寒小物で隙間をガード" },
                tip: "外の寒さと室内の暖房の差が大きいです。コートの中は脱ぎ着しやすいニット＋インナーの組み合わせがベスト。"
            },
            {
                min: -20,
                max: 6,
                season: "厳冬・極寒",
                seasonBg: "bg-indigo-700",
                themeGradient: "linear-gradient(135deg, #c7d2fe 0%, #818cf8 50%, #4f46e5 100%)",
                icon: "❄️",
                title: "極寒！厚手ダウン＆防寒フル装備",
                summary: "冷え込みが厳しい日。高機能インナーとダウンジャケットで防寒を最優先。",
                tops: { title: "発熱インナー ＋ 厚手ニット", desc: "保温インナー（ヒートテック等）＋厚手のウールセーター。", emoji: "❄️", tag: "保温インナー必須" },
                bottoms: { title: "防風パンツ / タイツ＋厚手パンツ", desc: "風を通さない裏フリースパンツやタイツの重着。", emoji: "👖", tag: "重ね履き推奨" },
                outer: { title: "厚手ダウンジャケット / ロングダウン", desc: "防風・防寒性に優れた最強アウター。", emoji: "🧥", tag: "ダウンでしっかり保温" },
                accessories: { title: "厚手マフラー・手袋・ニット帽", desc: "露出部位を極力減らす防寒3点セット。", emoji: "🧤", tag: "フル装備で防寒" },
                tip: "冷気は首・手首・足首から侵入します。マフラー・手袋・長めの靴下で「3つの首」をしっかり温めることがポイントです。"
            }
        ];

        // Preset button values
        const PRESETS = [
            { label: "30°C~ 猛暑", temp: 31 },
            { label: "26°C 夏日", temp: 28 },
            { label: "22°C 快適", temp: 22 },
            { label: "17°C 肌寒い", temp: 17 },
            { label: "13°C 上着要", temp: 14 },
            { label: "9°C 冬コート", temp: 9 },
            { label: "5°C以下 極寒", temp: 4 }
        ];

        // State Management
        let currentTemp = 20;
        let isNight = false;
        let isWindy = false;
        let savedFavorites = JSON.parse(localStorage.getItem('outfit_favs') || '[]');

        // DOM Elements
        const appBody = document.getElementById('appBody');
        const tempSlider = document.getElementById('tempSlider');
        const tempValueDisplay = document.getElementById('tempValueDisplay');
        const feelsLikeDisplay = document.getElementById('feelsLikeDisplay');
        const seasonBadge = document.getElementById('seasonBadge');
        const timeContextBadge = document.getElementById('timeContextBadge');
        const presetButtonsContainer = document.getElementById('presetButtons');
        
        const toggleNightBtn = document.getElementById('toggleNight');
        const toggleWindBtn = document.getElementById('toggleWind');

        const outfitHeadingIcon = document.getElementById('outfitHeadingIcon');
        const outfitHeadingTitle = document.getElementById('outfitHeadingTitle');
        const outfitSummaryText = document.getElementById('outfitSummaryText');

        // Card Elements
        const topsTitle = document.getElementById('topsTitle');
        const topsDesc = document.getElementById('topsDesc');
        const topsEmoji = document.getElementById('topsEmoji');
        const topsTag = document.getElementById('topsTag');

        const bottomsTitle = document.getElementById('bottomsTitle');
        const bottomsDesc = document.getElementById('bottomsDesc');
        const bottomsEmoji = document.getElementById('bottomsEmoji');
        const bottomsTag = document.getElementById('bottomsTag');

        const outerTitle = document.getElementById('outerTitle');
        const outerDesc = document.getElementById('outerDesc');
        const outerEmoji = document.getElementById('outerEmoji');
        const outerTag = document.getElementById('outerTag');

        const accTitle = document.getElementById('accTitle');
        const accDesc = document.getElementById('accDesc');
        const accEmoji = document.getElementById('accEmoji');
        const accTag = document.getElementById('accTag');

        const detailedTipText = document.getElementById('detailedTipText');
        
        // Favorite Modal Elements
        const openFavoritesBtn = document.getElementById('openFavoritesBtn');
        const closeFavoritesBtn = document.getElementById('closeFavoritesBtn');
        const closeFavoritesFooterBtn = document.getElementById('closeFavoritesFooterBtn');
        const favoritesModal = document.getElementById('favoritesModal');
        const favoritesList = document.getElementById('favoritesList');
        const favCount = document.getElementById('favCount');
        const saveCurrentOutfitBtn = document.getElementById('saveCurrentOutfitBtn');
        const clearAllFavsBtn = document.getElementById('clearAllFavsBtn');

        // Toast Element
        const toast = document.getElementById('toast');
        const toastMessage = document.getElementById('toastMessage');

        // Initialize Presets UI
        function initPresets() {
            presetButtonsContainer.innerHTML = '';
            PRESETS.forEach(p => {
                const btn = document.createElement('button');
                btn.className = 'px-2.5 py-1.5 rounded-xl text-xs font-semibold bg-white/70 hover:bg-white text-slate-700 border border-slate-200/80 transition-all active:scale-95 text-center shadow-sm';
                btn.innerText = p.label;
                btn.onclick = () => {
                    tempSlider.value = p.temp;
                    updateApp();
                };
                presetButtonsContainer.appendChild(btn);
            });
        }

        // Calculate Effective / Feels like Temp
        function getFeelsLikeTemp() {
            let temp = currentTemp;
            if (isNight) temp -= 2;
            if (isWindy) temp -= 3;
            return temp;
        }

        // Main Update Function
        function updateApp() {
            currentTemp = parseInt(tempSlider.value, 10);
            const feelsLike = getFeelsLikeTemp();

            // Find matching rule based on FEELS LIKE temperature
            const rule = OUTFIT_RULES.find(r => feelsLike >= r.min && feelsLike <= r.max) || OUTFIT_RULES[OUTFIT_RULES.length - 1];

            // Update Temperature Text
            tempValueDisplay.innerText = currentTemp;
            feelsLikeDisplay.innerText = feelsLike;

            // Update Theme Background Gradient
            appBody.style.background = rule.themeGradient;

            // Update Badges
            seasonBadge.innerText = rule.season;
            seasonBadge.className = `px-3 py-1 rounded-full text-xs font-bold uppercase tracking-wider text-white shadow-sm pulse-badge ${rule.seasonBg}`;

            let contextText = "昼間標準";
            if (isNight && isWindy) contextText = "夜間 & 強風";
            else if (isNight) contextText = "夜間・早朝";
            else if (isWindy) contextText = "風が強め";
            timeContextBadge.innerText = contextText;

            // Update Headings
            outfitHeadingIcon.innerText = rule.icon;
            outfitHeadingTitle.innerText = rule.title;
            outfitSummaryText.innerText = rule.summary;

            // Update Cards Content
            topsTitle.innerText = rule.tops.title;
            topsDesc.innerText = rule.tops.desc;
            topsEmoji.innerText = rule.tops.emoji;
            topsTag.querySelector('span').innerText = rule.tops.tag;

            bottomsTitle.innerText = rule.bottoms.title;
            bottomsDesc.innerText = rule.bottoms.desc;
            bottomsEmoji.innerText = rule.bottoms.emoji;
            bottomsTag.querySelector('span').innerText = rule.bottoms.tag;

            outerTitle.innerText = rule.outer.title;
            outerDesc.innerText = rule.outer.desc;
            outerEmoji.innerText = rule.outer.emoji;
            outerTag.querySelector('span').innerText = rule.outer.tag;

            accTitle.innerText = rule.acc.title || rule.accessories.title;
            accDesc.innerText = rule.acc.desc || rule.accessories.desc;
            accEmoji.innerText = rule.acc.emoji || rule.accessories.emoji;
            accTag.querySelector('span').innerText = rule.acc.tag || rule.accessories.tag;

            // Update Detailed Tip
            detailedTipText.innerText = rule.tip;

            // Re-render lucide icons if needed
            if (window.lucide) {
                lucide.createIcons();
            }
        }

        // Toggle Buttons Handling
        toggleNightBtn.onclick = () => {
            isNight = !isNight;
            toggleNightBtn.classList.toggle('bg-indigo-600', isNight);
            toggleNightBtn.classList.toggle('text-white', isNight);
            toggleNightBtn.classList.toggle('bg-white/70', !isNight);
            toggleNightBtn.classList.toggle('text-slate-700', !isNight);
            updateApp();
        };

        toggleWindBtn.onclick = () => {
            isWindy = !isWindy;
            toggleWindBtn.classList.toggle('bg-sky-600', isWindy);
            toggleWindBtn.classList.toggle('text-white', isWindy);
            toggleWindBtn.classList.toggle('bg-white/70', !isWindy);
            toggleWindBtn.classList.toggle('text-slate-700', !isWindy);
            updateApp();
        };

        // Slider Input Event
        tempSlider.oninput = updateApp;

        // Toast Notification System
        function showToast(msg) {
            toastMessage.innerText = msg;
            toast.classList.remove('translate-y-12', 'opacity-0');
            toast.classList.add('translate-y-0', 'opacity-100');
            setTimeout(() => {
                toast.classList.remove('translate-y-0', 'opacity-100');
                toast.classList.add('translate-y-12', 'opacity-0');
            }, 2500);
        }

        // Favorites / Saved Outfits Logic
        function updateFavCount() {
            favCount.innerText = savedFavorites.length;
        }

        function saveCurrentOutfit() {
            const feelsLike = getFeelsLikeTemp();
            const rule = OUTFIT_RULES.find(r => feelsLike >= r.min && feelsLike <= r.max) || OUTFIT_RULES[OUTFIT_RULES.length - 1];
            
            const newFav = {
                id: Date.now(),
                temp: currentTemp,
                feelsLike: feelsLike,
                season: rule.season,
                title: rule.title,
                tops: rule.tops.title,
                outer: rule.outer.title,
                date: new Date().toLocaleDateString('ja-JP', { month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' })
            };

            savedFavorites.unshift(newFav);
            localStorage.setItem('outfit_favs', JSON.stringify(savedFavorites));
            updateFavCount();
            showToast('お気に入りに保存しました！');
        }

        function renderFavoritesList() {
            favoritesList.innerHTML = '';
            if (savedFavorites.length === 0) {
                favoritesList.innerHTML = `
                    <div class="text-center py-10 text-slate-400">
                        <i data-lucide="bookmark-x" class="w-12 h-12 mx-auto mb-2 opacity-50"></i>
                        <p class="text-sm font-medium">まだ保存されたコーディネートはありません</p>
                    </div>
                `;
                lucide.createIcons();
                return;
            }

            savedFavorites.forEach(fav => {
                const item = document.createElement('div');
                item.className = 'p-4 rounded-2xl bg-white/80 border border-slate-200/70 shadow-sm flex items-center justify-between gap-3 hover:bg-white transition-all';
                item.innerHTML = `
                    <div class="space-y-1">
                        <div class="flex items-center gap-2">
                            <span class="text-xs font-bold px-2 py-0.5 rounded-md bg-slate-900 text-white">${fav.temp}°C</span>
                            <span class="text-xs text-slate-500 font-medium">${fav.date}</span>
                        </div>
                        <h4 class="font-bold text-slate-800 text-sm">${fav.title}</h4>
                        <p class="text-xs text-slate-600">👚 ${fav.tops} / 🧥 ${fav.outer}</p>
                    </div>
                    <button data-id="${fav.id}" class="delete-fav-btn p-2 rounded-xl text-slate-400 hover:text-rose-500 hover:bg-rose-50 transition-colors">
                        <i data-lucide="trash-2" class="w-4 h-4"></i>
                    </button>
                `;
                favoritesList.appendChild(item);
            });

            // Attach delete listeners
            document.querySelectorAll('.delete-fav-btn').forEach(btn => {
                btn.onclick = (e) => {
                    const id = parseInt(e.currentTarget.getAttribute('data-id'), 10);
                    savedFavorites = savedFavorites.filter(f => f.id !== id);
                    localStorage.setItem('outfit_favs', JSON.stringify(savedFavorites));
                    updateFavCount();
                    renderFavoritesList();
                    showToast('削除しました');
                };
            });

            lucide.createIcons();
        }

        // Modal Controls
        function openModal() {
            renderFavoritesList();
            favoritesModal.classList.remove('hidden');
            setTimeout(() => {
                favoritesModal.classList.remove('opacity-0');
            }, 10);
        }

        function closeModal() {
            favoritesModal.classList.add('opacity-0');
            setTimeout(() => {
                favoritesModal.classList.add('hidden');
            }, 300);
        }

        openFavoritesBtn.onclick = openModal;
        closeFavoritesBtn.onclick = closeModal;
        closeFavoritesFooterBtn.onclick = closeModal;
        saveCurrentOutfitBtn.onclick = saveCurrentOutfit;

        clearAllFavsBtn.onclick = () => {
            if (savedFavorites.length === 0) return;
            savedFavorites = [];
            localStorage.setItem('outfit_favs', JSON.stringify(savedFavorites));
            updateFavCount();
            renderFavoritesList();
            showToast('すべての保存ログを削除しました');
        };

        // Window Load Initialization
        window.onload = function() {
            initPresets();
            updateFavCount();
            updateApp();
            if (window.lucide) {
                lucide.createIcons();
            }
        };
    </script>
</body>
</html>
