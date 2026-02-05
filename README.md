<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Aura OS V12 - Professional</title>
    <style>
        :root { --accent: #00f2ff; --bg: #0a0a0a; --panel: rgba(20, 20, 25, 0.9); }
        * { box-sizing: border-box; font-family: 'Segoe UI', sans-serif; cursor: default; user-select: none; }
        body { margin: 0; height: 100vh; overflow: hidden; background: #000; color: white; }
        
        #desktop { height: 100%; width: 100%; background: url('https://images.unsplash.com/photo-1451187580459-43490279c0fa?w=1600') center/cover; position: relative; }
        
        /* TASKBAR FIXED */
        #taskbar { position: fixed; bottom: 0; width: 100%; height: 55px; background: rgba(0,0,0,0.8); backdrop-filter: blur(20px); display: flex; align-items: center; padding: 0 15px; z-index: 10000; border-top: 1px solid rgba(255,255,255,0.1); }
        #apps-dock { display: flex; gap: 12px; flex: 1; justify-content: center; }
        .dock-icon { width: 42px; height: 42px; background: rgba(255,255,255,0.05); border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 22px; transition: 0.3s; border: 1px solid rgba(255,255,255,0.1); }
        .dock-icon:hover { transform: translateY(-5px); background: var(--accent); color: #000; }

        /* WINDOWS FIXED */
        .aura-win { position: absolute; background: var(--panel); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.15); border-radius: 15px; box-shadow: 0 20px 60px rgba(0,0,0,0.8); display: flex; flex-direction: column; overflow: hidden; z-index: 1000; }
        .win-bar { padding: 12px 18px; background: rgba(255,255,255,0.05); display: flex; justify-content: space-between; cursor: move; align-items: center; }
        .win-body { flex: 1; padding: 15px; overflow: auto; }
        .close-btn { color: #ff5f57; font-size: 20px; cursor: pointer; font-weight: bold; }

        /* APP STYLES */
        .file-item { padding: 10px; border-bottom: 1px solid rgba(255,255,255,0.05); display: flex; align-items: center; gap: 10px; }
        .file-item:hover { background: rgba(255,255,255,0.05); }
        textarea { width: 100%; height: 80%; background: transparent; color: white; border: 1px solid #333; padding: 10px; border-radius: 8px; outline: none; resize: none; }
        
        /* ICONS GRID */
        .icon-grid { display: grid; grid-template-columns: repeat(auto-fill, 90px); gap: 20px; padding: 25px; }
        .desk-app { display: flex; flex-direction: column; align-items: center; text-align: center; font-size: 11px; }
        .desk-app span:first-child { font-size: 40px; margin-bottom: 5px; filter: drop-shadow(0 5px 10px rgba(0,0,0,0.5)); }

        .btn-p { background: var(--accent); color: black; border: none; padding: 10px; border-radius: 8px; font-weight: bold; width: 100%; margin-top: 10px; }
    </style>
</head>
<body>

<div id="desktop">
    <div class="icon-grid" id="main-grid"></div>
    <div id="window-layer"></div>
</div>

<div id="taskbar">
    <div style="font-size:26px; cursor:pointer">💠</div>
    <div id="apps-dock"></div>
    <div id="time-area" style="text-align:right; font-size:12px;">
        <div id="clock" style="font-weight:bold;">00:00</div>
        <div id="date" style="opacity:0.5;">05/02/26</div>
    </div>
</div>

<script>
    let zIdx = 1000;

    const Aura = {
        open: function(name, icon, html, w=400, h=350) {
            const id = 'w_' + Date.now();
            const win = document.createElement('div');
            win.className = 'aura-win'; win.id = id;
            win.style.width = w+'px'; win.style.height = h+'px';
            win.style.top = '100px'; win.style.left = '100px';
            win.style.zIndex = ++zIdx;

            win.innerHTML = `
                <div class="win-bar" onmousedown="Aura.drag(event, '${id}')">
                    <span style="font-size:12px; font-weight:bold; letter-spacing:1px;">${name}</span>
                    <span class="close-btn" onclick="Aura.close('${id}')">×</span>
                </div>
                <div class="win-body">${html}</div>
            `;
            document.getElementById('window-layer').appendChild(win);
            this.dock(icon, id);
        },
        close: (id) => { document.getElementById(id).remove(); document.getElementById('d_'+id).remove(); },
        dock: (icon, id) => {
            const d = document.createElement('div');
            d.className = 'dock-icon'; d.id = 'd_'+id; d.innerText = icon;
            document.getElementById('apps-dock').appendChild(d);
        },
        drag: (e, id) => {
            const w = document.getElementById(id); w.style.zIndex = ++zIdx;
            let sx = e.clientX - w.offsetLeft, sy = e.clientY - w.offsetTop;
            document.onmousemove = (ev) => { w.style.left = (ev.clientX-sx)+'px'; w.style.top = (ev.clientY-sy)+'px'; };
            document.onmouseup = () => document.onmousemove = null;
        }
    };

    // --- DEEP APPS LOGIC ---

    function launchFiles() {
        Aura.open("File Manager", "📂", `
            <div id="file-list">
                <div class="file-item">📁 <b>Documents</b></div>
                <div class="file-item">🖼️ <b>Gallery</b></div>
                <div class="file-item">🎵 <b>Music</b></div>
                <div class="file-item">💾 <b>System_Storage (C:)</b></div>
            </div>
        `, 320, 300);
    }

    function launchNotes() {
        Aura.open("Aura Notes", "📝", `
            <textarea id="note-text" placeholder="Start typing your ideas..."></textarea>
            <button class="btn-p" onclick="alert('Note Saved to Aura Cloud!')">SAVE NOTE</button>
        `, 350, 400);
    }

    function launchWeather() {
        Aura.open("Weather", "☁️", `
            <div style="text-align:center; padding:20px;">
                <h1 style="margin:0;">26°C</h1>
                <p>Lucknow, India</p>
                <div style="font-size:50px;">⛅</div>
                <p>Partly Cloudy</p>
            </div>
        `, 280, 280);
    }

    // --- INITIALIZE DESKTOP ---
    window.onload = () => {
        const apps = [
            {n: "Browser", i: "🌐", f: () => Aura.open("Chrome", "🌐", `<iframe src="https://www.bing.com" style="width:100%; height:100%; border:none; background:white;"></iframe>`, 600, 450)},
            {n: "Files", i: "📂", f: launchFiles},
            {n: "Notepad", i: "📝", f: launchNotes},
            {n: "Weather", i: "☁️", f: launchWeather},
            {n: "Store", i: "🛍️", f: () => alert('Aura Store is updating...')},
            {n: "Settings", i: "⚙️", f: () => alert('Settings Panel V2')}
        ];

        const grid = document.getElementById('main-grid');
        apps.forEach(app => {
            grid.innerHTML += `<div class="desk-app" onclick="(${app.f})()"><span>${app.i}</span><span>${app.n}</span></div>`;
        });

        setInterval(() => {
            const d = new Date();
            document.getElementById('clock').innerText = d.toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
            document.getElementById('date').innerText = d.toLocaleDateString();
        }, 1000);
    };
</script>
</body>
</html>
