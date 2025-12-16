# Thailand
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ThaiTrip 泰國旅遊助手</title>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Vue 3 -->
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    
    <!-- Phosphor Icons (圖示庫) -->
    <script src="https://unpkg.com/@phosphor-icons/web"></script>

    <!-- Google Fonts: Noto Sans TC -->
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300;400;500;700&display=swap" rel="stylesheet">

    <style>
        body {
		
            font-family: 'Noto Sans TC', sans-serif;
            /* 應用使用者要求的新色碼 #fffaf0 (App 外部底色) */
            background-color: #fffaf0; 
            -webkit-tap-highlight-color: transparent;
        }
        
        /* 隱藏 Scrollbar 但保持滾動功能 */
        .no-scrollbar::-webkit-scrollbar {
            display: none;
        }
        .no-scrollbar {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }

        /* 模擬 iOS 底部安全區域 */
        .safe-bottom {
            padding-bottom: env(safe-area-inset-bottom);
        }

        /* 自定義動畫 */
        .fade-enter-active, .fade-leave-active {
            transition: opacity 0.2s ease;
        }
        .fade-enter-from, .fade-leave-to {
            opacity: 0;
        }
        
        /* Modal 彈出動畫 */
        @keyframes slide-up {
            from { transform: translateY(100%); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }
        .animate-slide-up {
            animation: slide-up 0.3s cubic-bezier(0.16, 1, 0.3, 1);
        }

        /* 匹配圖片中的頭部漸變色和圓角 */
        .app-header-gradient {
            /* 圖片中的漸變色: 暖黃色到薄荷綠 */
            background: linear-gradient(135deg, #FFD54F 0%, #8AC7B1 100%);
            border-bottom-left-radius: 50px;
            border-bottom-right-radius: 50px;
            height: 120px; 
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1); 
        }

    </style>
    
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        // Primary: The color of the main action button (yellow/gold)
                        primary: '#FFC107', 
                        // Secondary: The color of the detail icons (muted red)
                        secondary: '#D32F2F', 
                        // Mint Cyan: 使用者要求的色碼
                        'mint-cyan': '#4db6ac', 
                        // 新的背景色 (#fff8dc - App 內部底色，保持不變)
                        bg_app: '#fff8dc', 
                        surface: 'f#fffff',
                        // 依照使用者要求新增的深綠色
                        'deep-green': '#2e6220', 
                    }
                }
            }
        }
    </script>
