# gemini-mood-spotify
same as mood spotify but coded by gemini
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mood → Spotify (GitHub Pages Ver.)</title>
    <style>
        :root {
            --primary-color: #1DB954; /* Spotify Green */
            --bg-color: #121212;
            --card-bg: #181818;
            --text-color: #FFFFFF;
            --sub-text: #B3B3B3;
        }

        body {
            font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
        }

        h1 {
            margin-bottom: 10px;
            font-size: 2.5rem;
            letter-spacing: -1px;
        }
        
        .subtitle {
            color: var(--sub-text);
            margin-bottom: 30px;
            font-size: 0.9rem;
        }

        /* 登入區塊 */
        #login-section {
            text-align: center;
            margin-top: 50px;
        }

        .btn-login {
            background-color: var(--primary-color);
            color: white;
            padding: 14px 32px;
            border-radius: 50px;
            text-decoration: none;
            font-weight: bold;
            font-size: 1rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            border: none;
            cursor: pointer;
            transition: transform 0.2s;
        }
        
        .btn-login:hover {
            transform: scale(1.05);
            background-color: #1ed760;
        }

        /* 主應用區塊 (預設隱藏) */
        #app-section {
            width: 100%;
            max-width: 600px;
            display: none; /* 登入前隱藏 */
        }

        /* 表單樣式 */
        .form-group {
            margin-bottom: 20px;
            background: var(--card-bg);
            padding: 20px;
            border-radius: 8px;
        }

        label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
            color: var(--primary-color);
        }

        select, input[type="range"] {
            width: 100%;
            padding: 10px;
            border-radius: 4px;
            border: 1px solid #333;
            background: #282828;
            color: white;
            font-size: 1rem;
        }

        .checkbox-group {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
        }

        .checkbox-label {
            background: #282828;
            padding: 8px 15px;
            border-radius: 20px;
            cursor: pointer;
            border: 1px solid transparent;
            font-weight: normal;
            color: var(--text-color);
            user-select: none;
        }

        /* 隱藏原生 checkbox，用 CSS 改變外觀 */
        .checkbox-label input {
            display: none;
        }

        .checkbox-label:has(input:checked) {
            background-color: var(--primary-color);
            color: black;
            font-weight: bold;
        }

        .submit-btn {
            width: 100%;
            background-color: white;
            color: black;
            border: none;
            padding: 15px;
            font-size: 1.1rem;
            font-weight: bold;
            border-radius: 50px;
            cursor: pointer;
            margin-top: 10px;
            transition: 0.2s;
        }

        .submit-btn:hover {
            background-color: var(--primary-color);
            color: white;
        }

        /* 錯誤訊息 */
        #error-message {
            color: #ff5c5c;
            background: rgba(255, 92, 92, 0.1);
            padding: 10px;
            border-radius: 4px;
            margin-top: 20px;
            display: none;
            text-align: center;
        }

        /* 歌單卡片區 */
        #results-area {
            margin-top: 40px;
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
            gap: 20px;
        }

        .card {
            background-color: var(--card-bg);
            border-radius: 8px;
            overflow: hidden;
            transition: background-color 0.3s;
            text-decoration: none;
            color: white;
            display: flex;
            flex-direction: column;
        }

        .card:hover {
            background-color: #282828;
        }

        .card img {
            width: 100%;
            aspect-ratio: 1/1;
            object-fit: cover;
        }

        .card-body {
            padding: 15px;
        }

        .card-title {
            font-weight: bold;
            margin: 0 0 5px 0;
            font-size: 0.95rem;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .card-desc {
            font-size: 0.8rem;
            color: var(--sub-text);
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
        }
    </style>
</head>
<body>

    <h1>Mood → Spotify</h1>
    <div class="subtitle">Powered by Spotify API</div>

    <div id="login-section">
        <p>請先登入 Spotify 以取得搜尋權限</p>
        <button id="login-btn" class="btn-login">Login with Spotify</button>
    </div>

    <div id="app-section">
        <form id="music-form">
            <div class="form-group">
                <label>現在心情如何？</label>
                <select id="mood">
                    <option value="happy">開心 (Happy)</option>
                    <option value="chill">放空 (Chill)</option>
                    <option value="tired">很累 (Tired)</option>
                    <option value="sad">想哭 (Sad)</option>
                    <option value="focus">想衝刺讀書 (Focus)</option>
                    <option value="energetic">想運動 (Energetic)</option>
                    <option value="lazy">想耍廢 (Lazy)</option>
                </select>
            </div>

            <div class="form-group">
                <label>想聽什麼風格？ (可多選)</label>
                <div class="checkbox-group">
                    <label class="checkbox-label"><input type="checkbox" name="style" value="lofi"> Lofi</label>
                    <label class="checkbox-label"><input type="checkbox" name="style" value="jazz"> Jazz</label>
                    <label class="checkbox-label"><input type="checkbox" name="style" value="classical"> Classical</label>
                    <label class="checkbox-label"><input type="checkbox" name="style" value="indie"> Indie</label>
                    <label class="checkbox-label"><input type="checkbox" name="style" value="r&b"> R&B</label>
                    <label class="checkbox-label"><input type="checkbox" name="style" value="rock"> Rock</label>
                    <label class="checkbox-label"><input type="checkbox" name="style" value="k-pop"> K-pop</label>
                </div>
            </div>

            <div class="form-group">
                <label>當下場景</label>
                <select id="scene">
                    <option value="study">讀書</option>
                    <option value="work">工作</option>
                    <option value="commute">通勤</option>
                    <option value="sleep">睡前</option>
                    <option value="workout">運動</option>
                    <option value="breakup">失戀</option>
                </select>
            </div>

            <div class="form-group">
                <label>能量指數 (0: 舒緩 ~ 100: 激昂)</label>
                <input type="range" id="energy" min="0" max="100" value="50">
                <div style="text-align: right; color: var(--sub-text); font-size: 0.8rem;">
                    目前：<span id="energy-val">50</span>%
                </div>
            </div>

            <button type="button" id="search-btn" class="submit-btn">幫我找歌單</button>
        </form>

        <div id="error-message"></div>

        <div id="results-area"></div>
    </div>

    <script>
        // ==========================================
        // CONFIGURATION (已填入你的資訊)
        // ==========================================
        
        const CLIENT_ID = '0b26f1d7c1a444ee9d12b89050936c0c'; 
        const REDIRECT_URI = 'https://michelle05102800-gif.github.io/gemini-mood-spotify/'; 

        const AUTH_ENDPOINT = 'https://accounts.spotify.com/authorize';
        const RESPONSE_TYPE = 'token';
        
        // ==========================================
        // 狀態管理
        // ==========================================
        let accessToken = null;

        const loginBtn = document.getElementById('login-btn');
        const loginSection = document.getElementById('login-section');
        const appSection = document.getElementById('app-section');
        const searchBtn = document.getElementById('search-btn');
        const errorMsg = document.getElementById('error-message');
        const resultsArea = document.getElementById('results-area');
        const energyInput = document.getElementById('energy');
        const energyVal = document.getElementById('energy-val');

        energyInput.addEventListener('input', (e) => {
            energyVal.textContent = e.target.value;
        });

        // ==========================================
        // 1. 登入流程 (Implicit Grant Flow)
        // ==========================================

        window.onload = () => {
            const hash = window.location.hash;
            
            if (hash && hash.includes('access_token')) {
                const tokenParam = hash.substring(1).split('&').find(elem => elem.startsWith('access_token'));
                accessToken = tokenParam.split('=')[1];
                
                window.location.hash = '';
                showApp();
            }
        };

        loginBtn.addEventListener('click', () => {
            const url = `${AUTH_ENDPOINT}?client_id=${CLIENT_ID}&redirect_uri=${encodeURIComponent(REDIRECT_URI)}&response_type=${RESPONSE_TYPE}&scope=user-read-private`;
            // 讓瀏覽器跳轉
            window.location.href = url;
        });

        function showApp() {
            loginSection.style.display = 'none';
            appSection.style.display = 'block';
        }

        // ==========================================
        // 2. 搜尋邏輯
        // ==========================================

        searchBtn.addEventListener('click', async () => {
            errorMsg.style.display = 'none';
            resultsArea.innerHTML = '<p style="text-align:center; width:100%;">正在搜尋 Spotify...</p>';

            const mood = document.getElementById('mood').value;
            const scene = document.getElementById('scene').value;
            const energy = parseInt(document.getElementById('energy').value);
            const styleCheckboxes = document.querySelectorAll('input[name="style"]:checked');
            const styles = Array.from(styleCheckboxes).map(cb => cb.value);

            let energyKeyword = "";
            if (energy < 30) energyKeyword = "sleep calm quiet";
            else if (energy < 60) energyKeyword = "chill moderate";
            else if (energy < 85) energyKeyword = "upbeat power";
            else energyKeyword = "high intensity party";

            // 關鍵字組合
            const query = `${mood} ${styles.join(' ')} ${scene} ${energyKeyword}`.trim();
            console.log("搜尋關鍵字:", query);

            if (!accessToken) {
                showError("尚未取得 Access Token，請重新登入。");
                loginSection.style.display = 'block';
                appSection.style.display = 'none';
                return;
            }

            try {
                const response = await fetch(`https://api.spotify.com/v1/search?q=${encodeURIComponent(query)}&type=playlist&limit=6`, {
                    method: 'GET',
                    headers: {
                        'Authorization': `Bearer ${accessToken}`
                    }
                });

                if (!response.ok) {
                    if (response.status === 401) {
                        throw new Error("Token 已過期，請重新整理頁面登入");
                    }
                    throw new Error(`API Error: ${response.statusText}`);
                }

                const data = await response.json();
                renderResults(data.playlists.items);

            } catch (error) {
                console.error(error);
                showError(error.message);
            }
        });

        function renderResults(playlists) {
            resultsArea.innerHTML = ''; 

            if (!playlists || playlists.length === 0) {
                resultsArea.innerHTML = '<p>找不到相關歌單，試試別的組合？</p>';
                return;
            }

            playlists.forEach(playlist => {
                const imageUrl = playlist.images && playlist.images.length > 0 ? playlist.images[0].url : 'https://via.placeholder.com/300?text=No+Image';
                const name = playlist.name;
                const description = playlist.description || "";
                const externalUrl = playlist.external_urls.spotify;

                const card = document.createElement('a');
                card.className = 'card';
                card.href = externalUrl;
                card.target = '_blank';

                card.innerHTML = `
                    <img src="${imageUrl}" alt="${name}">
                    <div class="card-body">
                        <div class="card-title">${name}</div>
                        <div class="card-desc">${description}</div>
                    </div>
                `;
                resultsArea.appendChild(card);
            });
        }

        function showError(msg) {
            errorMsg.textContent = msg;
            errorMsg.style.display = 'block';
            resultsArea.innerHTML = '';
        }

    </script>
</body>
</html>
