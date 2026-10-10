<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>日語 Small Talk 實戰對話生成器</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Google Fonts & Font Awesome Icons -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Kosugi+Maru&family=Noto+Sans+JP:wght@400;500;700&family=Noto+Sans+TC:wght@400;500;700&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            matcha: {
              50: '#f4f7f4',
              100: '#e3eae3',
              200: '#c5d5c5',
              500: '#6b8e63',
              600: '#55724f',
              700: '#435a3e',
            },
            warmwood: '#fdfbf7',
            sakura: '#fbebf0',
            woodborder: '#e6ded4'
          },
          fontFamily: {
            jp: ['"Noto Sans JP"', '"Kosugi Maru"', 'sans-serif'],
            tc: ['"Noto Sans TC"', 'sans-serif'],
          }
        }
      }
    }
  </script>

  <style>
    body {
      background-color: #fcf9f2;
      background-image: radial-gradient(#e5decf 1px, transparent 1px);
      background-size: 20px 20px;
      font-family: 'Noto Sans TC', 'Noto Sans JP', sans-serif;
    }

    /* Ruby Control Style */
    rt {
      font-size: 0.65em;
      color: #6b7280;
      user-select: none;
    }
    .hide-furigana rt {
      display: none !important;
    }

    /* 3D Flip Card Animation Styles */
    .perspective-1000 {
      perspective: 1000px;
    }
    .flip-card-inner {
      position: relative;
      width: 100%;
      height: 100%;
      transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
      transform-style: preserve-3d;
    }
    .flip-card-inner.flipped {
      transform: rotateY(180deg);
    }
    .flip-card-front, .flip-card-back {
      position: absolute;
      width: 100%;
      height: 100%;
      -webkit-backface-visibility: hidden;
      backface-visibility: hidden;
      border-radius: 1rem;
    }
    .flip-card-back {
      transform: rotateY(180deg);
    }

    /* Masking styles for mode 3 */
    .mask-text {
      background-color: #cbd5e1;
      color: transparent !important;
      border-radius: 0.25rem;
      cursor: pointer;
      transition: all 0.2s;
      user-select: none;
    }
    .mask-text rt {
      visibility: hidden;
    }
    .mask-text:hover {
      background-color: #94a3b8;
    }
    .mask-revealed {
      background-color: transparent !important;
      color: inherit !important;
    }
    .mask-revealed rt {
      visibility: visible !important;
    }
  </style>
</head>
<body class="text-slate-800 min-h-screen flex flex-col justify-between">

  <header class="sticky top-0 z-40 bg-white/90 backdrop-blur-md border-b border-woodborder shadow-sm">
    <div class="max-w-6xl mx-auto px-4 py-3 flex flex-wrap items-center justify-between gap-3">
      <!-- Logo -->
      <div class="flex items-center space-x-3">
        <div class="w-10 h-10 rounded-xl bg-matcha-500 text-white flex items-center justify-center font-bold text-xl shadow-md">
          雑
        </div>
        <div>
          <h1 class="text-xl font-bold tracking-wide text-slate-800">日語 Small Talk 實戰對話</h1>
          <p class="text-xs text-slate-500">2分鐘生活與職場流暢話題庫 (40話)</p>
        </div>
      </div>

      <!-- Settings & Controls Header Bar -->
      <div class="flex flex-wrap items-center gap-2 text-sm">
        <!-- Furigana Toggle -->
        <button id="toggle-furigana-btn" onclick="toggleFurigana()" class="px-3 py-1.5 rounded-lg border border-slate-300 bg-white hover:bg-slate-50 text-slate-700 flex items-center space-x-1.5 transition">
          <i class="fa-solid fa-[#000] fa-language text-matcha-600"></i>
          <span>假名：<span id="furigana-status-text" class="font-bold text-matcha-600">顯示</span></span>
        </button>

        <!-- TTS Speed Selector -->
        <div class="flex items-center space-x-1 bg-white border border-slate-300 rounded-lg px-2 py-1">
          <i class="fa-solid fa-[#000] fa-gauge-high text-slate-400 text-xs"></i>
          <span class="text-xs text-slate-500 hidden sm:inline">語速:</span>
          <select id="tts-rate-select" class="bg-transparent text-xs font-medium text-slate-700 focus:outline-none cursor-pointer">
            <option value="0.75">0.75x 慢速</option>
            <option value="1.0" selected>1.0x 正常</option>
            <option value="1.25">1.25x 快速</option>
          </select>
        </div>

        <!-- Timer Button -->
        <button onclick="toggleTimerModal()" class="px-3 py-1.5 rounded-lg bg-amber-500 hover:bg-amber-600 text-white font-medium flex items-center space-x-1 shadow-sm transition">
          <i class="fa-solid fa-[#000] fa-stopwatch"></i>
          <span>2分鐘計時器</span>
        </button>
      </div>
    </div>
  </header>

  <main class="max-w-6xl mx-auto px-4 py-6 flex-grow w-full space-y-6">

    <!-- Hero / Random Picker Banner -->
    <section class="bg-gradient-to-r from-matcha-600 to-matcha-700 rounded-2xl p-6 text-white shadow-xl relative overflow-hidden">
      <div class="absolute right-0 top-0 opacity-10 translate-x-8 -translate-y-8 text-9xl font-jp pointer-events-none">会話</div>
      
      <div class="relative z-10 max-w-2xl space-y-4">
        <span class="inline-block px-3 py-1 bg-white/20 backdrop-blur-md rounded-full text-xs font-semibold uppercase tracking-wider text-matcha-100">
          即席スモールトーク
        </span>
        <h2 class="text-2xl sm:text-3xl font-bold font-jp">隨機抽取 Small Talk 話題！</h2>
        <p class="text-sm text-matcha-100 leading-relaxed">
          不知如何開始話題？點擊下方按鈕隨機抽題，立即進行 2 分鐘對話練習。
        </p>
        
        <div class="pt-2 flex flex-wrap gap-3">
          <button onclick="drawRandomTopic()" class="px-6 py-3 bg-amber-400 hover:bg-amber-300 text-slate-900 font-bold rounded-xl shadow-lg hover:shadow-xl transform hover:-translate-y-0.5 transition flex items-center space-x-2">
            <i class="fa-solid fa-[#000] fa-shuffle"></i>
            <span>隨機抽一個話題</span>
          </button>
          <button onclick="scrollToTopics()" class="px-5 py-3 bg-white/10 hover:bg-white/20 text-white rounded-xl font-medium transition flex items-center space-x-2">
            <i class="fa-solid fa-[#000] fa-list-ul"></i>
            <span>瀏覽 40 組全話題</span>
          </button>
        </div>
      </div>
    </section>

    <!-- Mode Selector & Controls Filter Bar -->
    <section class="bg-white rounded-2xl p-4 shadow-sm border border-woodborder space-y-4">
      <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 border-b border-slate-100 pb-4">
        <!-- Learning Mode Switcher -->
        <div>
          <label class="text-xs font-bold text-slate-400 uppercase tracking-wider block mb-1">練習模式 (Practice Mode)</label>
          <div class="inline-flex p-1 bg-slate-100 rounded-xl space-x-1">
            <button id="mode-btn-standard" onclick="setMode('standard')" class="px-3.5 py-1.5 rounded-lg text-sm font-medium transition bg-white shadow-sm text-matcha-700">
              <i class="fa-solid fa-[#000] fa-align-left mr-1"></i>標準雙語
            </button>
            <button id="mode-btn-flip" onclick="setMode('flip')" class="px-3.5 py-1.5 rounded-lg text-sm font-medium transition text-slate-600 hover:text-slate-900">
              <i class="fa-solid fa-[#000] fa-rotate mr-1"></i>3D 翻轉卡片
            </button>
            <button id="mode-btn-mask" onclick="setMode('mask')" class="px-3.5 py-1.5 rounded-lg text-sm font-medium transition text-slate-600 hover:text-slate-900">
              <i class="fa-solid fa-[#000] fa-eye-slash mr-1"></i>遮蔽練習
            </button>
          </div>
        </div>

        <!-- Extra Mask Controls (Shows in mask mode) -->
        <div id="mask-controls" class="hidden flex items-center space-x-2 bg-amber-50 p-2 rounded-xl border border-amber-200 text-xs">
          <span class="text-amber-800 font-medium">遮蔽對象:</span>
          <button onclick="setMaskTarget('zh')" id="mask-zh-btn" class="px-2 py-1 rounded bg-amber-600 text-white font-medium">隱藏中文</button>
          <button onclick="setMaskTarget('ja')" id="mask-ja-btn" class="px-2 py-1 rounded bg-white text-amber-800 border border-amber-300">隱藏日文</button>
        </div>
      </div>

      <!-- Filter Categories & Search Bar -->
      <div class="flex flex-col sm:flex-row items-stretch sm:items-center justify-between gap-3">
        <!-- Categories Filters -->
        <div id="category-filters" class="flex flex-wrap gap-1.5">
          <!-- Populated by JS -->
        </div>

        <!-- Search Input -->
        <div class="relative min-w-[200px]">
          <i class="fa-solid fa-[#000] fa-magnifying-glass absolute left-3 top-1/2 -translate-y-1/2 text-slate-400 text-sm"></i>
          <input type="text" id="search-input" oninput="handleSearch()" placeholder="搜尋關鍵字 / 話題..." class="w-full pl-9 pr-3 py-1.5 text-sm rounded-xl border border-slate-200 focus:outline-none focus:border-matcha-500 focus:ring-1 focus:ring-matcha-500 bg-slate-50">
        </div>
      </div>
    </section>

    <section id="featured-section" class="hidden">
      <!-- Interactive Random Card dynamically populated -->
    </section>

    <section id="topics-container" class="grid grid-cols-1 md:grid-cols-2 gap-6">
      <!-- Generated Cards go here -->
    </section>

  </main>

  <div id="timer-modal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl p-6 max-w-sm w-full shadow-2xl border border-slate-100 text-center space-y-4 relative">
      <button onclick="toggleTimerModal()" class="absolute top-4 right-4 text-slate-400 hover:text-slate-600">
        <i class="fa-solid fa-[#000] fa-xmark text-xl"></i>
      </button>

      <div class="w-16 h-16 bg-amber-100 text-amber-600 rounded-full flex items-center justify-center mx-auto text-2xl">
        <i class="fa-solid fa-[#000] fa-stopwatch"></i>
      </div>

      <div>
        <h3 class="text-lg font-bold text-slate-800">2 分鐘對話計時器</h3>
        <p class="text-xs text-slate-500 mt-1">模擬真實日本職場/社交 2 分鐘對話節奏</p>
      </div>

      <!-- Digital Timer Screen -->
      <div class="text-5xl font-mono font-bold text-slate-800 bg-slate-50 py-4 rounded-xl border border-slate-200 tracking-wider" id="timer-display">
        02:00
      </div>

      <!-- Control Buttons -->
      <div class="flex items-center justify-center space-x-3 pt-2">
        <button id="timer-start-btn" onclick="startTimer()" class="flex-1 py-2.5 bg-matcha-600 hover:bg-matcha-700 text-white font-medium rounded-xl transition">
          <i class="fa-solid fa-[#000] fa-play mr-1"></i> 開始
        </button>
        <button id="timer-pause-btn" onclick="pauseTimer()" class="flex-1 py-2.5 bg-slate-200 hover:bg-slate-300 text-slate-700 font-medium rounded-xl transition hidden">
          <i class="fa-solid fa-[#000] fa-pause mr-1"></i> 暫停
        </button>
        <button onclick="resetTimer()" class="px-4 py-2.5 bg-slate-100 hover:bg-slate-200 text-slate-600 font-medium rounded-xl transition">
          <i class="fa-solid fa-[#000] fa-rotate-right"></i>
        </button>
      </div>
    </div>
  </div>

  <!-- Toast Notification -->
  <div id="toast" class="fixed bottom-6 right-6 bg-slate-800 text-white px-4 py-2.5 rounded-xl shadow-lg text-sm flex items-center space-x-2 transition-all duration-300 opacity-0 pointer-events-none translate-y-4 z-50">
    <i id="toast-icon" class="fa-solid fa-[#000] fa-circle-check text-matcha-500"></i>
    <span id="toast-msg">已複製文字</span>
  </div>

  <footer class="bg-white border-t border-woodborder mt-12 py-6 text-center text-xs text-slate-500 space-y-2">
    <p>日語 Small Talk (2分鐘雜談) 實戰對話生成器 &copy; 2026</p>
    <p class="text-slate-400">專為日語學習者打造的輕量化對話練習平臺</p>
  </footer>

  <script>
    /* ==========================================================================
       40 COMPLETE TOPICS DATABASE (W/ FURIGANA RUBY & CHINESE)
       ========================================================================== */
    const TOPICS_DATA = [
      // 分類 1: 性格習慣與生活風格 (1-8)
      {
        id: 1,
        category: "性格習慣與生活風格",
        title: "朝型人間 vs 夜型人間 (早起派與夜貓子)",
        technique: "分享個人作息差異，並詢問對方習慣",
        dialogue: [
          {
            speaker: "A",
            ja: "<ruby>朝型<rt>あさがた</rt></ruby>ですか？それとも<ruby>夜型<rt>よるがた</rt></ruby>ですか？",
            zh: "你是早起派還是夜貓子派？"
          },
          {
            speaker: "B",
            ja: "<ruby>完全<rt>かんぜん</rt></ruby>に<ruby>夜型<rt>よるがた</rt></ruby>ですね。<ruby>夜<rt>よる</rt></ruby>のほうが高集中できます。<ruby>朝<rt>あさ</rt></ruby>はちょっと<ruby>苦手<rt>にがて</rt></ruby>です。",
            zh: "我完全是夜貓子呢。晚上比較能集中精神，早上有點不太行。"
          },
          {
            speaker: "A",
            ja: "わかります。<ruby>私<rt>わたし</rt></ruby>も<ruby>昔<rt>むかし</rt></ruby>は<ruby>夜型<rt>よるがた</rt></ruby>でしたが、最近は<ruby>早起<rt>はやお</rt></ruby>きに挑戦しています。",
            zh: "我懂！我以前也是夜貓子，但最近正在挑戰早起。"
          },
          {
            speaker: "B",
            ja: "すごいですね！<ruby>早起<rt>はやお</rt></ruby>きして何かしていますか？",
            zh: "好厲害喔！早起之後都會做些什麼呢？"
          }
        ]
      },
      {
        id: 2,
        category: "性格習慣與生活風格",
        title: "猫派 vs 犬派 (貓派與狗派)",
        technique: "用寵物話題快速建立親切感與共鳴",
        dialogue: [
          {
            speaker: "A",
            ja: "〇〇さんは、<ruby>犬派<rt>いぬは</rt></ruby>ですか？<ruby>猫派<rt>ねこは</rt></ruby>ですか？",
            zh: "〇〇先生/小姐是狗派還是貓派呢？"
          },
          {
            speaker: "B",
            ja: "<ruby>断然<rt>だんぜん</rt></ruby><ruby>猫派<rt>ねこは</rt></ruby>です！あのマイペースなところがたまらないですね。",
            zh: "絕對是貓派！那種隨心所欲的個性真的讓人受不了地喜歡。"
          },
          {
            speaker: "A",
            ja: "やっぱり！実は<ruby>私<rt>わたし</rt></ruby>も<ruby>猫<rt>ねこ</rt></ruby>を飼っているんですよ。",
            zh: "果然！其實我自己也有養貓喔。"
          },
          {
            speaker: "B",
            ja: "えっ、本当ですか？どんな<ruby>種類<rt>しゅるい</rt></ruby>の<ruby>猫<rt>ねこ</rt></ruby>ですか？写真見たいです！",
            zh: "欸，是真的嗎？是什麼品種的貓呢？好想看照片！"
          }
        ]
      },
      {
        id: 3,
        category: "性格習慣與生活風格",
        title: "休日の過ごし方 (週末假日如何度過)",
        technique: "聊聊休閒興趣，展現生活化的一面",
        dialogue: [
          {
            speaker: "A",
            ja: "インドア<ruby>派<rt>は</rt></ruby>ですか？アウトドア<ruby>派<rt>は</rt></ruby>ですか？",
            zh: "你是室內派還是戶外派？"
          },
          {
            speaker: "B",
            ja: "<ruby>休日<rt>きゅうじつ</rt></ruby>は家で映画を見るのが好きなので、インドア<ruby>派<rt>は</rt></ruby>ですね。",
            zh: "假日時我喜歡待在家看電影，所以是室內派呢。"
          },
          {
            speaker: "A",
            ja: "いいですね。最近何か面白い映画は見ましたか？",
            zh: "真不錯呢。最近有看什麼有趣的電影嗎？"
          },
          {
            speaker: "B",
            ja: "Netflixで話題のドラマを<ruby>一気<rt>いっき</rt></ruby>見しました！おすすめです。",
            zh: "我把Netflix上熱門的影集一口氣追完了！非常推薦喔。"
          }
        ]
      },
      {
        id: 4,
        category: "性格習慣與生活風格",
        title: "ミニマリスト (極簡主義生活風格)",
        technique: "討論生活哲學，帶出個人日常選擇",
        dialogue: [
          {
            speaker: "A",
            ja: "最近、<ruby>部屋<rt>へや</rt></ruby>の<ruby>片付<rt>かたづ</rt></ruby>けをして物を減らしているんです。",
            zh: "最近我在整理房間，正在減少家裡的物品。"
          },
          {
            speaker: "B",
            ja: "ミニマリストを目指しているんですか？すばらしいですね！",
            zh: "是在以極簡主義者為目標嗎？太厲害了！"
          },
          {
            speaker: "A",
            ja: "はい、でも洋服が多くてなかなか捨てられなくて…",
            zh: "對啊，但是衣服太多了，實在有點捨不得扔……"
          },
          {
            speaker: "B",
            ja: "<ruby>洋服<rt>ようふく</rt></ruby>は一番難易度が高いですよね。<ruby>一年<rt>いちねん</rt></ruby><ruby>着<rt>き</rt></ruby>なかったら<ruby>処分<rt>しょぶん</rt></ruby>するといいですよ。",
            zh: "衣服是難度最高的呢。如果有一年沒穿到的話，建議可以清理掉喔。"
          }
        ]
      },
      {
        id: 5,
        category: "性格習慣與生活風格",
        title: "キャッシュレス決済 (無現金支付習慣)",
        technique: "從現代社會習慣，聊聊日常生活變化",
        dialogue: [
          {
            speaker: "A",
            ja: "最近、ほとんど<ruby>現金<rt>げんきん</rt></ruby>を使わなくなりましたよね。",
            zh: "最近幾乎都不怎麼使用現金了呢。"
          },
          {
            speaker: "B",
            ja: "本当ですね。スマホ決済ばかりで、<ruby>財布<rt>さいふ</rt></ruby>を<ruby>忘<rt>わす</rt></ruby>れても困らなくなりました。",
            zh: "真的。都是用手機支付，現在就算忘記帶錢包也不會有困擾了。"
          },
          {
            speaker: "A",
            ja: "でもたまに<ruby>現金<rt>げんきん</rt></ruby>しか使えないお店があって焦ります！",
            zh: "不過偶爾遇到只能用現金的店家還是會慌張一下！"
          },
          {
            speaker: "B",
            ja: "あはは、確かに！一応少しだけ現金も持っておくのが安心ですね。",
            zh: "哈哈真的！果然還是隨身帶一點點現金比較安心呢。"
          }
        ]
      },
      {
        id: 6,
        category: "性格習慣與生活風格",
        title: "運動と健康維持 (運動與健康習慣)",
        technique: "分享健康管理與日常運動，表達關心",
        dialogue: [
          {
            speaker: "A",
            ja: "健康のために何か運動はしていますか？",
            zh: "為了健康，平時有做些什麼運動嗎？"
          },
          {
            speaker: "B",
            ja: "<ruby>毎朝<rt>まいあさ</rt></ruby>30分くらい散歩するようにしています。<ruby>気分<rt>きぶん</rt></ruby><ruby>転換<rt>てんかん</rt></ruby>になりますよ。",
            zh: "我盡量每天早上散步大約30分鐘，非常有助於轉換心情喔。"
          },
          {
            speaker: "A",
            ja: "<ruby>素晴<rt>すば</rt></ruby>らしい習慣ですね。<ruby>私<rt>わたし</rt></ruby>もジムに入会したのですが、続かなくて…",
            zh: "真是太棒的習慣了。我自己雖然報名了健身房，但都沒堅持下去……"
          },
          {
            speaker: "B",
            ja: "<ruby>無理<rt>むり</rt></ruby>せず、まずはスモールステップから始めるのがいいですよ！",
            zh: "不用勉強自己，先從微小的第一步開始做起比較好喔！"
          }
        ]
      },
      {
        id: 7,
        category: "性格習慣與生活風格",
        title: "SNSとの付き合い方 (社群媒體使用習慣)",
        technique: "交流數位生活的平衡與個人時間管理",
        dialogue: [
          {
            speaker: "A",
            ja: "ついついスマホでSNSを見すぎてしまうことってありませんか？",
            zh: "你會不會常常不小心滑手機看社群看太久？"
          },
          {
            speaker: "B",
            ja: "よくあります！<ruby>寝<rt>ね</rt></ruby>る前に見始めると止まらなくなりますよね。",
            zh: "經常會這樣！睡前一開始看就停不下來了對吧。"
          },
          {
            speaker: "A",
            ja: "そうなんです。最近は「スマホ断ち」を試しています。",
            zh: "就是說啊。所以我最近正在嘗試『數位戒斷』。"
          },
          {
            speaker: "B",
            ja: "効果はどうですか？<ruby>夜<rt>よる</rt></ruby>よく<ruby>眠<rt>ねむ</rt></ruby>れるようになりましたか？",
            zh: "效果怎麼樣呢？晚上有變得比較好入睡嗎？"
          }
        ]
      },
      {
        id: 8,
        category: "性格習慣與生活風格",
        title: "読書習慣 (閱讀興趣與聽書體驗)",
        technique: "聊聊吸收知識與休閒閱讀的方法",
        dialogue: [
          {
            speaker: "A",
            ja: "最近、何か面白い本を読まれましたか？",
            zh: "最近有沒有讀到什麼有趣的新書呢？"
          },
          {
            speaker: "B",
            ja: "最近は紙の本ではなく、オーディオブックで「聴く読書」をしています。",
            zh: "我最近不是看紙本書，而是用有聲書在實踐『用聽的閱讀』。"
          },
          {
            speaker: "A",
            ja: "へえ！通勤時間にも聴けて効率的ですね。",
            zh: "欸！這樣通勤時間也能聽，效率很高呢。"
          },
          {
            speaker: "B",
            ja: "そうなんです！<ruby>家事<rt>かじ</rt></ruby>をしながらでも聴けるのでオススメですよ。",
            zh: "沒錯！做家事的時候也能聽，非常推薦喔。"
          }
        ]
      },

      // 分類 2: 飲食與品味體驗 (9-16)
      {
        id: 9,
        category: "飲食與品味體驗",
        title: "コーヒー派 vs 紅茶派 (咖啡與紅茶的喜好)",
        technique: "經典飲品話題，延伸至放鬆方式與口袋名單",
        dialogue: [
          {
            speaker: "A",
            ja: "<ruby>仕事<rt>しごと</rt></ruby><ruby>中<rt>ちゅう</rt></ruby>はコーヒー派ですか？それとも紅茶派ですか？",
            zh: "工作中你是咖啡派還是紅茶派呢？"
          },
          {
            speaker: "B",
            ja: "<ruby>朝<rt>あさ</rt></ruby>は断然コーヒーですね。目が覚めますから。",
            zh: "早上絕對是咖啡派呢，畢竟能醒腦嘛。"
          },
          {
            speaker: "A",
            ja: "こだわりのお気に入りの豆とかあるんですか？",
            zh: "有什麼特別講究或喜歡的咖啡豆嗎？"
          },
          {
            speaker: "B",
            ja: "最近は深煎りのエチオピア産にはまっています！",
            zh: "最近非常沉迷於深烘焙的衣索比亞豆子喔！"
          }
        ]
      },
      {
        id: 10,
        category: "飲食與品味體驗",
        title: "辛い食べ物 (能否吃辣與推薦料理)",
        technique: "聊聊口味偏好，順便交流美食餐廳",
        dialogue: [
          {
            speaker: "A",
            ja: "辛い料理は得意ですか？",
            zh: "你很擅長吃辣的料理嗎？"
          },
          {
            speaker: "B",
            ja: "<ruby>大好<rt>だいす</rt></ruby>きです！麻婆豆腐や韓国料理をよく食べに行きます。",
            zh: "超喜歡的！我很常去吃麻婆豆腐或是韓國料理。"
          },
          {
            speaker: "A",
            ja: "へえ！おすすめの激辛のお店があったら教えてください。",
            zh: "哇！如果有推薦的超辣餐廳請一定要告訴我。"
          },
          {
            speaker: "B",
            ja: "駅前にすごく美味しい四川料理のお店がありますよ。今度行きましょう！",
            zh: "車站前有一家非常好吃的四川料理店喔，下次一起去吧！"
          }
        ]
      },
      {
        id: 11,
        category: "飲食與品味體驗",
        title: "自炊と得意料理 (親自下廚與拿手菜)",
        technique: "分享家常菜經驗，拉近日常生活距離",
        dialogue: [
          {
            speaker: "A",
            ja: "普段は自炊をすることが多いですか？",
            zh: "平時你自己親自下廚做飯的時候多嗎？"
          },
          {
            speaker: "B",
            ja: "<ruby>週末<rt>しゅうまつ</rt></ruby>にまとめて作り置きを作っています。<ruby>得意<rt>とくい</rt></ruby><ruby>料理<rt>りょうり</rt></ruby>はスパイスカレーです。",
            zh: "我會在週末一次做好備用常備菜。拿手菜是香料咖哩。"
          },
          {
            speaker: "A",
            ja: "スパイスから作るんですか？本格的ですね！",
            zh: "是從各式香料開始調配做起的嗎？好專業喔！"
          },
          {
            speaker: "B",
            ja: "調合を変えると味が変わって面白いんですよ。",
            zh: "改變香料調配比例口味就會變，很有趣喔。"
          }
        ]
      },
      {
        id: 12,
        category: "飲食與品味體驗",
        title: "ラーメンの好み (拉麵湯頭風味之爭)",
        technique: "談論日本最具代表性的美食話題",
        dialogue: [
          {
            speaker: "A",
            ja: "ラーメンは何味が一番好きですか？",
            zh: "拉麵你最喜歡什麼風味湯頭呢？"
          },
          {
            speaker: "B",
            ja: "こってりした豚骨ラーメンが一番好きですね。",
            zh: "我最喜歡濃郁的豚骨拉麵了。"
          },
          {
            speaker: "A",
            ja: "<ruby>私<rt>わたし</rt></ruby>はあっさりした醤油派です。バリカタで注文しますか？",
            zh: "我是清爽的醬油派呢。你去吃豚骨拉麵會點超硬麵條（バリ硬）嗎？"
          },
          {
            speaker: "B",
            ja: "もちろん！麺は固めが好きです！",
            zh: "那當然！麵條就是要偏硬才好吃！"
          }
        ]
      },
      {
        id: 13,
        category: "飲食與品味體驗",
        title: "コンビニスイーツ (超商甜點話題)",
        technique: "平易近人的熱門商品話題，製造輕鬆氛圍",
        dialogue: [
          {
            speaker: "A",
            ja: "日本のコンビニスイーツってクオリティが高いですよね。",
            zh: "日本便利商店的甜點品質真的非常高呢。"
          },
          {
            speaker: "B",
            ja: "本当ですね！新商品が出るとついつい買ってしまいます。",
            zh: "真的！每次看到有新商品推出，都會忍不住買下來。"
          },
          {
            speaker: "A",
            ja: "最近何かヒットしたスイーツはありますか？",
            zh: "最近有吃到什麼驚為天人的熱門甜點嗎？"
          },
          {
            speaker: "B",
            ja: "ローソンのバスチーがやっぱり安くて美味しいです！",
            zh: "Lawson 的巴斯克起司蛋糕果然還是便宜又美味！"
          }
        ]
      },
      {
        id: 14,
        category: "飲食與品味體驗",
        title: "お酒の嗜み (飲酒與居酒屋文化)",
        technique: "下班後的社交活動，表達適度親和力",
        dialogue: [
          {
            speaker: "A",
            ja: "お酒はよく飲まれますか？",
            zh: "平時常喝酒嗎？"
          },
          {
            speaker: "B",
            ja: "週末にビールやハイボールを少し飲む程度ですよ。",
            zh: "大概只有週末會喝一點啤酒或 Highball 的程度而已喔。"
          },
          {
            speaker: "A",
            ja: "<ruby>最近<rt>さいきん</rt></ruby>はクラフトビールも人気がありますよね。",
            zh: "最近精釀啤酒也相當受歡迎呢。"
          },
          {
            speaker: "B",
            ja: "いろいろな味があって飲み比べるのが楽しいですよね！",
            zh: "有各式各樣風味，互相比較品嚐真的很有趣呢！"
          }
        ]
      },
      {
        id: 15,
        category: "飲食與品味體驗",
        title: "季節の味覚 (季節限定旬之食材)",
        technique: "用「四季節氣」體現日語特有的風雅感受",
        dialogue: [
          {
            speaker: "A",
            ja: "<ruby>秋<rt>あき</rt></ruby>になると美味しいものが増えて困りますね。",
            zh: "一到秋天美味的食物變多，真讓人困擾呢。"
          },
          {
            speaker: "B",
            ja: "<ruby>食欲<rt>しょくよく</rt></ruby>の<ruby>秋<rt>あき</rt></ruby>ですからね！秋刀魚や栗が美味しい季節です。",
            zh: "畢竟是食欲之秋嘛！是秋刀魚和栗子都很美味的季節。"
          },
          {
            speaker: "A",
            ja: "焼き芋も恋しくなりますね。",
            zh: "也會開始懷念烤地瓜呢。"
          },
          {
            speaker: "B",
            ja: "季節の旬のものを食べるのが一番の贅沢ですね。",
            zh: "品嚐當季新鮮食材就是最大的奢華享受呢。"
          }
        ]
      },
      {
        id: 16,
        category: "飲食與品味體驗",
        title: "パン派 vs 米派 (早餐吃麵包還是米飯)",
        technique: "經典的習慣比較，簡單自然好接話",
        dialogue: [
          {
            speaker: "A",
            ja: "朝食はパン派ですか？それともご飯派ですか？",
            zh: "早餐你是麵包派還是米飯派呢？"
          },
          {
            speaker: "B",
            ja: "手軽なのでパンが多いですね。食パンにジャムを塗って食べています。",
            zh: "因為比較方便，所以我大多吃麵包呢。吐司抹上果醬吃。"
          },
          {
            speaker: "A",
            ja: "私はしっかり和食のご飯と味噌汁派です！",
            zh: "我是堅定的日式米飯搭味噌湯派！"
          },
          {
            speaker: "B",
            ja: "朝から温かい味噌汁は体に良さそうですね！",
            zh: "一大早就能喝到熱騰騰的味噌湯，感覺對身體很好呢！"
          }
        ]
      },

      // 分類 3: 職場日常與提神靈感 (17-24)
      {
        id: 17,
        category: "職場日常與提神靈感",
        title: "眠気覚まし (職場下午提神秘訣)",
        technique: "同理同事疲勞，分享友善實用小妙招",
        dialogue: [
          {
            speaker: "A",
            ja: "昼食の後はどうしても眠くなってしまいます…",
            zh: "吃完午飯後無論如何都會變得很睏呢……"
          },
          {
            speaker: "B",
            ja: "わかります。<ruby>私<rt>わたし</rt></ruby>は少しストレッチをしたり、冷たい水を飲んだりしています。",
            zh: "我懂！我會做些伸展操，或是喝杯冰水醒腦。"
          },
          {
            speaker: "A",
            ja: "なるほど。ちょっと席を立って歩いてくるのも良さそうですね。",
            zh: "原來如此，稍微離開座位走動一下好像也不錯。"
          },
          {
            speaker: "B",
            ja: "15分くらいの仮眠も効果的ですよ！試してみてください。",
            zh: "大約15分鐘的小睡也非常有效喔！請務必試試看。"
          }
        ]
      },
      {
        id: 18,
        category: "職場日常與提神靈感",
        title: "リモートワーク (居家上班與進公司辦公)",
        technique: "現代工作型態交流，分享效率高低",
        dialogue: [
          {
            speaker: "A",
            ja: "在宅勤務と出社、どちらが好きですか？",
            zh: "居家上班和進公司辦公，你比較喜歡哪一個？"
          },
          {
            speaker: "B",
            ja: "通勤時間がないので在宅勤務の方が楽ですね。",
            zh: "因為沒有通勤時間，所以居家上班比較輕鬆呢。"
          },
          {
            speaker: "A",
            ja: "でも対面で話した方が仕事がスムーズな時もありますよね。",
            zh: "不過有時候還是面對面溝通工作比較順暢對吧。"
          },
          {
            speaker: "B",
            ja: "そうですね。ハイブリッドな働き方が一番理想的です。",
            zh: "確實是這樣。混合式的工作型態是最理想的呢。"
          }
        ]
      },
      {
        id: 19,
        category: "職場日常與提神靈感",
        title: "通勤時間の過ごし方 (通勤時間運用與利用)",
        technique: "分享日常碎片時間的運用方法",
        dialogue: [
          {
            speaker: "A",
            ja: "通勤時間は電車で何をしていますか？",
            zh: "搭電車通勤的時間你都在做些什麼呢？"
          },
          {
            speaker: "B",
            ja: "ポッドキャストを聴くか、ニュースをチェックしています。",
            zh: "不是聽 Podcast，就是刷新聞訊息。"
          },
          {
            speaker: "A",
            ja: "有意義な時間ですね。<ruby>私<rt>わたし</rt></ruby>はついついゲームをしてしまいます。",
            zh: "真是有意義的時間呢。我常常不小心就在玩手機遊戲。"
          },
          {
            speaker: "B",
            ja: "リラックスする時間も大切ですよ！",
            zh: "放鬆的時間也很重要啦！"
          }
        ]
      },
      {
        id: 20,
        category: "職場日常與提神靈感",
        title: "オフィス環境 (辦公室溫度與舒適度)",
        technique: "辦公室關懷話題，貼心展現包容",
        dialogue: [
          {
            speaker: "A",
            ja: "オフィスのエアコン、ちょっと寒くありませんか？",
            zh: "辦公室的空調，會不會覺得有點冷？"
          },
          {
            speaker: "B",
            ja: "あ、少し寒いですね。カーディガンを羽織ろうかな。",
            zh: "啊，確實有點冷呢。我是不是該披件外套好呢。"
          },
          {
            speaker: "A",
            ja: "設定温度を少し上げてもらうようにお願いしましょうか。",
            zh: "要不要請人幫忙把設定溫度調高一點點呢？"
          },
          {
            speaker: "B",
            ja: "助かります！ありがとうございます。",
            zh: "幫大忙了！非常感謝你。"
          }
        ]
      },
      {
        id: 21,
        category: "職場日常與提神靈感",
        title: "デスク周りのこだわり (桌面整理與擺飾)",
        technique: "談論工作環境，展現個人性格小物",
        dialogue: [
          {
            speaker: "A",
            ja: "〇〇さんのデスク、いつも綺麗に整頓されていますね！",
            zh: "〇〇先生/小姐的桌子總是整理得好乾淨喔！"
          },
          {
            speaker: "B",
            ja: "ありがとうございます。物が少ない方が集中できるんです。",
            zh: "謝謝。東西少一點比較能夠集中精神。"
          },
          {
            speaker: "A",
            ja: "あの小さな観葉植物も可愛いですね。",
            zh: "那個小小的觀葉植物也很可愛呢。"
          },
          {
            speaker: "B",
            ja: "癒やし効果を狙って置いています！",
            zh: "放那是為了達到療癒放鬆的效果喔！"
          }
        ]
      },
      {
        id: 22,
        category: "職場日常與提神靈感",
        title: "タスク管理術 (工作待辦事項與時間管理)",
        technique: "交換工作技巧，展現向上學習積極度",
        dialogue: [
          {
            speaker: "A",
            ja: "仕事のタスク管理はどうやってやっていますか？",
            zh: "平時你的工作任務管理都是怎麼做的呢？"
          },
          {
            speaker: "B",
            ja: "To-Doリストアプリを使って毎朝整理しています。",
            zh: "我都是用 To-Do List App 每天早上進行整理。"
          },
          {
            speaker: "A",
            ja: "<ruby>私<rt>わたし</rt></ruby>はまだ付箋紙に書いてデスクに貼っています（笑）。",
            zh: "我現在還是寫在便利貼上貼在桌上呢（笑）。"
          },
          {
            speaker: "B",
            ja: "アナログも視覚的ですぐ分かっていいと思いますよ！",
            zh: "紙本也很直覺，一眼就能看到也很好啊！"
          }
        ]
      },
      {
        id: 23,
        category: "職場日常與提神靈感",
        title: "プレミアムフライデー / 定時退社 (準時下班打算)",
        technique: "輕鬆聊聊下班後的個人休閒計畫",
        dialogue: [
          {
            speaker: "A",
            ja: "今日は金曜日ですね！定時で帰れそうですか？",
            zh: "今天是星期五呢！今天有機會準時下班嗎？"
          },
          {
            speaker: "B",
            ja: "はい、なんとか仕事が終わったので定時で上がります！",
            zh: "有，工作好不容易告一段落了，今天能準時走！"
          },
          {
            speaker: "A",
            ja: "いいですね！何かご予定はあるんですか？",
            zh: "太棒了！下班後有什麼計畫嗎？"
          },
          {
            speaker: "B",
            ja: "友達と前から予約していた焼き肉を食べに行きます！",
            zh: "要跟朋友去吃好久以前就預約好的燒肉！"
          }
        ]
      },
      {
        id: 24,
        category: "職場日常與提神靈感",
        title: "新商品の試食・試用 (辦公室零食分享)",
        technique: "透過分享點心，熱絡辦公室氣氛",
        dialogue: [
          {
            speaker: "A",
            ja: "これ、出張のお土産のお菓子なんですけど、どうぞ！",
            zh: "這個是出差帶回來的伴手禮點心，請吃！"
          },
          {
            speaker: "B",
            ja: "わあ、ありがとうございます！いただきます。",
            zh: "哇，太感謝你了！那我不客氣囉。"
          },
          {
            speaker: "A",
            ja: "地元ですごく有名なクッキーなんですよ。",
            zh: "這是在當地非常著名的餅乾喔。"
          },
          {
            speaker: "B",
            ja: "サクサクしていてすごく美味しいです！ごちそうさまです。",
            zh: "口感酥酥脆脆的非常好吃！謝謝你的招待。"
          }
        ]
      },

      // 分類 4: 出差旅行與休閒生活 (25-32)
      {
        id: 25,
        category: "出差旅行與休閒生活",
        title: "行きたい旅行先 (國內外想去的旅遊地點)",
        technique: "分享度假憧憬，尋求共同旅遊話題",
        dialogue: [
          {
            speaker: "A",
            ja: "もし長い休みが取れたらどこに行きたいですか？",
            zh: "如果能放長假的話，你最想去哪裡玩呢？"
          },
          {
            speaker: "B",
            ja: "北海道に行きたいですね。美味しいものを食べ尽くしたいです。",
            zh: "好想去北海道喔，想把當地的美食通通吃一遍。"
          },
          {
            speaker: "A",
            ja: "北海道は海鮮もラーメンも最高ですからね！",
            zh: "北海道無論是海鮮還是拉麵都是最讚的呢！"
          },
          {
            speaker: "B",
            ja: "温泉に入ってゆっくり過ごすのが夢です。",
            zh: "泡著溫泉悠閒度過是我的夢想呢。"
          }
        ]
      },
      {
        id: 26,
        category: "出差旅行與休閒生活",
        title: "温泉旅行 (日本溫泉體驗與放鬆)",
        technique: "討論日本獨特的放鬆文化與行程",
        dialogue: [
          {
            speaker: "A",
            ja: "温泉はお好きですか？",
            zh: "你喜歡泡溫泉嗎？"
          },
          {
            speaker: "B",
            ja: "大好きです！冬に行くと最高に癒やされますよね。",
            zh: "超喜歡的！冬天去泡的話真的能得到最高度的療癒呢。"
          },
          {
            speaker: "A",
            ja: "おすすめの温泉地はありますか？",
            zh: "有沒有推薦的溫泉勝地呢？"
          },
          {
            speaker: "B",
            ja: "箱根や草津温泉がアクセスも良くておすすめですよ。",
            zh: "箱根或是草津溫泉交通都很方便，非常推薦喔。"
          }
        ]
      },
      {
        id: 27,
        category: "出差旅行與休閒生活",
        title: "ソロ活・一人旅 (一人旅與獨處樂趣)",
        technique: "分享獨自旅行的自由與充實體驗",
        dialogue: [
          {
            speaker: "A",
            ja: "一人旅をしたことはありますか？",
            zh: "你曾經試過一個人去旅行嗎？"
          },
          {
            speaker: "B",
            ja: "はい、自分のペースで自由に行動できるので好きです。",
            zh: "有啊，可以按照自己的節奏自由行動，我很喜歡。"
          },
          {
            speaker: "A",
            ja: "寂しくないですか？",
            zh: "不會覺得寂寞嗎？"
          },
          {
            speaker: "B",
            ja: "最初は少し不安でしたが、慣れるとすごく快適ですよ！",
            zh: "剛開始有點不安，但習慣之後真的非常舒適自如喔！"
          }
        ]
      },
      {
        id: 28,
        category: "出差旅行與休閒生活",
        title: "テーマパーク (遊樂園與主題樂園)",
        technique: "分享童心與週末熱門去處",
        dialogue: [
          {
            speaker: "A",
            ja: "ディズニーランドとユニバーサル・スタジオ、どちらが好きですか？",
            zh: "迪士尼樂園和環球影城，你比較喜歡哪一個？"
          },
          {
            speaker: "B",
            ja: "アトラクションが好きなのでユニバ派ですね！",
            zh: "因為我很喜歡遊樂設施，所以是環球影城派！"
          },
          {
            speaker: "A",
            ja: "マリオのエリアは行かれましたか？",
            zh: "你去過瑪利歐園區了嗎？"
          },
          {
            speaker: "B",
            ja: "行きました！世界観の再現度がすごくて感動しました。",
            zh: "去過了！世界觀的還原度高到讓人超感動。"
          }
        ]
      },
      {
        id: 29,
        category: "出差旅行與休閒生活",
        title: "写真とカメラ (攝影與打卡熱點)",
        technique: "聊天內容結合景點、照片與數位記錄",
        dialogue: [
          {
            speaker: "A",
            ja: "旅行に行くと写真はたくさん撮る方ですか？",
            zh: "去旅行的時候你是會拍很多照片的那種人嗎？"
          },
          {
            speaker: "B",
            ja: "はい、スマホで風景や料理の写真をよく撮ります。",
            zh: "對啊，我很常拿手機拍風景跟美食照片。"
          },
          {
            speaker: "A",
            ja: "最近の一眼レフカメラも興味がありますか？",
            zh: "最近對單眼相機也有興趣嗎？"
          },
          {
            speaker: "B",
            ja: "欲しいですが、重そうで迷っています（笑）。",
            zh: "很想要，但感覺好重所以還在猶豫（笑）。"
          }
        ]
      },
      {
        id: 30,
        category: "出差旅行與休閒生活",
        title: "キャンプとグランピング (露營與奢華露營)",
        technique: "探討戶外風潮與接觸大自然的方式",
        dialogue: [
          {
            speaker: "A",
            ja: "最近流行りのキャンプって興味ありますか？",
            zh: "最近很流行露營，你有興趣嗎？"
          },
          {
            speaker: "B",
            ja: "本格的なキャンプは難しそうですが、グランピングなら行ってみたいです。",
            zh: "硬核的露營感覺好難，不過奢華露營（Glamping）的話會想去試試看。"
          },
          {
            speaker: "A",
            ja: "道具が無くても手ぶらで行けるのが魅力ですよね。",
            zh: "不用帶道具空手就能去就是最大的魅力呢。"
          },
          {
            speaker: "B",
            ja: "大自然の中でバーベキューをするのが楽しみです！",
            zh: "好期待在大自然裡面烤肉喔！"
          }
        ]
      },
      {
        id: 31,
        category: "出差旅行與休閒生活",
        title: "新幹線の過ごし方 (新幹線上的長途時光)",
        technique: "出差族經典話題，聊聊座位與便當選擇",
        dialogue: [
          {
            speaker: "A",
            ja: "新幹線に乗るときは、富士山側の席を選びますか？",
            zh: "搭新幹線的時候，你會特地選看的到富士山那一側的座位嗎？"
          },
          {
            speaker: "B",
            ja: "絶対E席（富士山側）を予約します！見えると嬉しくなりますよね。",
            zh: "絕對會預約 E 席（富士山側）！看得到的話心情會變好呢。"
          },
          {
            speaker: "A",
            ja: "駅弁も楽しみの一つですよね。",
            zh: "車站便當也是樂趣之一對吧。"
          },
          {
            speaker: "B",
            ja: "ビールとシウマイ弁当を買うのが定番の楽しみです！",
            zh: "買啤酒配燒賣便當是我最經典的享用模式！"
          }
        ]
      },
      {
        id: 32,
        category: "出差旅行與休閒生活",
        title: "季節のイベント (花火大會與花見櫻花季)",
        technique: "探討日本四季風物詩，凝聚節慶氛圍",
        dialogue: [
          {
            speaker: "A",
            ja: "夏はお祭りや花火大会に行かれましたか？",
            zh: "夏天有去參加祭典或是看花火大會嗎？"
          },
          {
            speaker: "B",
            ja: "はい、近くの花火大会に浴衣を着て行きました！",
            zh: "有啊，我穿著浴衣去了附近的烟火大會！"
          },
          {
            speaker: "A",
            ja: "浴衣、風情があってステキですね！",
            zh: "浴衣真的很有日式風情，太棒了！"
          },
          {
            speaker: "B",
            ja: "人は多かったですが、いい夏の思い出になりました。",
            zh: "雖然人很多，但成了很棒的夏天回憶。"
          }
        ]
      },

      // 分類 5: 夢想假設與人生話題 (33-40)
      {
        id: 33,
        category: "夢想假設與人生話題",
        title: "宝くじが当たったら (如果中了彩券大獎)",
        technique: "趣味假設性話題，快速活絡氣氛",
        dialogue: [
          {
            speaker: "A",
            ja: "もし宝くじで1億円当たったら何に使いますか？",
            zh: "如果彩券中了1億日圓，你會拿來幹嘛？"
          },
          {
            speaker: "B",
            ja: "まずは家を買って、残りは世界一周旅行に使いたいです！",
            zh: "首先先買間房子，剩下的拿去環遊世界！"
          },
          {
            speaker: "A",
            ja: "夢がありますね！仕事は辞めますか？",
            zh: "真有夢想！那會把工作辭掉嗎？"
          },
          {
            speaker: "B",
            ja: "うーん、ヒマになりそうなので少しは続けるかもしれません（笑）。",
            zh: "唔，感覺會變得很閒，說不定還是會稍微繼續工作吧（笑）。"
          }
        ]
      },
      {
        id: 34,
        category: "夢想假設與人生話題",
        title: "タイムトラベル (時光旅行想去過去或未來)",
        technique: "科幻思考話題，展現想像力與個人情懷",
        dialogue: [
          {
            speaker: "A",
            ja: "タイムマシンがあったら、過去と未来どちらに行きたいですか？",
            zh: "如果有時光機，你比較想去過去還是未來？"
          },
          {
            speaker: "B",
            ja: "過去に行って、歴史的な瞬間を見てみたいですね。",
            zh: "想去過去，親眼看看歷史性的那一刻呢。"
          },
          {
            speaker: "A",
            ja: "どの時代に行ってみたいですか？",
            zh: "最想去哪一個時代看看？"
          },
          {
            speaker: "B",
            ja: "江戸時代に行って、当時の町並みを歩いてみたいです！",
            zh: "想去江戶時代，在當時的街道上散散步！"
          }
        ]
      },
      {
        id: 35,
        category: "夢想假設與人生話題",
        title: "無人島に一つだけ持っていくなら (帶一樣東西去無人島)",
        technique: "經典破冰問題，測驗務實度與幽默感",
        dialogue: [
          {
            speaker: "A",
            ja: "無人島に一つだけ持っていけるとしたら、何を持っていきますか？",
            zh: "如果只能帶一樣東西去無人島，你會帶什麼？"
          },
          {
            speaker: "B",
            ja: "現実的ですが、サバイバルナイフですね。",
            zh: "雖然很務實，但應該是野外求生刀吧。"
          },
          {
            speaker: "A",
            ja: "さすが実用的ですね！私はスマホって言いそうになりました（笑）。",
            zh: "不愧是你真實用！我剛差點要說手機了（笑）。"
          },
          {
            speaker: "B",
            ja: "電波が届かないから意味がないですよ！（笑）",
            zh: "那裡沒訊號所以帶了也沒用啦！（笑）"
          }
        ]
      },
      {
        id: 36,
        category: "夢想假設與人生話題",
        title: "超能力が手に入るなら (最想獲得哪種超能力)",
        technique: "童心未泯的假設，帶出輕鬆笑點",
        dialogue: [
          {
            speaker: "A",
            ja: "一つだけ超能力が使えるとしたら何がいいですか？",
            zh: "如果能獲得一種超能力，你希望是什麼？"
          },
          {
            speaker: "B",
            ja: "どこでもドアみたいに「瞬間移動」ができる能力です！",
            zh: "希望是像任意門一樣能夠『瞬間移動』的能力！"
          },
          {
            speaker: "A",
            ja: "満員電車に乗らなくて済みますね！",
            zh: "這樣就不需要擠滿員電車了呢！"
          },
          {
            speaker: "B",
            ja: "そうなんです！毎朝ギリギリまで寝られます（笑）。",
            zh: "就是說啊！每天早上都能睡到最後一刻了（笑）。"
          }
        ]
      },
      {
        id: 37,
        category: "夢想假設與人生話題",
        title: "人生で最後に食べたいもの (人生最後一餐想吃什麼)",
        technique: "關於美食與情感連結的浪漫想像",
        dialogue: [
          {
            speaker: "A",
            ja: "「人生最後に食べたいもの」って決まっていますか？",
            zh: "你心裡有決定好『人生最後一餐想吃什麼』了嗎？"
          },
          {
            speaker: "B",
            ja: "やっぱり母が作ったお味噌汁と塩おにぎりですね。",
            zh: "果然還是媽媽做的味噌湯跟鹽味飯糰呢。"
          },
          {
            speaker: "A",
            ja: "シンプルですが一番心に染みる味ですね。",
            zh: "雖然簡單，但卻是最暖心滋味呢。"
          },
          {
            speaker: "B",
            ja: "結局そういう家庭の味が一番好きなんです。",
            zh: "到頭來還是最喜歡那樣的家常味道。"
          }
        ]
      },
      {
        id: 38,
        category: "夢想假設與人生話題",
        title: "生まれ変わるなら (下輩子想變成什麼)",
        technique: "哲學性幽默問答，表達生活嚮往",
        dialogue: [
          {
            speaker: "A",
            ja: "もし生まれ変わるなら、何になりたいですか？",
            zh: "如果下輩子能轉世，你想變成什麼呢？"
          },
          {
            speaker: "B",
            ja: "お金持ちの家にかわいがられる「猫」になりたいです（笑）。",
            zh: "想變成被好人家寵愛的『貓咪』呢（笑）。"
          },
          {
            speaker: "A",
            ja: "一日中寝ていられて最高ですね！",
            zh: "一整天都可以睡覺，太棒了吧！"
          },
          {
            speaker: "B",
            ja: "悩みもなさそうで羨ましいですよね。",
            zh: "感覺完全沒有煩惱，真令人羨慕呢。"
          }
        ]
      },
      {
        id: 39,
        category: "夢想假設與人生話題",
        title: "一生もののお買い物 (買過最值得的高價物品)",
        technique: "分享價值觀與對生活的投資經驗",
        dialogue: [
          {
            speaker: "A",
            ja: "今までで「買って一番よかった高価な買い物」は何ですか？",
            zh: "至今為止你覺得『買過最值得的高價物品』是什麼？"
          },
          {
            speaker: "B",
            ja: "奮発して買った高級なベッドマットレスです。睡眠の質が変わりました。",
            zh: "咬牙狠下心買的高級床墊。睡眠品質完全改變了。"
          },
          {
            speaker: "A",
            ja: "睡眠への投資は価値がありますね！",
            zh: "對睡眠的投資真的很值得呢！"
          },
          {
            speaker: "B",
            ja: "毎日使うものなので満足度がすごく高いですよ。",
            zh: "因為是每天都要用的東西，滿意度真的非常高喔。"
          }
        ]
      },
      {
        id: 40,
        category: "夢想假設與人生話題",
        title: "老後の理想の生活 (理想的老後退休生活)",
        technique: "暢想未來，表達平淡寧靜的渴望",
        dialogue: [
          {
            speaker: "A",
            ja: "将来、老後はどんな生活を過ごしたいですか？",
            zh: "將來退休老後，你想過著什麼樣的生活？"
          },
          {
            speaker: "B",
            ja: "田舎の静かな場所で、庭で野菜を育てながら暮らしたいです。",
            zh: "想在鄉下清靜的地方，一邊在庭院種菜一邊生活。"
          },
          {
            speaker: "A",
            ja: "のんびりしていて素敵ですね。憧れます。",
            zh: "悠閒自在好棒喔，令人嚮往。"
          },
          {
            speaker: "B",
            ja: "そのためにも今しっかり働かないとですね！",
            zh: "為了這個目標，現在得好好加油工作才行呢！"
          }
        ]
      }
    ];

    /* ==========================================================================
       APP STATE & GLOBAL CONTROLS
       ========================================================================== */
    let state = {
      showFurigana: true,
      currentCategory: '全部',
      searchQuery: '',
      practiceMode: 'standard', // 'standard', 'flip', 'mask'
      maskTarget: 'zh', // 'zh' or 'ja'
      currentFeaturedId: null,
      timerSeconds: 120,
      timerInterval: null,
      isTimerRunning: false,
      speechSynth: window.speechSynthesis
    };

    const categories = ['全部', '性格習慣與生活風格', '飲食與品味體驗', '職場日常與提神靈感', '出差旅行與休閒生活', '夢想假設與人生話題'];

    window.onload = function() {
      renderCategoryFilters();
      renderTopics();
      setupSpeechSynth();
    };

    /* Speech Synthesis Init Check */
    function setupSpeechSynth() {
      if ('speechSynthesis' in window) {
        window.speechSynthesis.onvoiceschanged = () => {
          // pre-load voices
          window.speechSynthesis.getVoices();
        };
      }
    }

    /* ==========================================================================
       TTS VOICE SPEECH FUNCTIONS
       ========================================================================== */
    function speakText(textWithRuby, event) {
      if (event) event.stopPropagation();
      
      if (!('speechSynthesis' in window)) {
        showToast('您的瀏覽器不支援語音朗讀功能');
        return;
      }

      // Strip ruby tags for clean speech text
      const tempDiv = document.createElement('div');
      tempDiv.innerHTML = textWithRuby;
      // remove rt tags
      const rts = tempDiv.querySelectorAll('rt');
      rts.forEach(rt => rt.remove());
      const cleanText = tempDiv.textContent || tempDiv.innerText || '';

      // Cancel ongoing speech
      window.speechSynthesis.cancel();

      const utterance = new SpeechSynthesisUtterance(cleanText);
      utterance.lang = 'ja-JP';

      // Speed rate from control
      const rateSelect = document.getElementById('tts-rate-select');
      utterance.rate = parseFloat(rateSelect.value) || 1.0;

      // Find Japanese Voice if available
      const voices = window.speechSynthesis.getVoices();
      const jaVoice = voices.find(v => v.lang.includes('ja') || v.lang.includes('JP'));
      if (jaVoice) {
        utterance.voice = jaVoice;
      }

      window.speechSynthesis.speak(utterance);
    }

    function speakFullDialogue(topicId, event) {
      if (event) event.stopPropagation();

      const topic = TOPICS_DATA.find(t => t.id === topicId);
      if (!topic) return;

      const fullText = topic.dialogue.map(d => {
        const temp = document.createElement('div');
        temp.innerHTML = d.ja;
        temp.querySelectorAll('rt').forEach(rt => rt.remove());
        return temp.textContent;
      }).join('。 ');

      speakText(fullText);
    }

    /* ==========================================================================
       FURIGANA & MODE CONTROLS
       ========================================================================== */
    function toggleFurigana() {
      state.showFurigana = !state.showFurigana;
      const statusText = document.getElementById('furigana-status-text');
      
      if (state.showFurigana) {
        document.body.classList.remove('hide-furigana');
        statusText.textContent = '顯示';
      } else {
        document.body.classList.add('hide-furigana');
        statusText.textContent = '隱藏';
      }
    }

    function setMode(mode) {
      state.practiceMode = mode;
      
      // Update Button UI
      ['standard', 'flip', 'mask'].forEach(m => {
        const btn = document.getElementById(`mode-btn-${m}`);
        if (m === mode) {
          btn.className = 'px-3.5 py-1.5 rounded-lg text-sm font-medium transition bg-white shadow-sm text-matcha-700 font-bold';
        } else {
          btn.className = 'px-3.5 py-1.5 rounded-lg text-sm font-medium transition text-slate-600 hover:text-slate-900';
        }
      });

      // Show/Hide Mask Controls
      const maskControls = document.getElementById('mask-controls');
      if (mode === 'mask') {
        maskControls.classList.remove('hidden');
      } else {
        maskControls.classList.add('hidden');
      }

      renderTopics();
      if (state.currentFeaturedId) {
        renderFeaturedCard(state.currentFeaturedId);
      }
    }

    function setMaskTarget(target) {
      state.maskTarget = target;
      const zhBtn = document.getElementById('mask-zh-btn');
      const jaBtn = document.getElementById('mask-ja-btn');

      if (target === 'zh') {
        zhBtn.className = 'px-2 py-1 rounded bg-amber-600 text-white font-medium';
        jaBtn.className = 'px-2 py-1 rounded bg-white text-amber-800 border border-amber-300';
      } else {
        jaBtn.className = 'px-2 py-1 rounded bg-amber-600 text-white font-medium';
        zhBtn.className = 'px-2 py-1 rounded bg-white text-amber-800 border border-amber-300';
      }

      renderTopics();
      if (state.currentFeaturedId) {
        renderFeaturedCard(state.currentFeaturedId);
      }
    }

    function toggleMask(element) {
      if (state.practiceMode !== 'mask') return;
      element.classList.toggle('mask-revealed');
    }

    /* ==========================================================================
       CATEGORY FILTERS & SEARCH
       ========================================================================== */
    function renderCategoryFilters() {
      const container = document.getElementById('category-filters');
      container.innerHTML = categories.map(cat => `
        <button onclick="selectCategory('${cat}')" class="px-3 py-1 rounded-lg text-xs font-medium transition ${
          state.currentCategory === cat 
            ? 'bg-matcha-600 text-white shadow-sm' 
            : 'bg-slate-100 text-slate-600 hover:bg-slate-200'
        }">
          ${cat}
        </button>
      `).join('');
    }

    function selectCategory(cat) {
      state.currentCategory = cat;
      renderCategoryFilters();
      renderTopics();
    }

    function handleSearch() {
      state.searchQuery = document.getElementById('search-input').value.trim().toLowerCase();
      renderTopics();
    }

    function getFilteredTopics() {
      return TOPICS_DATA.filter(topic => {
        const matchesCategory = state.currentCategory === '全部' || topic.category === state.currentCategory;
        const matchesSearch = !state.searchQuery || 
          topic.title.toLowerCase().includes(state.searchQuery) ||
          topic.technique.toLowerCase().includes(state.searchQuery) ||
          topic.dialogue.some(d => d.ja.includes(state.searchQuery) || d.zh.includes(state.searchQuery));
        return matchesCategory && matchesSearch;
      });
    }

    /* ==========================================================================
       RANDOM PICKER & FEATURED DISPLAY
       ========================================================================== */
    function drawRandomTopic() {
      const randomIndex = Math.floor(Math.random() * TOPICS_DATA.length);
      const selected = TOPICS_DATA[randomIndex];
      state.currentFeaturedId = selected.id;

      renderFeaturedCard(selected.id);
      
      const featuredSec = document.getElementById('featured-section');
      featuredSec.classList.remove('hidden');
      featuredSec.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
    }

    function renderFeaturedCard(id) {
      const topic = TOPICS_DATA.find(t => t.id === id);
      if (!topic) return;

      const featuredSec = document.getElementById('featured-section');
      featuredSec.innerHTML = `
        <div class="bg-gradient-to-br from-amber-50 to-orange-50 border-2 border-amber-300 rounded-2xl p-6 shadow-md relative">
          <div class="flex items-center justify-between mb-4">
            <span class="px-3 py-1 bg-amber-500 text-white font-bold text-xs rounded-full uppercase tracking-wider flex items-center space-x-1">
              <i class="fa-solid fa-[#000] fa-star"></i>
              <span>隨機抽題結果</span>
            </span>
            <button onclick="document.getElementById('featured-section').classList.add('hidden')" class="text-slate-400 hover:text-slate-600">
              <i class="fa-solid fa-[#000] fa-xmark text-lg"></i>
            </button>
          </div>

          <div class="mb-4">
            <h3 class="text-xl font-bold text-slate-800 font-jp">${topic.id}. ${topic.title}</h3>
            <p class="text-xs text-amber-800 mt-1"><i class="fa-solid fa-[#000] fa-lightbulb mr-1"></i>技巧解析：${topic.technique}</p>
          </div>

          ${generateDialogueHTML(topic)}

          <div class="mt-4 pt-3 border-t border-amber-200/60 flex flex-wrap items-center justify-between gap-2 text-xs">
            <button onclick="speakFullDialogue(${topic.id}, event)" class="px-3 py-1.5 bg-matcha-600 hover:bg-matcha-700 text-white rounded-lg font-medium transition flex items-center space-x-1.5 shadow-sm">
              <i class="fa-solid fa-[#000] fa-volume-high"></i>
              <span>全文日語朗讀</span>
            </button>
            <button onclick="copyDialogueText(${topic.id})" class="px-3 py-1.5 bg-white border border-amber-300 text-amber-900 rounded-lg font-medium hover:bg-amber-100 transition flex items-center space-x-1">
              <i class="fa-solid fa-[#000] fa-copy"></i>
              <span>複製對話文本</span>
            </button>
          </div>
        </div>
      `;
    }

    /* ==========================================================================
       CARD DIALOGUE GENERATOR (MODES 1, 2, 3)
       ========================================================================== */
    function generateDialogueHTML(topic) {
      // MODE 2: 3D FLIP CARD MODE
      if (state.practiceMode === 'flip') {
        return `
          <div class="perspective-1000 w-full min-h-[220px] cursor-pointer" onclick="toggleCardFlip(this)">
            <div class="flip-card-inner border border-woodborder rounded-2xl bg-white shadow-sm hover:shadow-md transition">
              <!-- FRONT (Japanese) -->
              <div class="flip-card-front p-4 bg-white rounded-2xl flex flex-col justify-between border border-matcha-200">
                <div class="space-y-3">
                  <div class="text-xs font-bold text-matcha-600 flex items-center justify-between">
                    <span><i class="fa-solid fa-[#000] fa-language mr-1"></i>正面 (日文)</span>
                    <span class="text-slate-400 font-normal">點擊翻面 <i class="fa-solid fa-[#000] fa-rotate"></i></span>
                  </div>
                  <div class="space-y-2">
                    ${topic.dialogue.map(d => `
                      <div class="text-sm font-jp leading-relaxed">
                        <span class="font-bold text-matcha-700 mr-1">${d.speaker}:</span>
                        <span>${d.ja}</span>
                        <button onclick="speakText('${escapeQuotes(d.ja)}', event)" class="ml-1 text-slate-400 hover:text-matcha-600 transition">
                          <i class="fa-solid fa-[#000] fa-volume-high text-xs"></i>
                        </button>
                      </div>
                    `).join('')}
                  </div>
                </div>
                <div class="text-center text-xs text-slate-400 pt-2 border-t border-slate-100">
                  <i class="fa-solid fa-[#000] fa-hand-pointer mr-1"></i>點擊翻轉查看中文翻譯與解析
                </div>
              </div>

              <!-- BACK (Chinese & Technique) -->
              <div class="flip-card-back p-4 bg-slate-50 rounded-2xl flex flex-col justify-between border border-slate-300">
                <div class="space-y-3">
                  <div class="text-xs font-bold text-slate-500 flex items-center justify-between">
                    <span><i class="fa-solid fa-[#000] fa-circle-info mr-1"></i>背面 (翻譯與解析)</span>
                    <span class="text-slate-400 font-normal">點擊翻回 <i class="fa-solid fa-[#000] fa-rotate"></i></span>
                  </div>
                  <div class="p-2 bg-amber-50 border border-amber-200 rounded-lg text-xs text-amber-800">
                    <i class="fa-solid fa-[#000] fa-lightbulb mr-1"></i><strong>對話技巧：</strong>${topic.technique}
                  </div>
                  <div class="space-y-2">
                    ${topic.dialogue.map(d => `
                      <div class="text-xs text-slate-700 leading-relaxed">
                        <span class="font-bold text-slate-900 mr-1">${d.speaker}:</span>
                        <span>${d.zh}</span>
                      </div>
                    `).join('')}
                  </div>
                </div>
              </div>
            </div>
          </div>
        `;
      }

      // MODE 1 & 3 (Standard Bilingual / Masking Mode)
      return `
        <div class="space-y-3">
          ${topic.dialogue.map(d => {
            const isMaskJa = state.practiceMode === 'mask' && state.maskTarget === 'ja';
            const isMaskZh = state.practiceMode === 'mask' && state.maskTarget === 'zh';

            return `
              <div class="p-3 rounded-xl bg-slate-50/80 border border-slate-100 hover:border-slate-200 transition space-y-1">
                <!-- Japanese Line -->
                <div class="flex items-start justify-between">
                  <div class="text-sm font-jp leading-relaxed text-slate-800 ${isMaskJa ? 'mask-text' : ''}" onclick="toggleMask(this)">
                    <span class="font-bold text-matcha-600 mr-1 select-none">${d.speaker}:</span>
                    <span>${d.ja}</span>
                  </div>
                  <button onclick="speakText('${escapeQuotes(d.ja)}', event)" title="單句朗讀" class="ml-2 text-slate-400 hover:text-matcha-600 transition p-1">
                    <i class="fa-solid fa-[#000] fa-volume-high text-xs"></i>
                  </button>
                </div>
                <!-- Chinese Translation Line -->
                <div class="text-xs text-slate-500 pl-4 border-l-2 border-matcha-200 ${isMaskZh ? 'mask-text' : ''}" onclick="toggleMask(this)">
                  ${d.zh}
                </div>
              </div>
            `;
          }).join('')}
        </div>
      `;
    }

    function toggleCardFlip(cardContainer) {
      const inner = cardContainer.querySelector('.flip-card-inner');
      if (inner) {
        inner.classList.toggle('flipped');
      }
    }

    function escapeQuotes(str) {
      return str.replace(/'/g, "\\'").replace(/"/g, '&quot;');
    }

    /* ==========================================================================
       RENDER TOPICS GRID
       ========================================================================== */
    function renderTopics() {
      const container = document.getElementById('topics-container');
      const filtered = getFilteredTopics();

      if (filtered.length === 0) {
        container.innerHTML = `
          <div class="col-span-full py-12 text-center text-slate-400 space-y-2">
            <i class="fa-solid fa-[#000] fa-magnifying-glass-minus text-4xl"></i>
            <p class="text-sm">找不到符合條件的話題，請嘗試其他關鍵字。</p>
          </div>
        `;
        return;
      }

      container.innerHTML = filtered.map(topic => `
        <div class="bg-white rounded-2xl p-5 border border-woodborder shadow-sm hover:shadow-md transition flex flex-col justify-between space-y-4">
          <!-- Card Header -->
          <div>
            <div class="flex items-center justify-between text-xs text-slate-400 mb-2">
              <span class="px-2 py-0.5 rounded bg-matcha-50 text-matcha-700 font-medium">${topic.category}</span>
              <span class="font-mono text-slate-300">#${topic.id.toString().padStart(2, '0')}</span>
            </div>
            
            <h3 class="text-lg font-bold text-slate-800 font-jp mb-1">${topic.title}</h3>
            <p class="text-xs text-slate-500 mb-3"><i class="fa-solid fa-[#000] fa-lightbulb text-amber-500 mr-1"></i>${topic.technique}</p>

            <!-- Card Dialogue Body -->
            ${generateDialogueHTML(topic)}
          </div>

          <!-- Card Action Footer -->
          <div class="pt-3 border-t border-slate-100 flex items-center justify-between text-xs">
            <button onclick="speakFullDialogue(${topic.id}, event)" class="px-3 py-1.5 bg-matcha-50 hover:bg-matcha-100 text-matcha-700 font-medium rounded-lg transition flex items-center space-x-1">
              <i class="fa-solid fa-[#000] fa-volume-high"></i>
              <span>全文朗讀</span>
            </button>

            <button onclick="copyDialogueText(${topic.id})" class="text-slate-400 hover:text-slate-600 transition flex items-center space-x-1 px-2 py-1">
              <i class="fa-solid fa-[#000] fa-copy"></i>
              <span>複製</span>
            </button>
          </div>
        </div>
      `).join('');
    }

    /* ==========================================================================
       TIMER FUNCTIONS
       ========================================================================== */
    function toggleTimerModal() {
      const modal = document.getElementById('timer-modal');
      modal.classList.toggle('hidden');
    }

    function startTimer() {
      if (state.isTimerRunning) return;
      state.isTimerRunning = true;
      
      document.getElementById('timer-start-btn').classList.add('hidden');
      document.getElementById('timer-pause-btn').classList.remove('hidden');

      state.timerInterval = setInterval(() => {
        if (state.timerSeconds > 0) {
          state.timerSeconds--;
          updateTimerDisplay();
        } else {
          pauseTimer();
          showToast('⏰ 2分鐘時間到！Small Talk 結束！');
        }
      }, 1000);
    }

    function pauseTimer() {
      state.isTimerRunning = false;
      clearInterval(state.timerInterval);
      document.getElementById('timer-start-btn').classList.remove('hidden');
      document.getElementById('timer-pause-btn').classList.add('hidden');
    }

    function resetTimer() {
      pauseTimer();
      state.timerSeconds = 120;
      updateTimerDisplay();
    }

    function updateTimerDisplay() {
      const mins = Math.floor(state.timerSeconds / 60).toString().padStart(2, '0');
      const secs = (state.timerSeconds % 60).toString().padStart(2, '0');
      document.getElementById('timer-display').textContent = `${mins}:${secs}`;
    }

    /* ==========================================================================
       UTILITIES & TOAST
       ========================================================================== */
    function scrollToTopics() {
      document.getElementById('topics-container').scrollIntoView({ behavior: 'smooth' });
    }

    function copyDialogueText(topicId) {
      const topic = TOPICS_DATA.find(t => t.id === topicId);
      if (!topic) return;

      const fullText = `【${topic.title}】\n` + topic.dialogue.map(d => {
        const temp = document.createElement('div');
        temp.innerHTML = d.ja;
        temp.querySelectorAll('rt').forEach(rt => rt.remove());
        const cleanJa = temp.textContent;
        return `${d.speaker}: ${cleanJa}\n   ${d.zh}`;
      }).join('\n');

      // ExecCommand Fallback for Clipboard
      const textarea = document.createElement('textarea');
      textarea.value = fullText;
      document.body.appendChild(textarea);
      textarea.select();
      try {
        document.execCommand('copy');
        showToast('已成功複製對話文本！');
      } catch (err) {
        showToast('複製失敗，請手動複製');
      }
      document.body.removeChild(textarea);
    }

    function showToast(msg) {
      const toast = document.getElementById('toast');
      const toastMsg = document.getElementById('toast-msg');
      toastMsg.textContent = msg;

      toast.classList.remove('opacity-0', 'pointer-events-none', 'translate-y-4');
      toast.classList.add('opacity-100', 'translate-y-0');

      setTimeout(() => {
        toast.classList.remove('opacity-100', 'translate-y-0');
        toast.classList.add('opacity-0', 'pointer-events-none', 'translate-y-4');
      }, 2500);
    }
  </script>
</body>
</html>