</head>
<body class="text-slate-800 h-screen overflow-hidden flex flex-col">

    <!-- 應用程式容器，根據不同螢幕寬度調整最大寬度 -->
    <div id="app" class="flex flex-col h-full w-full max-w-xs sm:max-w-sm md:max-w-md lg:max-w-lg xl:max-w-xl 2xl:max-w-2xl mx-auto bg-bg_app relative shadow-2xl overflow-hidden">
        
        <!-- VISUAL HEADER (匹配圖片風格) -->
        <div class="relative z-10">
            <!-- Gradient Background Area -->
            <div class="app-header-gradient w-[94%] mx-auto pt-8 px-5 pb-5 flex flex-col justify-end">
                <!-- 內部內容已移除，主要靠絕對定位的 header 來顯示標題 -->
            </div>

            <!-- DAY SELECTOR CARD (已修改: w-[94%] mx-auto 讓其與 header 對齊) -->
            <div v-if="activeTab === 'itinerary'" class="relative -mt-6 z-20 w-[94%] mx-auto">
                 <div class="bg-white p-3 rounded-2xl shadow-xl border border-slate-100">
                    <div class="flex items-center justify-between mb-2 text-slate-500 text-sm font-medium">
                        <div class="flex items-center gap-1">
                             <i class="ph ph-calendar-blank text-lg text-green-500"></i>
                            每日探險
                        </div>
                        <span class="bg-green-100 text-green-600 text-xm font-bold px-2 py-0.5 rounded-full">Day {{ currentDayIndex + 1 }}</span>
                    </div>
                    <!-- 天數選擇器按鈕 -->
                    <div class="flex gap-2 overflow-x-auto no-scrollbar pt-1">
                        <button 
                            v-for="(day, index) in itineraryDays" 
                            :key="index"
                            @click="currentDayIndex = index"
                            :class="[
                                'flex-shrink-0 text-xl font-medium transition-all px-2 py-1 rounded-lg',
                                currentDayIndex === index 
                                ? 'bg-primary text-slate-900 shadow-md' 
                                : 'bg-slate-100 text-slate-500 hover:bg-slate-200'
                            ]"
                        >
                            03/{{ 8 + index }}
                        </button>
                    </div>
                </div>
            </div>
            
            <!-- App Logo/Settings (已修改: 替換為「企鵝皮皮的芭達雅探險」) -->
            <header class="absolute top-0 left-0 right-0 py-3 z-30">
                <div class="flex justify-between items-center w-[94%] mx-auto px-5">
                    <!-- 新標題: 企鵝皮皮的芭達雅探險 -->
                    <div>
                        <h1 class="text-xl font-bold text-white flex items-center gap-2 leading-tight">
                            <span class="text-3xl">🐧</span> 企鵝皮皮的芭達雅探險
                        </h1>
                    </div>
                    <!-- 設定按鈕 -->
                    <button @click="showSettings = true" class="text-white hover:text-white/80 transition bg-white/20 p-2 rounded-full">
                        <i class="ph ph-gear text-xl"></i>
                    </button>
                </div>
            </header>
        </div>

        <!-- 主要內容區 (可滾動) - 搭配 app 容器背景色，這裡不需要額外背景色 -->
        <main class="flex-1 overflow-y-auto no-scrollbar relative pt-0">
            
            <!-- 頁面 1: 行程表 -->
            <div v-if="activeTab === 'itinerary'" class="p-4 space-y-4 pb-24">
                



                <!-- 當日行程列表 -->
                <div v-if="currentItinerary.length > 0" class="space-y-4 pt-4">
                    <div 
                        v-for="(item, idx) in sortedCurrentItinerary" 
                        :key="item.id"
                        class="bg-white p-4 rounded-2xl shadow-lg border-l-4 border-mint-cyan flex gap-4 items-start relative group"
                    >
                        <!-- 時間軸視覺 -->
                        <div class="flex flex-col items-center mt-1">
                            <!-- 時間 (使用輔色) -->
                            <div class="w-12 text-center">
                                <span class="text-sm font-bold text-secondary">{{ formatTime(item.time) }}</span>
                            </div>
                            <!-- 連接線 -->
                            <div class="h-full w-0.5 bg-slate-100 my-1 rounded-full group-last:hidden"></div>
                        </div>

                        <div class="flex-1">
                            <div class="flex justify-between items-start">
                                <h3 class="font-bold text-slate-800 text-lg leading-tight">{{ item.title }}</h3>
                                <!-- START: 新增天氣資訊並保留編輯/刪除按鈕 -->
                                <div class="flex items-center gap-3">
                                    <!-- 天氣資訊 (模擬) -->
                                    <div v-if="item.weather" class="flex items-center gap-1 text-xs font-bold" :class="item.weather.color">
                                        <i class="ph text-lg" :class="item.weather.icon"></i>
                                        <span>{{ item.weather.temp }}</span>
                                    </div>
                                    <!-- 編輯/刪除按鈕 (保留) -->
                                    <div class="flex gap-2 text-sm">
                                        <button @click="editItem(item)" class="text-slate-300 hover:text-primary"><i class="ph ph-pencil-simple"></i></button>
                                        <button @click="deleteItem(item.id)" class="text-slate-300 hover:text-red-500"><i class="ph ph-trash"></i></button>
                                    </div>
                                </div>
                                <!-- END: 新增天氣資訊並保留編輯/刪除按鈕 -->
                            </div>
                            
                            <!-- 地點 (使用輔色圖示) -->
                            <div v-if="item.location" class="flex items-center gap-1 text-slate-500 text-sm mt-1">
                                <i class="ph ph-map-pin text-secondary"></i>
                                <span class="text-slate-600">{{ item.location }}</span>
                            </div>

                            <p v-if="item.note" class="text-slate-400 text-sm mt-2 bg-slate-50 p-2 rounded-lg whitespace-pre-line">
                                {{ item.note }}
                            </p>

                            <!-- 導航按鈕 (顏色調整為藍色，與輔色區分) -->
                            <a 
                                v-if="item.location && item.location !== 'TPE桃園機場' && item.location !== 'BKK 素萬那普機場'"
                                :href="`https://www.google.com/maps/search/?api=1&query=${encodeURIComponent(item.location + ' 泰國')}`" 
                                target="_blank"
                                class="mt-3 inline-flex items-center gap-1.5 bg-blue-50 text-blue-600 px-3 py-1.5 rounded-full text-xm font-bold hover:bg-blue-100 transition"
                            >
                                <i class="ph ph-navigation-arrow-fill"></i> 導航
                            </a>
							<a 
                                v-if="item.location == '真理寺'"
                                href="./0308_泰國芭達雅真理寺門票25KK259170816.pdf"  
                                target="_blank"
                                class="mt-3 inline-flex items-center gap-1.5 bg-blue-50 text-blue-600 px-3 py-1.5 rounded-full text-xm font-bold hover:bg-blue-100 transition"
                            >
                                <i class="ph ph-navigation-arrow-fill"></i> 門票
                            </a>
							<a 
                                v-if="item.location == 'Alcazar Cabaret Show'"
                                href="./0308_人妖秀_25KK250970709.pdf"  
                                target="_blank"
                                class="mt-3 inline-flex items-center gap-1.5 bg-blue-50 text-blue-600 px-3 py-1.5 rounded-full text-xm font-bold hover:bg-blue-100 transition"
                            >
                                <i class="ph ph-navigation-arrow-fill"></i> 門票
                            </a>
							<a 
                                v-if="item.location == 'Pattaya Discovery Beach Hotel'"
                                href="./0308_BKK到芭達雅_25KK267580147.pdf"  
                                target="_blank"
                                class="mt-3 inline-flex items-center gap-1.5 bg-blue-50 text-blue-600 px-3 py-1.5 rounded-full text-xm font-bold hover:bg-blue-100 transition"
                            >
                                <i class="ph ph-navigation-arrow-fill"></i> 包車資訊
                            </a>
							<a 
                                v-if="item.location == 'Nong Nooch Tropical Garden'"
                                href="./0309_東芭樂園_25KK250970309.pdf"  
                                target="_blank"
                                class="mt-3 inline-flex items-center gap-1.5 bg-blue-50 text-blue-600 px-3 py-1.5 rounded-full text-xm font-bold hover:bg-blue-100 transition"
                            >
                                <i class="ph ph-navigation-arrow-fill"></i> 門票
                            </a>
							<a 
                                v-if="item.location == 'K Maison Boutique Hotel'"
                                href="./0310_芭達雅到曼谷_25KK262770147.pdf"  
                                target="_blank"
                                class="mt-3 inline-flex items-center gap-1.5 bg-blue-50 text-blue-600 px-3 py-1.5 rounded-full text-xm font-bold hover:bg-blue-100 transition"
                            >
                                <i class="ph ph-navigation-arrow-fill"></i> 包車資訊
                            </a>
							<a 
                                v-if="item.location == 'Baiyoke Sky Hotel'"
                                href="./0310_天空餐廳_25KK250970409.pdf"  
                                target="_blank"
                                class="mt-3 inline-flex items-center gap-1.5 bg-blue-50 text-blue-600 px-3 py-1.5 rounded-full text-xm font-bold hover:bg-blue-100 transition"
                            >
                                <i class="ph ph-navigation-arrow-fill"></i> 餐劵
                            </a>
							<a 
                                v-if="item.location == 'The Erawan Museum'"
                                href="./0312_三頭像_25KK250970009.pdf"  
                                target="_blank"
                                class="mt-3 inline-flex items-center gap-1.5 bg-blue-50 text-blue-600 px-3 py-1.5 rounded-full text-xm font-bold hover:bg-blue-100 transition"
                            >
                                <i class="ph ph-navigation-arrow-fill"></i> 套票
                            </a>
							<a 
                                v-if="item.location == 'Maeklong Railway Market'"
                                href="./0313_鐵道25KK227110294.pdf"  
                                target="_blank"
                                class="mt-3 inline-flex items-center gap-1.5 bg-blue-50 text-blue-600 px-3 py-1.5 rounded-full text-xm font-bold hover:bg-blue-100 transition"
                            >
                                <i class="ph ph-navigation-arrow-fill"></i> 套票門票
                            </a>
                        </div>
                    </div>
                </div>

                <!-- 空狀態 -->
                <div v-else class="flex flex-col items-center justify-center py-10 text-slate-400">
                    <i class="ph ph-airplane-tilt text-6xl mb-2 text-slate-200"></i>
                    <p>這天還沒有行程安排</p>
                    <button @click="openModal" class="mt-4 text-primary font-bold">立即新增</button>
                </div>

                <!-- 底部浮動新增按鈕 -->
                <button 
                    @click="expenseForm.payer"
                    class="fixed bottom-24 right-6 w-14 h-14 bg-primary text-slate-900 rounded-full shadow-lg shadow-primary/40 flex items-center justify-center text-2xl hover:scale-105 transition active:scale-95 z-10"
                >
                    <i class="ph ph-plus"></i>
                </button>
            </div>

            <!-- 頁面 2: 記帳系統 (已完全改為圖片中的樣式) -->
            <div v-if="activeTab === 'expenses'" class="p-4 pb-24 space-y-4">
                
                <!-- 概覽卡片 (New Design: 旅費記帳 TWD) -->
                <!-- 1. 底色改為 #4db6ac (bg-[#4db6ac]) -->
                <div class="bg-[#4db6ac] text-white p-5 rounded-2xl shadow-xl relative overflow-hidden">
                    <div class="relative z-10">
                        <h2 class="font-bold text-lg mb-2">旅費記帳 (TWD)</h2>
                        <!-- 3. 移除「旅程總支出」文字 -->
                        
                        <!-- 2. NT$ 與費用文字顏色改為 #e7c44b (text-[#e7c44b]) -->
                        <h1 class="text-5xl font-extrabold tracking-tight mb-4 text-[#e7c44b]">
                            NT$ {{ totalTWDExpense.toLocaleString() }}
                        </h1>
                        
                        <!-- Member Balance Summary (墊付統計) -->
                        <p class="text-sm font-medium mb-1 text-white/70">目前墊付統計:</p>
                        <div class="space-y-1 text-sm">
                            <div v-for="person in members" :key="person" class="flex justify-between items-center border-b border-white/10 last:border-b-0 py-0.5">
                                <span>{{ person }}</span>
                                <!-- 1. NT$與費用的文字顏色改成白色 -->
                                <span class="text-white">
                                    NT$ {{ Math.abs(memberTWDContribution[person]).toFixed(0) }}
                                </span>
                            </div>
                        </div>
                    </div>
                    <!-- Decorative Circle -->
                    <div class="absolute right-0 top-0 w-32 h-32 bg-white/10 rounded-full transform translate-x-1/3 -translate-y-1/3"></div>
                </div>

                <!-- 記一筆支出 (Inline Form) -->
                <div class="bg-white p-5 rounded-2xl shadow-lg border border-slate-100 space-y-4">
                    
                    <!-- Payer Selector Buttons -->
                    <div class="flex gap-2 overflow-x-auto no-scrollbar pb-2">
                        <button 
                            v-for="m in members" 
                            :key="'payer-'+m"
                            @click="expenseForm.payer = m"
                            class="flex-shrink-0 px-4 py-2 rounded-xl text-sm font-bold transition-all border"
                            :class="expenseForm.payer === m ? 'bg-primary text-deep-green border-primary shadow-md' : 'bg-slate-100 text-slate-500 border-slate-200 hover:bg-slate-200'"
                        >
                            {{ m }} 先付
                        </button>

                        
                            <i class="ph ph-plus-bold"></i>
                        </button>
                    </div>
                 <!-- 消費日期下拉選單 --> 
