<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Focus Station: Necati Edition</title>
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.5.1/dist/confetti.browser.min.js"></script>
    <style>
        :root {
            --bg: #f3f4f6; --card: #ffffff; --text: #1f2937;
            --primary: #6366f1; --accent: #10b981; --glow: #6366f1;
        }
        .dark-mode {
            --bg: #111827; --card: #1f2937; --text: #f9fafb;
            --primary: #818cf8;
        }
        body {
            font-family: 'Segoe UI', sans-serif; background: var(--bg);
            color: var(--text); transition: 0.3s; display: flex;
            flex-direction: column; align-items: center; padding: 20px; margin: 0; overflow-x: hidden;
        }
        .container { max-width: 450px; width: 100%; z-index: 10; margin-bottom: 100px; }
        .header { display: flex; justify-content: space-between; align-items: center; width: 100%; margin-bottom: 20px; }
        
        .card {
            background: var(--card); padding: 20px; border-radius: 24px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.05); margin-bottom: 15px;
        }

        /* --- FİL NECATİ VE KONUŞMA BALONU --- */
        #necati-buddy {
            position: fixed; bottom: 10px; left: 10px; width: 80px; height: 80px;
            z-index: 999; cursor: pointer; transition: all 0.8s ease-in-out;
            font-size: 60px; display: flex; align-items: center; justify-content: center;
        }
        
        #necati-speech {
            position: fixed; bottom: 100px; left: 20px; background: white;
            padding: 10px 15px; border-radius: 20px; box-shadow: 0 5px 15px rgba(0,0,0,0.2);
            font-size: 13px; font-weight: bold; color: #333; display: none;
            z-index: 1000; max-width: 160px; border: 2px solid #6366f1;
        }
        #necati-speech::after {
            content: ''; position: absolute; bottom: -10px; left: 20px;
            border-width: 10px 10px 0; border-style: solid; border-color: white transparent;
        }

        /* --- DERS SEÇİCİ --- */
        .lesson-box {
            padding: 3px; border-radius: 18px; margin-bottom: 15px;
            transition: 0.5s ease; box-shadow: 0 0 15px var(--glow);
            background: var(--glow);
        }
        select {
            width: 100%; padding: 15px; border-radius: 16px; border: none;
            background: var(--card); color: var(--text); font-size: 18px;
            font-weight: 800; text-align: center; cursor: pointer; outline: none;
        }
        
        .focus-active { animation: breathe 2.5s infinite ease-in-out; }
        @keyframes breathe {
            0%, 100% { box-shadow: 0 0 10px var(--glow); }
            50% { box-shadow: 0 0 30px var(--glow); }
        }

        #timer { font-size: 65px; font-weight: 900; text-align: center; margin: 10px 0; color: var(--primary); }
        
        button { padding: 15px; border: none; border-radius: 15px; cursor: pointer; font-weight: bold; width: 100%; transition: 0.2s; }
        .btn-p { background: var(--primary); color: white; font-size: 16px; margin-bottom: 8px; }
        .btn-reset { background: rgba(239, 68, 68, 0.1); color: #ef4444; font-size: 14px; border: 1px solid rgba(239, 68, 68, 0.1); }

        .water-container { display: flex; justify-content: space-around; font-size: 32px; margin: 10px 0; }
        .task-item {
            background: rgba(129, 140, 248, 0.1); padding: 14px; border-radius: 12px;
            margin-bottom: 8px; display: flex; justify-content: space-between; align-items: center;
        }
        .done { text-decoration: line-through; opacity: 0.5; }
        
        .vibrate { animation: shake 0.5s infinite; border: 2px solid #ef4444 !important; }
        @keyframes shake { 0%, 100% { transform: translateX(0); } 25% { transform: translateX(-4px); } 75% { transform: translateX(4px); } }
    </style>
</head>
<body>

<div id="necati-speech">Hadi ders başına muhtişim çocuk!</div>
<div id="necati-buddy" onclick="necatiTalkManual()">🐘</div>

<div class="container">
    <div class="header">
        <h2 style="margin:0;">🚀 FocusStation</h2>
        <button onclick="toggleTheme()" id="themeBtn" style="width:50px; padding:10px; border-radius:50px; background:var(--card); border:1px solid #ddd;">🌙</button>
    </div>

    <div class="card" id="timerCard">
        <div class="lesson-box" id="lessonBox">
            <select id="lessonSelect" onchange="updateLessonColor()">
                <option value="matematik">📐 MATEMATİK</option>
                <option value="fizik">⚡ FİZİK</option>
                <option value="kimya">🧪 KİMYA</option>
                <option value="turkce">📖 TÜRKÇE</option>
                <option value="tarih">📜 TARİH</option>
                <option value="cografya">🌍 COĞRAFYA</option>
                <option value="biyoloji">🧬 BİYOLOJİ</option>
            </select>
        </div>
        <input type="number" id="minInput" value="25" style="width:100%; padding:10px; text-align:center; border-radius:10px; border:1px solid #ddd; font-weight:bold; background:var(--card); color:var(--text);">
        <div id="timer">25:00</div>
        <button class="btn-p" onclick="toggleTimer()" id="startBtn">BAŞLAT</button>
        <button class="btn-reset" onclick="resetTimer()">SIFIRLA</button>
    </div>

    <div class="card" id="waterCard">
        <h3 style="margin:0; font-size:16px; text-align:center;">💧 SU TAKİBİ</h3>
        <div class="water-container">
            <span style="cursor:pointer;" onclick="drinkWater(this)">🥤</span>
            <span style="cursor:pointer;" onclick="drinkWater(this)">🥤</span>
            <span style="cursor:pointer;" onclick="drinkWater(this)">🥤</span>
            <span style="cursor:pointer;" onclick="drinkWater(this)">🥤</span>
            <span style="cursor:pointer;" onclick="drinkWater(this)">🥤</span>
            <span style="cursor:pointer;" onclick="drinkWater(this)">🥤</span>
        </div>
    </div>

    <div class="card">
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:10px;">
            <h3 style="margin:0; font-size:18px;">📍 PLANLARIM</h3>
            <span id="taskStats" style="font-weight:bold;">0/0</span>
        </div>
        <div style="display:flex; gap:5px;">
            <input type="text" id="taskInput" placeholder="Yeni plan..." style="flex:1; padding:10px; border-radius:10px; border:1px solid #ddd; background:var(--card); color:var(--text);">
            <button onclick="addTask()" style="width:60px; background:var(--accent); color:white; border-radius:15px;">+</button>
        </div>
        <div id="taskList" style="margin-top:15px;"></div>
    </div>
</div>

<script>
    let timerId = null, timeLeft = 25 * 60, lastWaterTime = Date.now();
    let totalTasks = 0, completedTasks = 0;

    const necatiQuotes = [
        "Muhtişim bir odaklanma görüyorum!",
        "Kürşat abinin morali bozulmasın, çalış çalış!",
        "Ders bitince dürüm ısmarlayacak mısın?",
        "Ders çalışmak muhtişim bir şeydir evladım!",
        "Bak hala telefona bakıyor! Bırak onu!",
        "Bitince Remzi'yle okey oynayacağız, hadi bitir!",
        "Sen bu gidişle profesör olursun evladım!"
    ];

    const necati = document.getElementById('necati-buddy');
    const speech = document.getElementById('necati-speech');

    function moveNecati() {
        const x = Math.random() * (window.innerWidth - 100);
        necati.style.left = x + 'px';
        if(Math.random() > 0.4) {
            necatiTalk(necatiQuotes[Math.floor(Math.random() * necatiQuotes.length)]);
        }
    }

    function necatiTalk(text) {
        speech.innerText = text;
        speech.style.display = 'block';
        speech.style.left = (parseInt(necati.style.left) - 20) + 'px';
        setTimeout(() => { speech.style.display = 'none'; }, 3500);
    }

    function necatiTalkManual() {
        necatiTalk("Dürüm var mı dürüm? Acıktım!");
    }

    setInterval(moveNecati, 12000); 

    function updateLessonColor() {
        const lesson = document.getElementById("lessonSelect").value;
        const sayisal = ["matematik", "fizik", "kimya"];
        const sozel = ["turkce", "tarih", "cografya"];
        
        if (sayisal.includes(lesson)) document.documentElement.style.setProperty('--glow', '#22d3ee');
        else if (sozel.includes(lesson)) document.documentElement.style.setProperty('--glow', '#fbbf24');
        else document.documentElement.style.setProperty('--glow', '#4ade80');
    }

    function toggleTheme() {
        document.body.classList.toggle('dark-mode');
        document.getElementById("themeBtn").innerText = document.body.classList.contains('dark-mode') ? "☀️" : "🌙";
    }

    function updateDisplay() {
        let m = Math.floor(timeLeft / 60), s = timeLeft % 60;
        document.getElementById("timer").innerText = `${m}:${s < 10 ? '0'+s : s}`;
    }

    function toggleTimer() {
        if(timerId) {
            clearInterval(timerId); timerId = null;
            document.getElementById("startBtn").innerText = "DEVAM ET";
            document.getElementById("lessonBox").classList.remove("focus-active");
            necatiTalk("Aaa! Nereye? Daha yeni ısınmıştık!");
        } else {
            if(timeLeft === 25*60 || timeLeft === document.getElementById("minInput").value * 60) {
                 timeLeft = document.getElementById("minInput").value * 60;
            }
            document.getElementById("startBtn").innerText = "DURDUR";
            document.getElementById("lessonBox").classList.add("focus-active");
            necatiTalk("Muhtişim! Başlıyoruz!");
            timerId = setInterval(() => {
                timeLeft--; updateDisplay(); checkWater();
                if(timeLeft <= 0) {
                    clearInterval(timerId); fireConfetti();
                    necatiTalk("BİTTİ! Muhtişimsin evladım!");
                    resetTimer();
                }
            }, 1000);
        }
    }

    function resetTimer() {
        clearInterval(timerId); timerId = null;
        timeLeft = document.getElementById("minInput").value * 60;
        updateDisplay(); 
        document.getElementById("startBtn").innerText = "BAŞLAT";
        document.getElementById("lessonBox").classList.remove("focus-active");
    }

    function drinkWater(el) {
        if(el.innerText !== "🔵") {
            el.innerText = "🔵";
            lastWaterTime = Date.now();
            document.getElementById("waterCard").classList.remove("vibrate");
            necatiTalk("Löp löp su olsun, zihin açsın!");
        }
    }

    function checkWater() {
        if ((Date.now() - lastWaterTime) > (50 * 60 * 1000)) {
            document.getElementById("waterCard").classList.add("vibrate");
            if (navigator.vibrate) navigator.vibrate([300, 100, 300]);
        }
    }

    function addTask() {
        const inp = document.getElementById("taskInput");
        if(!inp.value) return;
        totalTasks++;
        const div = document.createElement("div");
        div.className = "task-item";
        div.innerHTML = `<span onclick="this.parentElement.classList.toggle('done'); updateStats();">${inp.value}</span> <span onclick="this.parentElement.remove(); totalTasks--; updateStats();">🗑️</span>`;
        document.getElementById("taskList").appendChild(div);
        inp.value = ""; updateStats();
        necatiTalk("Yeni bir hedef! Muhtişim!");
    }

    function updateStats() {
        completedTasks = document.querySelectorAll('.done').length;
        document.getElementById("taskStats").innerText = `${completedTasks}/${totalTasks}`;
    }

    function fireConfetti() {
        confetti({ particleCount: 150, spread: 70, origin: { y: 0.6 } });
    }
    
    window.onload = () => {
        updateLessonColor();
        setTimeout(moveNecati, 2000);
    };
</script>
</body>
</html>
