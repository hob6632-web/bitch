<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>我的小視窗工具</title>
    <!-- 使用 Tailwind CSS 進行快速切版 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* 主頁面的基礎樣式 */
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            min-height: 100vh;
        }
    </style>
</head>
<body class="flex flex-col items-center justify-center relative">

    <!-- 右上角即時時鐘 -->
    <div class="absolute top-4 right-4 bg-white/80 backdrop-blur-sm px-4 py-2 rounded-lg shadow text-gray-700 font-mono text-xl border border-gray-200">
        🕒 <span id="clock">00:00:00</span>
    </div>

    <!-- 主卡片區域 -->
    <div class="bg-white p-8 rounded-2xl shadow-xl w-full max-w-md text-center transform transition-all hover:scale-[1.01]">
        <h1 class="text-3xl font-bold text-gray-800 mb-2">My Popup Tool</h1>
        <p class="text-gray-500 mb-8 text-sm">Chapter 13 練習作業</p>

        <!-- 歡迎訊息區域 -->
        <div id="welcomeArea" class="mb-6 min-h-[3rem] flex items-center justify-center">
            <p class="text-xl text-gray-700 font-medium animate-pulse">準備好了嗎？</p>
        </div>

        <!-- 按鈕 -->
        <button id="startBtn" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 px-8 rounded-full shadow-lg transform transition active:scale-95 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 mb-6 w-full">
            開啟小視窗
        </button>

        <!-- 計數器 -->
        <div class="pt-4 border-t border-gray-100">
            <p class="text-gray-600">
                你已經開過 <span id="countDisplay" class="font-bold text-indigo-600 text-2xl mx-1">0</span> 次小視窗
            </p>
        </div>
    </div>

    <script>
        // --- 1. 右上角時鐘功能 (setInterval) ---
        function updateClock() {
            const now = new Date();
            const timeString = now.toLocaleTimeString('zh-TW', { hour12: false });
            document.getElementById('clock').textContent = timeString;
        }
        setInterval(updateClock, 1000); // 每秒執行一次
        updateClock(); // 初始化立即執行一次

        
        // --- 2. 變數與 DOM 元素選取 ---
        const startBtn = document.getElementById('startBtn');
        const welcomeArea = document.getElementById('welcomeArea');
        const countDisplay = document.getElementById('countDisplay');
        
        // 從 localStorage 讀取計數，如果沒有則預設為 0
        let openCount = parseInt(localStorage.getItem('windowOpenCount')) || 0;
        countDisplay.textContent = openCount;


        // --- 3. 按鈕點擊事件處理 ---
        startBtn.addEventListener('click', function() {
            // A. 從 localStorage 取得上次的名字作為預設值
            const lastUsedName = localStorage.getItem('username') || '';

            // B. 使用 prompt() 擷取使用者輸入
            const name = window.prompt("請輸入你的名字：", lastUsedName);

            // 如果使用者按取消或沒輸入，則不執行後續動作
            if (name === null || name.trim() === "") return;

            // C. 儲存名字與計數到 localStorage
            localStorage.setItem('username', name);
            openCount++;
            localStorage.setItem('windowOpenCount', openCount);
            
            // 更新畫面計數
            countDisplay.textContent = openCount;

            // D. 主頁顯示歡迎字樣
            welcomeArea.innerHTML = `
                <p class="text-2xl text-indigo-800 font-bold transition-all duration-500">
                    歡迎你回來，${name}！
                </p>
            `;

            // E. 開啟新視窗 (使用 window.open)
            openSmallWindow(name);
        });


        // --- 4. 建立與寫入小視窗的邏輯 (封裝成函式) ---
        function openSmallWindow(name) {
            // 計算視窗位置讓它稍微居中
            const left = (window.screen.width - 400) / 2;
            const top = (window.screen.height - 300) / 2;
            
            // 開啟空白視窗
            // 注意：現代瀏覽器可能會阻擋 Popup，請確保允許彈跳視窗
            const newWindow = window.open(
                '', 
                '_blank', 
                `width=400,height=300,left=${left},top=${top},menubar=no,status=no,toolbar=no`
            );

            if (!newWindow) {
                alert("瀏覽器封鎖了彈跳視窗，請允許後再試一次！");
                return;
            }

            // 判斷時間決定背景色
            const hours = new Date().getHours();
            let bgColorClass = "";
            let timeGreeting = "";
            
            if (hours >= 6 && hours < 12) {
                bgColorClass = "bg-gradient-to-br from-yellow-100 to-orange-200"; // 早上
                timeGreeting = "早安";
            } else if (hours >= 12 && hours < 18) {
                bgColorClass = "bg-gradient-to-br from-blue-200 to-cyan-200"; // 下午
                timeGreeting = "午安";
            } else {
                bgColorClass = "bg-gradient-to-br from-indigo-900 to-purple-800 text-white"; // 晚上
                timeGreeting = "晚安";
            }

            // 建構小視窗的 HTML 內容
            // 這邊是為了模擬作業中「small.html」的內容
            const popupContent = `
                <!DOCTYPE html>
                <html lang="zh-TW">
                <head>
                    <meta charset="UTF-8">
                    <title>Hello ${name}</title>
                    <script src="https://cdn.tailwindcss.com"><\/script>
                    <style>
                        /* 進場動畫 */
                        @keyframes fadeUp {
                            from { opacity: 0; transform: translateY(20px); }
                            to { opacity: 1; transform: translateY(0); }
                        }
                        .animate-enter {
                            animation: fadeUp 0.8s ease-out forwards;
                        }
                    </style>
                </head>
                <body class="${bgColorClass} h-screen flex flex-col items-center justify-center overflow-hidden p-4 select-none">
                    
                    <div class="text-center animate-enter">
                        <div class="text-5xl mb-4">👋</div>
                        <h2 class="text-2xl font-bold mb-2">Hello, ${name}！</h2>
                        <p class="text-lg opacity-80 mb-6">${timeGreeting}，祝你有個美好的一天</p>
                        
                        <div class="bg-white/20 backdrop-blur-md rounded-lg px-6 py-3 inline-block">
                            <span class="text-sm uppercase tracking-wider">Auto Close In</span>
                            <div class="text-4xl font-mono font-bold mt-1" id="timer">3</div>
                        </div>
                    </div>

                    <script>
                        // 小視窗內的 JavaScript
                        let timeLeft = 3;
                        const timerDisplay = document.getElementById('timer');

                        const intervalId = setInterval(() => {
                            timeLeft--;
                            timerDisplay.textContent = timeLeft;

                            if (timeLeft <= 0) {
                                clearInterval(intervalId);
                                window.close(); // 時間到，自動關閉
                            }
                        }, 1000);
                    <\/script>
                </body>
                </html>
            `;

            // 使用 document.write 將內容寫入新視窗
            newWindow.document.write(popupContent);
            newWindow.document.close(); // 重要：告訴瀏覽器寫入完成，這樣才會停止載入圖示
        }
    </script>
</body>
</html>