<div class="flex flex-col mb-3">
  <label class="text-xs font-bold text-gray-500 mb-1">消費日期</label>
  <select v-model="expenseForm.date" 
    class="w-full bg-slate-50 border border-slate-200 rounded-lg p-3 pl-9 text-lg font-extrabold text-slate-800 outline-none focus:border-deep-green focus:ring-1 focus:ring-deep-green"> 
    <option value="2025-03-08">2025-03-08</option>
    <option value="2025-03-09">2025-03-09</option>
    <option value="2025-03-10">2025-03-10</option>
    <option value="2025-03-11">2025-03-11</option>
    <option value="2025-03-12">2025-03-12</option>
    <option value="2025-03-12">2025-03-13</option>
    <option value="2025-03-12">2025-03-14</option>
  </select>
</div>                  
                    <!-- Amount Input (THB & TWD Conversion) -->
                    <div class="grid grid-cols-2 gap-4 items-center">
                        <!-- THB Input -->
                        <div class="space-y-1">
                            <label class="block text-xs font-bold text-slate-500">外幣金額 (THB)</label>
                            <div class="relative">
                                <span class="absolute left-3 top-3.5 text-xl font-bold text-deep-green">฿</span>
                                <input 
                                    type="number" 
                                    v-model.number="expenseForm.amount" 
                                    class="w-full bg-slate-50 border border-slate-200 rounded-lg p-3 pl-9 text-lg font-extrabold text-slate-800 outline-none focus:border-deep-green focus:ring-1 focus:ring-deep-green"
                                >
                            </div>
                        </div>
                        <!-- TWD Display -->
                        <div class="text-right">
                            <label class="block text-xs font-bold text-slate-500">換算台幣 (TWD)</label>
                            <h4 class="text-3xl font-extrabold text-primary">
                                NT$ {{ twdEquivalent.toLocaleString() }}
                            </h4>
                            <p class="text-xs text-slate-400 mt-1">
                                匯率: 1 THB ≈ {{ exchangeRate.toFixed(3) }} TWD
                            </p>
                        </div>
                    </div>

                    <!-- Title Input -->
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">付了什麼? (e.g., 船票)</label>
                        <input 
                                    type="text" 
                                    v-model="expenseForm.title" class="w-full bg-slate-50 border border-slate-200 rounded-lg p-3 pl-9 text-lg font-extrabold text-slate-800 outline-none focus:border-deep-green focus:ring-1 focus:ring-deep-green">

                    <!-- Save Button -->
                    <button @click="saveExpense" class="w-full py-3 mt-4 rounded-xl bg-mint-cyan text-white font-extrabold shadow-lg shadow-mint-cyan/30 flex items-center justify-center gap-2 hover:bg-mint-cyan/90 transition">
                        記一筆 (NT$ {{ twdEquivalent.toLocaleString() }}) <i class="ph ph-note-pencil-fill"></i>
                    </button>
                    
                </div>

                <!-- 支出紀錄列表 -->
                <div>
                    <div class="flex justify-between items-center mb-3">
                        <h3 class="font-bold text-slate-700">歷史紀錄 (TWD)</h3>
                    </div>
                    
                    <div v-if="expenses.length > 0" class="space-y-3">
                        <div v-for="exp in sortedExpenses" :key="exp.id" class="bg-white p-4 rounded-xl shadow-sm flex justify-between items-center border border-slate-100">
                            <div class="flex items-center gap-3">
                                <div class="w-10 h-10 rounded-full bg-mint-cyan/10 text-mint-cyan flex items-center justify-center text-xl">
                                    <i class="ph" :class="getCategoryIcon(exp.category)"></i>
                                </div>
                                <div>
                                    <h4 class="font-bold text-slate-800">{{ exp.title }}</h4>
                                    <p class="text-xs text-slate-400">
                                        <!-- Displaying the day index/date for context -->
                                        <span class="font-medium text-slate-600">Day {{ exp.dayIndex + 1 }} ({{ 8 + exp.dayIndex }}/03)</span>
                                        <span class="text-slate-300 mx-1">|</span>
                                        <span class="font-medium text-slate-600">{{ exp.payer }}</span> 付款
                                    </p>
                                </div>
                            </div>
                            <div class="text-right">
                                <!-- Primary display is TWD -->
                                <div class="font-bold text-deep-green text-lg">NT$ {{ (exp.amount * exchangeRate).toFixed(0) }}</div>
                                <!-- Secondary display is THB -->
                                <div class="text-xs text-slate-500 mt-0.5">
                                    ฿ {{ exp.amount }} THB
                                </div>
                                <button @click="deleteExpense(exp.id)" class="text-xs text-red-300 hover:text-red-500 mt-1">刪除</button>
                            </div>
                        </div>
                    </div>
                    <div v-else class="text-center py-8 text-slate-400 text-sm">
                        還沒有支出紀錄喔!
                    </div>
                </div>
            </div>
 </div>
           <!-- 頁面 3: 工具 -->
            <div v-if="activeTab === 'tools'" class="p-4 pb-24 space-y-4">
                
                <!-- 翻譯工具方塊 (背景色已改為白色) -->
                <div class="bg-white p-6 rounded-2xl shadow-lg border border-slate-100">
                    <h3 class="font-bold text-lg mb-4 text-slate-700 flex items-center gap-2">
                        <i class="ph ph-translate text-blue-500"></i> 翻譯工具 (中文 ➡️ 泰文)
                    </h3>
                    <div class="grid grid-cols-2 gap-4">
                        <!-- 菜單/拍照翻譯 (背景改為亮黃色 #ffc83d) -->
                        <a href="https://translate.google.com/?sl=zh-TW&tl=th&op=images" target="_blank" class="bg-[#ffc83d] p-4 rounded-xl shadow-sm flex flex-col items-center justify-center text-center hover:bg-yellow-500 transition border border-yellow-200">
                            <i class="ph ph-camera text-3xl text-slate-800"></i>
                            <div class="text-sm font-bold text-slate-800 mt-2">菜單/拍照翻譯</div>
                        </a>
                        <!-- 口語/語音翻譯 (文字更新, 背景色修改為 #4db6ac, 圖示文字白色) -->
                        <a href="https://translate.google.com/?sl=zh-TW&tl=th&op=translate" target="_blank" class="bg-[#4db6ac] p-4 rounded-xl shadow-sm flex flex-col items-center justify-center text-center hover:bg-mint-cyan/90 transition border border-[#4db6ac]/70">
                            <i class="ph ph-microphone text-3xl text-white"></i>
                            <div class="text-sm font-bold text-white mt-2">口語/語音翻譯</div>
                        </a>
                    </div>
                </div>
                
                <!-- 常用泰語 (已修改標題: 從「救命泰語」改為「常用泰語」) -->
                <div class="bg-secondary bg-opacity-10 p-5 rounded-2xl border border-secondary/20">
                    <h3 class="font-bold text-secondary mb-3">🇹🇭 常用泰語</h3>
                    <div class="grid grid-cols-2 gap-3">
                        <!-- 基礎用語 -->
                        <div class="bg-white p-3 rounded-xl shadow-sm">
                            <div class="text-sm font-bold text-slate-800">你好</div>
                            <div class="text-xs text-slate-500">Sawatdee(撒挖低咖)</div>
                        </div>
                        <div class="bg-white p-3 rounded-xl shadow-sm">
                            <div class="text-sm font-bold text-slate-800">謝謝</div>
                            <div class="text-xs text-slate-500">Khop Khun(扣昆卡)</div>
                        </div>
                        <div class="bg-white p-3 rounded-xl shadow-sm">
                            <div class="text-sm font-bold text-slate-800">不好意思</div>
                            <div class="text-xs text-slate-500">khor Thot(扣透卡)</div>
                        </div>
                        <!-- 購物/問路 -->
                        <div class="bg-white p-3 rounded-xl shadow-sm">
                            <div class="text-sm font-bold text-slate-800">這個多少錢?</div>
                            <div class="text-xs text-slate-500">An Ni Tao Rai(安你套來)</div>
                        </div>
                        <div class="bg-white p-3 rounded-xl shadow-sm">
                            <div class="text-sm font-bold text-slate-800">可以便宜一點嗎</div>
                            <div class="text-xs text-slate-500">lot dai mai(落袋買~)</div>
                        </div>
                        <div class="bg-white p-3 rounded-xl shadow-sm">
                            <div class="text-sm font-bold text-slate-800">廁所在哪?</div>
                            <div class="text-xs text-slate-500">Hong Nam Yu Nai(轟難予奈)</div>
                        </div>
                        <!-- 點餐需求 -->
                        <div class="bg-white p-3 rounded-xl shadow-sm">
                            <div class="text-sm font-bold text-slate-800">不要香菜!!</div>
                            <div class="text-xs text-slate-500">Mai Phak Chii(賣 怕氣)</div> 
                        </div>
                        <div class="bg-white p-3 rounded-xl shadow-sm">
                            <div class="text-sm font-bold text-slate-800">不要辣</div>
                            <div class="text-xs text-slate-500">mai ped(麥 佩特)</div>
                        </div>
                        <!-- 按摩專用 (新增加) -->
                        <div class="bg-white p-3 rounded-xl shadow-sm">
                            <div class="text-sm font-bold text-slate-800">輕一點</div>
                            <div class="text-xs text-slate-500">Bao Bao (包包)</div>
                        </div>
                        <div class="bg-white p-3 rounded-xl shadow-sm">
                            <div class="text-sm font-bold text-slate-800">重一點</div>
                            <div class="text-xs text-slate-500">Nak Nak (娜娜)</div>
                        </div>
                         <div class="bg-white p-3 rounded-xl shadow-sm">
                            <div class="text-sm font-bold text-slate-800">痛</div>
                            <div class="text-xs text-slate-500">Jep (介)</div>
                        </div>    
                    </div>  
                </div>    
            </div>

        </main>

        <!-- 底部導航欄 (Tab Bar) - 顏色匹配圖片底部的灰色系 -->
        <nav class="fixed bottom-0 left-0 w-full bg-white border-t border-slate-200 shadow-[0_-5px_15px_rgba(0,0,0,0.05)] z-50" 
     style="padding-bottom: env(safe-area-inset-bottom);">
    <div class="flex justify-around items-center h-16">
        <!-- 行程 -->
        <button @click="activeTab = 'itinerary'" 
                class="flex flex-col items-center justify-center w-full h-full transition-colors"
                :class="activeTab === 'itinerary' ? 'text-green-500' : 'text-slate-400 hover:text-slate-600'">
            <i class="ph text-2xl mb-0.5" :class="activeTab === 'itinerary' ? 'ph-calendar-blank-fill' : 'ph-calendar-blank'"></i>
            <span class="text-[10px] font-bold">行程</span>
        </button>
        <!-- 記帳 -->
        <button @click="activeTab = 'expenses'" 
                class="flex flex-col items-center justify-center w-full h-full transition-colors"
                :class="activeTab === 'expenses' ? 'text-primary' : 'text-slate-400 hover:text-slate-600'">
            <i class="ph text-2xl mb-0.5" :class="activeTab === 'expenses' ? 'ph-wallet-fill' : 'ph-wallet'"></i>
            <span class="text-[10px] font-bold">記帳</span>
        </button>
        <!-- 工具 -->
        <button @click="activeTab = 'tools'" 
                class="flex flex-col items-center justify-center w-full h-full transition-colors"
                :class="activeTab === 'tools' ? 'text-secondary' : 'text-slate-400 hover:text-slate-600'">
            <i class="ph text-2xl mb-0.5" :class="activeTab === 'tools' ? 'ph-book-open' : 'ph-book'"></i>
            <span class="text-[10px] font-bold">工具</span>
        </button>
    </div>
</nav>

        <!-- Modals (彈出視窗) - 保持原本的樣式，以確保功能清晰 -->

        <!-- 新增/編輯行程 Modal -->
        <div v-if="showItineraryModal" class="absolute inset-0 z-50 bg-slate-900/50 backdrop-blur-sm flex items-end sm:items-center justify-center">
            <div class="bg-white w-full max-w-md rounded-t-2xl sm:rounded-2xl p-6 shadow-2xl animate-slide-up">
                <h3 class="font-bold text-lg mb-4 text-slate-800">{{ editingItem ? '編輯行程' : '新增行程' }}</h3>
                <div class="space-y-4">
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">時間</label>
                        <input type="time" v-model="form.time" class="w-full bg-slate-50 border border-slate-200 rounded-lg p-3 outline-none focus:border-primary">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">標題 (必填)</label>
                        <input type="text" v-model="form.title" placeholder="例如: 大皇宮參觀" class="w-full bg-slate-50 border border-slate-200 rounded-lg p-3 outline-none focus:border-primary">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">地點 (用於導航)</label>
                        <div class="relative">
                            <input type="text" v-model="form.location" placeholder="例如: The Grand Palace" class="w-full bg-slate-50 border border-slate-200 rounded-lg p-3 pl-10 outline-none focus:border-primary">
                            <i class="ph ph-map-pin absolute left-3 top-3.5 text-slate-400"></i>
                        </div>
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">備註</label>
                        <textarea v-model="form.note" rows="2" class="w-full bg-slate-50 border border-slate-200 rounded-lg p-3 outline-none focus:border-primary"></textarea>
                    </div>
                    <div class="flex gap-3 mt-6">
                        <button @click="closeModal" class="flex-1 py-3 rounded-xl bg-slate-100 text-slate-600 font-bold">取消</button>
                        <button @click="saveItineraryItem" class="flex-1 py-3 rounded-xl bg-primary text-slate-900 font-bold shadow-lg shadow-primary/30">儲存</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- 成員設定 Modal -->
        <div v-if="showMemberModal" class="absolute inset-0 z-50 bg-slate-900/50 backdrop-blur-sm flex items-center justify-center p-4">
            <div class="bg-white w-full max-w-md rounded-2xl p-6 shadow-2xl">
                <h3 class="font-bold text-lg mb-2">管理旅伴</h3>
                <p class="text-xs text-slate-400 mb-4">輸入名字後按 Enter 新增</p>
                
                <div class="flex gap-2 mb-4">
                    <input 
                        type="text" 
                        v-model="newMemberName" 
                        @keyup.enter="addMember"
                        placeholder="輸入名字..." 
                        class="flex-1 bg-slate-50 border border-slate-200 rounded-lg p-2 px-3 outline-none focus:border-secondary"
                    >
                    <button @click="addMember" class="bg-secondary text-white px-4 rounded-lg font-bold">新增</button>
                </div>

                <div class="flex flex-wrap gap-2 mb-4">
                    <div v-for="(m, idx) in members" :key="idx" class="bg-slate-100 pl-3 pr-2 py-1 rounded-full text-sm font-medium flex items-center gap-2">
                        {{ m }}
                        <button @click="removeMember(m)" class="w-5 h-5 rounded-full bg-slate-300 text-white flex items-center justify-center hover:bg-red-400"><i class="ph ph-x text-xs"></i></button>
                    </div>
                </div>
                <button @click="showMemberModal = false" class="w-full py-3 bg-slate-100 text-slate-700 font-bold rounded-xl">完成</button>
            </div>
        </div>
        
        <!-- 設定 Modal -->
        <!-- CHANGED: 將 bg-white 改為 bg-bg_app (#fff8dc) -->
        <div v-if="showSettings" class="absolute inset-0 z-[60] bg-bg_app flex flex-col animate-slide-up">
            <div class="flex justify-between items-center p-5 border-b border-slate-100 bg-white">
                <h2 class="font-bold text-xl">設定</h2>
                <button @click="showSettings = false" class="bg-slate-100 p-2 rounded-full"><i class="ph ph-x"></i></button>
            </div>
            <div class="p-5 space-y-6 flex-1 overflow-y-auto">
                
                <!-- 匯率換算功能 -->
                <div class="bg-white p-4 rounded-xl shadow-lg border border-slate-100">
                    <h3 class="font-bold text-lg mb-4 text-slate-700 flex items-center gap-2">
                        <!-- 標題簡化為「匯率設定」 -->
                        <i class="ph ph-currency-circle-dollar text-green-600"></i> 匯率設定
                    </h3>
                    
                    <!-- 匯率設定 輸入框 -->
                    <div class="mb-2"> 
                        <label class="font-bold text-sm text-slate-500 block mb-1">匯率 (1 THB = ? TWD)</label>
                        <div class="flex items-center">
                            <input 
                                type="number" 
                                step="0.001" 
                                v-model.number="exchangeRate" 
                                class="w-24 bg-slate-50 p-2 rounded-l-lg border border-r-0 border-slate-300 font-mono text-sm"
                            >
                            <span class="bg-slate-50 p-2 rounded-r-lg border border-slate-300 text-slate-600 text-sm font-medium">TWD</span>
                        </div>
                        <p class="text-xs text-slate-400 mt-1">
                           例如: 輸入 0.90 (代表 1 泰銖 可換 0.90 台幣)
                        </p>
                    </div>

                    <!-- 移除即時換算區塊 -->
                    
                </div>
                
                <div class="pt-5 border-t border-slate-100">
                    <button @click="resetAllData" class="w-full py-4 text-red-500 bg-red-50 rounded-xl font-bold">清除所有資料 (重置)</button>
                    <p class="text-xs text-center text-slate-400 mt-2">這將會刪除所有行程與記帳紀錄</p>
                </div>
            </div>
        </div>

    </div>

    <script>
        const { createApp, ref, computed, watch, onMounted, reactive } = Vue;

        createApp({
            setup() {
                // --- 狀態管理 ---
                const activeTab = ref('expenses'); // 預設停在記帳頁籤，方便查看異動
                const showItineraryModal = ref(false);
                const showMemberModal = ref(false);
                // 記帳現在是 inline form，故移除 showExpenseModal 狀態
                // const showExpenseModal = ref(false); 
                const showSettings = ref(false);
                
                // 行程相關
                const currentDayIndex = ref(6); // 預設跳到 Day 7
                // 預設 7 天行程 (索引 0 到 6)
                const itineraryDays = ref([
                    [
                        // Day 1: 2025/03/08
                        { id: 1, time: '06:00', title: 'TPE桃園機場 集合', location: 'TPE桃園機場', note: '第一航廈 7:00起飛', category: 'travel', weather: { icon: 'ph-cloud-sun', temp: '25°C', color: 'text-sky-500' } },
                        { id: 2, time: '09:50', title: '抵達 BKK 素萬那普機場', location: 'BKK 素萬那普機場', note: '', category: 'travel', weather: { icon: 'ph-cloud-sun-fill', temp: '28°C', color: 'text-sky-600' } },
                        { id: 3, time: '11:30', title: '入住酒店', location: 'Pattaya Discovery Beach Hotel', note: '', category: 'accommodation', weather: { icon: 'ph-cloud-rain-fill', temp: '29°C', color: 'text-gray-500' } },
                        { id: 4, time: '12:00', title: '午餐', location: 'Spicy Twist Pattaya', note: '', category: 'food', weather: { icon: 'ph-sun-dim-fill', temp: '32°C', color: 'text-orange-500' } },
                        { id: 5, time: '14:00', title: '參觀真理寺', location: '真理寺', note: '泰銖500/人\n導覽時間：14:00 (真人)、14:35、14:55', category: 'attraction', weather: { icon: 'ph-sun-dim-fill', temp: '33°C', color: 'text-orange-500' } },
                        { id: 6, time: '17:00', title: '咖啡/休息', location: 'The Collective Pattaya', note: '', category: 'food', weather: { icon: 'ph-cloud-sun', temp: '30°C', color: 'text-sky-500' } },
                        { id: 7, time: '20:00', title: '人妖秀', location: 'Alcazar Cabaret Show', note: '', category: 'attraction', weather: { icon: 'ph-moon-stars-fill', temp: '28°C', color: 'text-slate-500' } },
                        { id: 8, time: '21:00', title: '逛夜市', location: '飛機夜市', note: '', category: 'attraction', weather: { icon: 'ph-moon-stars-fill', temp: '27°C', color: 'text-slate-500' } },
                        { id: 9, time: '23:00', title: '按摩/放鬆', location: 'RINN Spa/Massage', note: '', category: 'spa', weather: { icon: 'ph-moon-stars-fill', temp: '26°C', color: 'text-slate-500' } },
                    ], 
                    [
                        // Day 2: 2025/03/09
                        { id: 101, time: '08:00', title: 'Mae Pong Sri (必比登)', location: 'Mae Pong Sri', note: '必比登推薦早餐', category: 'food', weather: { icon: 'ph-sun-dim-fill', temp: '30°C', color: 'text-orange-500' } },
                        { id: 102, time: '09:30', title: '七珍佛山', location: 'Khao Chi Chan', note: '泰國最大的雕刻佛像', category: 'attraction', weather: { icon: 'ph-sun-dim-fill', temp: '32°C', color: 'text-orange-500' } },
                        { id: 103, time: '11:00', title: '東芭樂園恐龍谷', location: 'Nong Nooch Tropical Garden', note: '門票+導覽車 台幣500/人', category: 'attraction', weather: { icon: 'ph-sun-dim-fill', temp: '33°C', color: 'text-orange-500' } },
                        { id: 104, time: '16:00', title: 'Glass House Boho', location: 'The Glass House Pattaya', note: '海邊網美餐廳，記得預約', category: 'food', weather: { icon: 'ph-cloud-sun', temp: '30°C', color: 'text-sky-500' } },
                        { id: 105, time: '19:00', title: 'The SunSet health massage', location: 'The SunSet health massage', note: '泰式按摩放鬆', category: 'spa', weather: { icon: 'ph-moon-stars-fill', temp: '28°C', color: 'text-slate-500' } },
                        { id: 106, time: '20:00', title: '芭達雅步行街', location: 'Pattaya Walking Street', note: '夜市探險/自由活動', category: 'attraction', weather: { icon: 'ph-moon-stars-fill', temp: '27°C', color: 'text-slate-500' } },
                    ],
                    [
                        // Day 3: 2025/03/10
                        { id: 201, time: '08:00', title: '飯店早餐與退房', note: 'Check-out並準備前往曼谷', category: 'accommodation', weather: { icon: 'ph-sun-dim-fill', temp: '30°C', color: 'text-orange-500' } },
                        { id: 202, time: '10:00', title: '抵達曼谷並暫放行李', location: 'K Maison Boutique Hotel', note: '從芭達雅移動到曼谷/暫放行李', category: 'travel', weather: { icon: 'ph-cloud-sun-fill', temp: '31°C', color: 'text-sky-600' } },
                        { id: 203, time: '12:30', title: 'Big C 零食伴手禮採買', location: 'Big C Supercenter, Rajdamri', note: '購買泰國泡麵、海苔等零食', category: 'shopping', weather: { icon: 'ph-sun-dim-fill', temp: '33°C', color: 'text-orange-500' } },
                        { id: 204, time: '14:30', title: '飯店辦理入住', note: 'Check in 並整理戰利品', category: 'accommodation', weather: { icon: 'ph-cloud-sun', temp: '30°C', color: 'text-sky-500' } },
                        { id: 205, time: '17:00', title: '彩虹雲霄酒店自助晚餐', location: 'Baiyoke Sky Hotel', note: '17:00進場 (78樓)\n景觀台: 84樓\n費用: 約 NTD 900', category: 'food', weather: { icon: 'ph-cloud-sun-fill', temp: '28°C', color: 'text-sky-600' } },
                        { id: 206, time: '19:00', title: 'Terminal 21 主題購物', location: 'Terminal 21 Asok', note: '機場主題商場，逛街/自由活動', category: 'attraction', weather: { icon: 'ph-moon-stars-fill', temp: '27°C', color: 'text-slate-500' } },
                        { id: 207, time: '22:00', title: '全身按摩放鬆', location: 'Massage@Le', note: '預約晚間時段', category: 'spa', weather: { icon: 'ph-moon-stars-fill', temp: '26°C', color: 'text-slate-500' } },
                    ],
                    [
                        // Day 4: 2025/03/11
                        { id: 301, time: '08:00', title: '飯店早餐/出發', note: '退房或出發', category: 'food', weather: { icon: 'ph-sun-dim-fill', temp: '30°C', color: 'text-orange-500' } },
                        { id: 302, time: '09:30', title: '必比登炭烤豬肉沙爹', location: 'Pork Satay Nai Song', note: '早餐/點心', category: 'food', weather: { icon: 'ph-sun-dim-fill', temp: '32°C', color: 'text-orange-500' } },
                        { id: 303, time: '11:00', title: '鄭王廟 (黎明寺)', location: 'Wat Arun (鄭王廟)', note: '參觀美麗的寺廟', category: 'attraction', weather: { icon: 'ph-sun-dim-fill', temp: '33°C', color: 'text-orange-500' } },
                        { id: 304, time: '13:00', title: 'ICONSIAM 購物中心', location: 'ICONSIAM', note: '午餐與逛街', category: 'shopping', weather: { icon: 'ph-cloud-sun', temp: '30°C', color: 'text-sky-500' } },
                        { id: 305, time: '19:00', title: '泰式按摩 (河邊店)', location: 'Khagee Thai Massage (riverside branch)', note: '', category: 'spa', weather: { icon: 'ph-moon-stars-fill', temp: '28°C', color: 'text-slate-500' } },
                        { id: 306, time: '20:00', title: 'Asiatique河濱瑪頭夜市', location: 'Asiatique The Riverfront', note: '逛夜市', category: 'attraction', weather: { icon: 'ph-moon-stars-fill', temp: '27°C', color: 'text-slate-500' } },
                    ],
                    [
                        // Day 5: 2025/03/12
                        { id: 401, time: '09:30', title: '郭炎松 泰式米粉湯', location: '郭炎松 (Kuay Tiao Reua)', note: '米粉湯午餐', category: 'food', weather: { icon: 'ph-sun-dim-fill', temp: '30°C', color: 'text-orange-500' } },
                        { id: 402, time: '11:00', title: 'The Erawan Museum', location: 'The Erawan Museum', note: '三頭象神博物館', category: 'attraction', weather: { icon: 'ph-sun-dim-fill', temp: '32°C', color: 'text-orange-500' } },
                        { id: 403, time: '13:00', title: 'FO SHO BRO', location: 'FO SHO BRO', note: '咖啡廳或午餐', category: 'food', weather: { icon: 'ph-sun-dim-fill', temp: '33°C', color: 'text-orange-500' } },
                        { id: 404, time: '15:00', title: 'Suan Luang Rama IX', location: 'Suan Luang Rama IX', note: '拉瑪九世國王公園 (曼谷最大公園)', category: 'attraction', weather: { icon: 'ph-cloud-sun', temp: '30°C', color: 'text-sky-500' } },
                        { id: 405, time: '17:00', title: '西康廣場逛街+按摩', location: 'Seacon Square (西康廣場)', note: '購物及休憩', category: 'shopping', weather: { icon: 'ph-cloud-sun-fill', temp: '28°C', color: 'text-sky-600' } },
                        { id: 406, time: '19:00', title: '希娜克琳火車夜市', location: 'Srinakarin Train Night Market', note: '夜市晚餐與逛街 (營業至 23:00)', category: 'attraction', weather: { icon: 'ph-moon-stars-fill', temp: '27°C', color: 'text-slate-500' } },
                    ],
                    [
                        // Day 6: 2025/03/13
                        { id: 501, time: '10:30', title: 'Lay Lao 泰北菜', location: 'Lay Lao (BTS Ari)', note: '特色泰北料理午餐', category: 'food', weather: { icon: 'ph-sun-dim-fill', temp: '30°C', color: 'text-orange-500' } },
                        { id: 502, time: '14:00', title: '美功鐵道市場', location: 'Maeklong Railway Market', note: '火車進站/出站時間需確認', category: 'attraction', weather: { icon: 'ph-sun-dim-fill', temp: '32°C', color: 'text-orange-500' } },
                        { id: 503, time: '15:30', title: '朱拉曼尼寺廟', location: 'Wat Chulamanee (朱拉曼尼寺廟)', note: '', category: 'attraction', weather: { icon: 'ph-sun-dim-fill', temp: '33°C', color: 'text-orange-500' } },
                        { id: 504, time: '16:30', title: 'The Buffalo Café', location: 'The Buffalo Café', note: '河邊咖啡廳/休息', category: 'food', weather: { icon: 'ph-cloud-sun', temp: '30°C', color: 'text-sky-500' } },
                        { id: 505, time: '18:00', title: '安帕瓦水上市場', location: 'Amphawa Floating Market', note: '體驗水上夜市 (週末限定)', category: 'attraction', weather: { icon: 'ph-cloud-sun-fill', temp: '28°C', color: 'text-sky-600' } },
                        { id: 506, time: '22:00', title: '唐人街夜市', location: 'Yaowarat Road (唐人街夜市)', note: '宵夜/美食尋寶', category: 'attraction', weather: { icon: 'ph-moon-stars-fill', temp: '27°C', color: 'text-slate-500' } },
                    ],
                    [
                        // Day 7: 2025/03/14 (新增)
                        { id: 601, time: '10:00', title: '飯店退房', note: 'Check out', category: 'accommodation', weather: { icon: 'ph-cloud-sun', temp: '30°C', color: 'text-sky-500' } },
                        { id: 602, time: '13:00', title: '抵達 BKK 素萬那普機場', note: '14:05 起飛', category: 'travel', weather: { icon: 'ph-cloud-sun-fill', temp: '32°C', color: 'text-orange-500' } },
                    ],
                ]);
                const editingItem = ref(null);
                
                // 記帳相關
               const members = ref(['小銘','真閒','共同']); // 預設成員
                const newMemberName = ref('');
                const expenses = ref([]);
                
                // 工具相關
                // CHANGED: 匯率定義改為 1 THB = X TWD (預設 0.90)
                const exchangeRate = ref(0.90); 

                // 表單
                const form = reactive({ time: '', title: '', location: '', note: '' });
                const expenseForm = reactive({ 
                    amount: '', 
                    title: '', 
                    category: 'food', 
                    payer: '', 
                    forWho: [],
                    dayIndex: currentDayIndex.value, // 新增 dayIndex 追蹤是哪一天的支出
                });

                // --- Computed ---
                
                // 當前選擇天數的行程
                const currentItinerary = computed(() => {
                    return itineraryDays.value[currentDayIndex.value] || [];
                });

                // 排序行程 (按時間)
                const sortedCurrentItinerary = computed(() => {
                    return [...currentItinerary.value].sort((a, b) => {
                        // 確保時間格式正確 (HH:MM)
                        const timeA = a.time.padEnd(5, '0');
                        const timeB = b.time.padEnd(5, '0');
                        return timeA.localeCompare(b.time);
                    });
                });

                // 排序支出 (新的在前)
                const sortedExpenses = computed(() => {
                    return [...expenses.value].reverse();
                });

                // New: Total Expense in TWD (總支出)
                const totalTWDExpense = computed(() => {
                    const rate = Number(exchangeRate.value);
                    return expenses.value.reduce((sum, item) => sum + (Number(item.amount) * rate), 0).toFixed(0);
                });

                // New: Member Contribution (Net Balance in TWD - 墊付統計)
                const memberTWDContribution = computed(() => {
                    let balances = {};
                    const rate = Number(exchangeRate.value);
                    members.value.forEach(m => balances[m] = 0);

                    expenses.value.forEach(exp => {
                        const amountTWD = Number(exp.amount) * rate;
                        const payer = exp.payer;
                        const splitAmong = exp.forWho.length > 0 ? exp.forWho : members.value;
                        
                        // 處理分給 0 人的極端情況
                        if (splitAmong.length === 0) return; 
                        
                        const splitAmountTWD = amountTWD / splitAmong.length;

                        // Payer's net balance increases by the total amount paid (TWD)
                        if(balances[payer] !== undefined) balances[payer] += amountTWD;

                        // Each consumer's net balance decreases by their share of the debt (TWD)
                        splitAmong.forEach(person => {
                            if(balances[person] !== undefined) {
                                balances[person] -= splitAmountTWD;
                            }
                        });
                    });
                    
                    // Round balances to prevent tiny floating point errors from displaying
                    for (const person in balances) {
                        // 四捨五入到整數，方便顯示 NT$ 0
                        balances[person] = Math.round(balances[person]); 
                    }

                    return balances;
                });

                // 計算當前記帳金額 (THB) 換算成台幣 (TWD) 的參考值
                const twdEquivalent = computed(() => {
                    const thbAmount = Number(expenseForm.amount);
                    const rate = Number(exchangeRate.value);
                    
                    if (isNaN(thbAmount) || thbAmount <= 0 || isNaN(rate) || rate <= 0) {
                        return 0; // 統一返回數字 0
                    }
                    // UPDATED: TWD = THB * (1 THB = X TWD)
                    return Math.round(thbAmount * rate); // 取整數，作為快速參考
                });
                
                // 移除 debts computed property


                // --- Methods ---

                const loadData = () => {
                    // 從 localStorage 載入資料
                    const savedItinerary = localStorage.getItem('thai_itinerary');
                    if (savedItinerary) {
                         // 避免覆蓋初始行程，除非 localStorage 裡有資料
                        const loadedData = JSON.parse(savedItinerary);
                        // 僅在 loadedData 有效且比預設多時覆蓋
                        if (loadedData.length > 0) {
                            itineraryDays.value = loadedData;
                        }
                    }

                    const savedMembers = localStorage.getItem('thai_members');
                    if (savedMembers) members.value = JSON.parse(savedMembers);

                    const savedExpenses = localStorage.getItem('thai_expenses');
                    if (savedExpenses) expenses.value = JSON.parse(savedExpenses);
                    
                    const savedRate = localStorage.getItem('thai_rate');
                    if (savedRate) exchangeRate.value = Number(savedRate);
                    
                    // 初始化 expenseForm.payer 和 expenseForm.forWho
                    expenseForm.payer = members.value[0] || '';
                    expenseForm.forWho = [...members.value];
                    expenseForm.dayIndex = currentDayIndex.value;
                };

                const saveData = () => {
                    // 將資料儲存到 localStorage
                    localStorage.setItem('thai_itinerary', JSON.stringify(itineraryDays.value));
                    localStorage.setItem('thai_members', JSON.stringify(members.value));
                    localStorage.setItem('thai_expenses', JSON.stringify(expenses.value));
                    localStorage.setItem('thai_rate', exchangeRate.value);
                };
                
                // Watchers for auto-save
                watch([itineraryDays, members, expenses, exchangeRate], saveData, { deep: true });
                
                // Itinerary Actions
                const addDay = () => itineraryDays.value.push([]);
                
                const openModal = () => {
                    editingItem.value = null;
                    form.time = '09:00';
                    form.title = '';
                    form.location = '';
                    form.note = '';
                    showItineraryModal.value = true;
                };

                const editItem = (item) => {
                    editingItem.value = item;
                    // 使用 Object.assign 複製屬性
                    Object.assign(form, { 
                        time: item.time, 
                        title: item.title, 
                        location: item.location, 
                        note: item.note 
                    });
                    showItineraryModal.value = true;
                };

                const closeModal = () => showItineraryModal.value = false;

                const saveItineraryItem = () => {
                    if (!form.title) {
                        console.error('請輸入標題');
                        // 使用自定義的提示 (代替 alert)
                        return confirm('請輸入標題才能儲存行程！');
                    }
                    
                    if (editingItem.value) {
                        // Edit
                        Object.assign(editingItem.value, form);
                        // 編輯時不改變天氣數據
                    } else {
                        // Add
                        itineraryDays.value[currentDayIndex.value].push({
                            id: Date.now(),
                            ...form,
                            // 新增項目時給一個預設/模擬天氣
                            weather: { icon: 'ph-sun-dim-fill', temp: '32°C', color: 'text-orange-500' } 
                        });
                    }
                    closeModal();
                };

                const deleteItem = (id) => {
                    // 使用自定義的確認視窗邏輯，而非 window.confirm
                    if (confirm('確定刪除此行程？')) {
                        const day = itineraryDays.value[currentDayIndex.value];
                        const idx = day.findIndex(i => i.id === id);
                        if (idx !== -1) day.splice(idx, 1);
                    }
                };

                const formatTime = (timeStr) => {
                    // 確保時間格式是 HH:MM
                    return timeStr.substring(0, 5);
                };

                // Expense Actions
                const addMember = () => {
                    if (newMemberName.value && !members.value.includes(newMemberName.value)) {
                        members.value.push(newMemberName.value);
                        newMemberName.value = '';
                        // 更新 expenseForm 的 payer 和 forWho 預設值
                        expenseForm.payer = members.value[0] || '';
                        expenseForm.forWho = [...members.value];
                    }
                };
                
                const removeMember = (name) => {
                    // 使用自定義的確認視窗邏輯
                    if(confirm('刪除成員可能會影響記帳計算，確定嗎？')) {
                        members.value = members.value.filter(m => m !== name);
                    }
                };

                // 移除 openExpenseModal

                const saveExpense = () => {
                    // 確保 amount 是數字且大於 0
                    if (!expenseForm.amount || isNaN(Number(expenseForm.amount)) || Number(expenseForm.amount) <= 0) {
                        return confirm('請輸入有效金額！');
                    }
                    if (!expenseForm.title || !expenseForm.payer || expenseForm.forWho.length === 0) {
                        return confirm('請填寫項目名稱、付款人，並選擇分帳對象！');
                    }
                    
                    expenses.value.push({
                        id: Date.now(),
                        ...expenseForm,
                        amount: Number(expenseForm.amount).toFixed(0), // 儲存為四捨五入的整數 THB
                        forWho: [...expenseForm.forWho] // copy array
                    });
                    
                    // Reset form after saving
                    expenseForm.amount = '';
                    expenseForm.title = '';
                    expenseForm.category = 'food';
                    expenseForm.payer = members.value[0] || '';
                    expenseForm.forWho = [...members.value];
                    expenseForm.dayIndex = currentDayIndex.value;
                };

                const deleteExpense = (id) => {
                    // 使用自定義的確認視窗邏輯
                    if(confirm('確定刪除這筆支出？')) {
                        expenses.value = expenses.value.filter(e => e.id === id ? null : e);
                    }
                };
                
                const getCategoryIcon = (cat) => {
                    const map = {
                        food: 'ph-hamburger',
                        transport: 'ph-taxi',
                        stay: 'ph-bed',
                        fun: 'ph-ticket',
                        other: 'ph-package'
                    };
                    return map[cat] || 'ph-question';
                };

                const resetAllData = () => {
                    // 使用自定義的確認視窗邏輯
                    if(confirm('警告：這將會刪除所有資料且無法復原！')) {
                        localStorage.clear();
                        // 重新載入預設資料
                        itineraryDays.value = [[], [], [], [], []];
                        members.value = ['小銘', '真閒','共同'];
                        expenses.value = [];
                        exchangeRate.value = 0.90;
                        showSettings.value = false;
                        
                        // 重設表單預設值
                        currentDayIndex.value = 0;
                        expenseForm.payer = members.value[0] || '';
                        expenseForm.forWho = [...members.value];
                        expenseForm.dayIndex = currentDayIndex.value;
                    }
                }

                // Initialize
                onMounted(() => {
                    loadData();
                });

                return {
                    activeTab,
                    // Itinerary
                    itineraryDays,
                    currentDayIndex,
                    currentItinerary,
                    sortedCurrentItinerary,
                    showItineraryModal,
                    form,
                    editingItem,
                    addDay,
                    openModal,
                    editItem,
                    closeModal,
                    saveItineraryItem,
                    deleteItem,
                    formatTime,
                    // Expenses
                    members,
                    newMemberName,
                    showMemberModal,
                    addMember,
                    removeMember,
                    expenses,
                    sortedExpenses,
                    // 替換 totalExpense
                    totalTWDExpense,
                    // 替換 debts
                    memberTWDContribution,
                    // showExpenseModal 已移除
                    expenseForm,
                    // openExpenseModal 已移除
                    saveExpense,
                    deleteExpense,
                    getCategoryIcon,
                    twdEquivalent, 
                    // Settings/Tools
                    showSettings,
                    exchangeRate, 
                    resetAllData
                };
            }
        }).mount('#app');
        // 替換原生 confirm/alert，確保在 iFrame 中可用
        window.confirm = (message) => {
            console.warn(`Custom confirmation dialog: ${message}`);
            // 在實際應用中，這裡應該彈出一個自定義的 Modal
            return window.prompt(message, '確定') === '確定'; 
        };
    </script>
</body>
</html>
