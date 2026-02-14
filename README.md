<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Symmetrical Heart - 9:16 Master Build</title>
    <link href="https://fonts.googleapis.com/css2?family=Caveat:wght@400;700&family=Dancing+Script:wght@600;700&family=Montserrat:wght@300;500&display=swap" rel="stylesheet">
    <style>
        :root {
            --dark-pink: #ff2a6d;
            --main-pink: #ffafcc;
            --bg-dark: #0a0a0a;
            --real-bg: #1e0a1a; 
        }

        body { 
            margin: 0; padding: 0; min-height: 100vh; width: 100vw; 
            display: flex; justify-content: center; align-items: center; 
            background: var(--bg-dark); overflow: hidden; font-family: 'Montserrat', sans-serif; 
            color: white; transition: background 1s ease; 
        }

        /* THE 9:16 VIEWPORT */
        #app { 
            height: 92vh;
            aspect-ratio: 9 / 16;
            position: relative; 
            overflow: hidden; 
            border: 8px solid #1a1a1a; 
            border-radius: 40px; 
            background: radial-gradient(circle, #1a1a1a 0%, #000 100%); 
            box-shadow: 0 0 80px rgba(0, 0, 0, 0.9), 0 0 30px rgba(255, 42, 109, 0.2); 
            z-index: 10; 
            display: flex; 
            flex-direction: column; 
            transition: all 0.6s cubic-bezier(0.23, 1, 0.32, 1);
        }

        #app::after {
            content: '';
            position: absolute;
            bottom: 8px;
            left: 50%;
            transform: translateX(-50%);
            width: 35%;
            height: 4px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 10px;
            z-index: 1000;
        }

        #app.full-canvas {
            height: 100vh;
            width: 100vw;
            max-width: none;
            border-radius: 0;
            border: none;
        }

        .screen { display: none; flex-direction: column; height: 100%; justify-content: center; align-items: center; text-align: center; position: absolute; width: 100%; top: 0; left: 0; padding: 20px; box-sizing: border-box; }
        .screen.active { display: flex; }

        .btn-action { background: var(--dark-pink); color: white; border: none; padding: 12px 25px; border-radius: 50px; cursor: pointer; font-weight: bold; margin-top: 20px; transition: 0.3s; font-size: 0.85rem; z-index: 100; }
        .btn-back { position: absolute; top: 25px; left: 20px; background: rgba(255,255,255,0.1); color: white; border: none; padding: 8px 12px; border-radius: 15px; font-size: 0.65rem; cursor: pointer; z-index: 500; }
        .handwritten { font-family: 'Dancing Script', cursive; font-size: 2rem; margin: 10px 0; }

        .gif-container { width: 140px; height: 140px; background: #2d0d1a; border-radius: 50%; display: flex; justify-content: center; align-items: center; overflow: hidden; border: 3px solid var(--dark-pink); margin-bottom: 20px; }
        .gif-container img { width: 80%; }

        .meter-container { width: 80%; background: #222; height: 14px; border-radius: 10px; overflow: hidden; margin: 15px 0; }
        .meter-fill { width: 0%; height: 100%; background: linear-gradient(90deg, var(--main-pink), var(--dark-pink)); }

        .note-card { background: rgba(255, 175, 204, 0.05); backdrop-filter: blur(10px); border: 1px solid rgba(255, 255, 255, 0.1); width: 90%; padding: 20px; border-radius: 15px; position: relative; }
        .note-text { font-family: 'Caveat', cursive; font-size: 1.2rem; line-height: 1.3; color: #fff; min-height: 120px; text-align: left; }
        
        .keyboard { width: 100%; margin-top: 15px; display: flex; flex-direction: column; gap: 4px; padding: 5px; background: rgba(0,0,0,0.5); border-radius: 10px; }
        .kb-row { display: flex; justify-content: center; gap: 3px; }
        .key { flex: 1; height: 24px; background: rgba(255,255,255,0.08); border-radius: 3px; display: flex; align-items: center; justify-content: center; font-size: 0.45rem; color: rgba(255,255,255,0.5); text-transform: uppercase; }
        .key.glow { background: var(--main-pink); color: #000; box-shadow: 0 0 8px var(--main-pink); }
        
        .finger { position: absolute; width: 25px; height: 25px; background: rgba(255, 255, 255, 0.15); border-radius: 50%; pointer-events: none; z-index: 1000; transition: all 0.1s ease; opacity: 0; border: 1px solid rgba(255,255,255,0.3); }
        .finger.visible { opacity: 1; }

        .polaroid-main { background: white; padding: 6px 6px 28px 6px; width: 130px; aspect-ratio: 9/16; position: absolute; left: 50%; top: 45%; transform: translate(-50%, -50%) scale(0); box-shadow: 0 10px 30px rgba(0,0,0,0.8); z-index: 100; transition: 0.5s; }
        .polaroid-main p { color: #333; margin: 5px 0 0 0; font-size: 0.9rem; font-family: 'Dancing Script', cursive; font-weight: bold; }
        .small-polaroid { position: absolute; width: 70px; aspect-ratio: 9/16; background: white; padding: 4px 4px 15px 4px; box-shadow: 0 5px 15px rgba(0,0,0,0.5); opacity: 0; z-index: 10; transition: all 0.5s ease; text-align: center; cursor: pointer; }
        .photo-wrapper img { width: 100%; height: 100%; object-fit: cover; }
        .zoom-to-center { z-index: 9999 !important; transform: translate(-50%, -50%) scale(2.8) !important; left: 50% !important; top: 50% !important; }
        .scrapbook-nav { position: absolute; bottom: 30px; width: 100%; display: flex; justify-content: space-between; padding: 0 20px; box-sizing: border-box; }
    </style>
</head>
<body>

<div id="app">
    <section id="screen1" class="screen active">
        <div class="gif-container"><img src="https://media.tenor.com/AqDR57xYDdUAAAAM/hi-wave.gif"></div>
        <h2 class="handwritten">Hey Beautiful</h2>
        <button class="btn-action" onclick="showScreen(2)">yeah</button>
    </section>

    <section id="screen2" class="screen">
        <button class="btn-back" onclick="showScreen(1)">← Back</button>
        <h2 class="handwritten">Cuteness Meter</h2>
        <h3 id="meter-percent" style="font-size: 2.2rem;">0%</h3>
        <div class="meter-container"><div id="meter-fill" class="meter-fill"></div></div>
        <button id="start-meter-btn" class="btn-action" onclick="runInfiniteMeter()">Start Counting</button>
        <button class="btn-action" onclick="showScreen('2_5')" style="font-size: 0.6rem;">Next →</button>
    </section>

    <section id="screen2_5" class="screen">
        <h2 class="handwritten">A gift for you</h2>
        <div id="bouquet-area" style="height: 150px; position: relative; width: 100%;"></div>
        <div style="display: flex; gap: 10px; margin-top: 10px;">
            <div onclick="collectFlower(this)" style="font-size: 2rem; cursor: pointer;">🌷</div>
            <div onclick="collectFlower(this)" style="font-size: 2rem; cursor: pointer;">🌷</div>
            <div onclick="collectFlower(this)" style="font-size: 2rem; cursor: pointer;">🌷</div>
            <div onclick="collectFlower(this)" style="font-size: 2rem; cursor: pointer;">🌷</div>
        </div>
        <button id="seal-btn" class="btn-action" style="display:none;" onclick="showScreen(3)">Seal with a Kiss 💋</button>
    </section>

    <section id="screen3" class="screen">
        <h2 class="handwritten">Pick a gift</h2>
        <div id="mystery-grid" style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; width: 90%;"></div>
    </section>

    <section id="screen4" class="screen">
        <div class="note-card">
            <div id="finger-l" class="finger"></div>
            <div id="finger-r" class="finger"></div>
            <div class="note-text" id="typewriter-text"></div>
            <div class="keyboard">
                <div class="kb-row"><div class="key" data-char="q">q</div><div class="key" data-char="w">w</div><div class="key" data-char="e">e</div><div class="key" data-char="r">r</div><div class="key" data-char="t">t</div><div class="key" data-char="y">y</div><div class="key" data-char="u">u</div><div class="key" data-char="i">i</div><div class="key" data-char="o">o</div><div class="key" data-char="p">p</div></div>
                <div class="kb-row"><div class="key" data-char="a">a</div><div class="key" data-char="s">s</div><div class="key" data-char="d">d</div><div class="key" data-char="f">f</div><div class="key" data-char="g">g</div><div class="key" data-char="h">h</div><div class="key" data-char="j">j</div><div class="key" data-char="k">k</div><div class="key" data-char="l">l</div></div>
                <div class="kb-row"><div class="key" data-char="z">z</div><div class="key" data-char="x">x</div><div class="key" data-char="c">c</div><div class="key" data-char="v">v</div><div class="key" data-char="b">b</div><div class="key" data-char="n">n</div><div class="key" data-char="m">m</div></div>
                <div class="kb-row"><div class="key" data-char=" " style="flex:4">space</div><div id="done-key" class="key" style="flex:2" onclick="showScreen('4_5')">done</div></div>
            </div>
        </div>
    </section>

    <section id="screen4_5" class="screen">
        <h2 class="handwritten">Pick the vibe</h2>
        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px;">
            <button class="btn-action" onclick="applyMood('romantic')">Warm</button>
            <button class="btn-action" onclick="applyMood('dreamy')">Dreamy</button>
        </div>
    </section>

    <section id="screen5" class="screen">
        <div id="scrapbook-area">
            <h1 class="handwritten" style="position: absolute; top: 20px; width: 100%; z-index: 5;">Our Story</h1>
            <div class="polaroid-main" id="hero-photo" onclick="toggleZoom(this)">
                <div class="photo-wrapper"><img src="https://i.postimg.cc/bNqCdwBV/Spotify_Installer.jpg" id="slideshow-img"></div>
                <p>Bestie Forever ❤️</p>
            </div>
            <div id="scatter-container" style="position: absolute; width: 100%; height: 100%; top: 0; left: 0;"></div>
            <div class="scrapbook-nav">
                <button class="btn-action" style="font-size: 0.6rem; padding: 10px;" onclick="showScreen('4_5')">← Back</button>
                <button class="btn-action" style="font-size: 0.6rem; padding: 10px;" onclick="location.reload()">Restart ↺</button>
            </div>
        </div>
    </section>
</div>

<script>
    // UPDATED PHOTO ARRAY
    const heartPhotos = [
        "https://i.postimg.cc/3rXfKvnx/photo_2026_02_14_14_34_39.jpg",
        "https://i.postimg.cc/8kRX16t1/photo_2026_02_14_14_34_42.jpg",
        "https://i.postimg.cc/3rXfKvn8/photo_2026_02_14_14_34_43.jpg",
        "https://i.postimg.cc/RC7pMHXW/photo_2026_02_14_14_34_44.jpg",
        "https://i.postimg.cc/fWcqw9BS/photo_2026_02_14_14_34_45.jpg",
        "https://i.postimg.cc/66rb9vYZ/photo_2026_02_14_14_34_46.jpg",
        "https://i.postimg.cc/c1RksnX8/photo_2026_02_14_14_34_47.jpg",
        "https://i.postimg.cc/KcPpZMJL/photo_2026_02_14_14_34_48_1.jpg", // Fixed link format
        "https://i.postimg.cc/ZKJ705Mv/photo_2026_02_14_14_34_49.jpg",
        "https://i.postimg.cc/yY7QW8bJ/photo_2026_02_14_14_34_50.jpg",
        "https://i.postimg.cc/BQJYtnV1/photo_2026_02_14_14_34_51.jpg",
        "https://i.postimg.cc/3J3SWxcG/photo_2026_02_14_14_34_51_1.jpg", // Fixed link format
        "https://i.postimg.cc/yY7QW8bF/photo_2026_02_14_14_34_52.jpg",
        "https://i.postimg.cc/jdRgCSF6/photo_2026_02_14_14_34_54.jpg",
        "https://i.postimg.cc/qMpbgv5L/photo_2026_02_14_14_34_56.jpg",
        "https://i.postimg.cc/J4R6tz2x/photo_2026_02_14_14_55_26.jpg",
        "https://i.postimg.cc/8PTKsz0n/photo_2026_02_14_14_55_27.jpg",
        "https://i.postimg.cc/dtJHDVxf/photo_2026_02_14_14_55_29.jpg",
        "https://i.postimg.cc/bNqCdwBV/Spotify_Installer.jpg",
        "https://i.postimg.cc/ncnRMhPN/Whats_App_Image_2026_02_14_at_01_55_02.jpg",
        "https://i.postimg.cc/rFgh42Bj/Whats_App_Image_2026_02_14_at_01_55_03.jpg",
        "https://i.postimg.cc/tCDr6GKr/Whats_App_Image_2026_02_14_at_01_55_06_p.jpg",
        "https://i.postimg.cc/8PwyvV88/Whats_App_Image_2026_02_14_at_13_30_26.jpg",
        "https://i.postimg.cc/ZKjfNzGQ/Whats_App_Image_2026_02_14_at_13_30_26_copy.jpg",
        "https://i.postimg.cc/28THWNsP/Whats_App_Image_2026_02_14_at_13_30_27.jpg",
        "https://i.postimg.cc/ncTdmt8f/Whats_App_Image_2026_02_14_at_14_56_47.jpg",
        "https://i.postimg.cc/KvQ9Th6C/Whats_App_Image_2026_02_14_at_13_30_28.jpg",
        "https://i.postimg.cc/ncTdmfyT/Whats_App_Image_2026_02_14_at_1o3_30_27.jpg",
        "https://i.postimg.cc/vHXPndRs/Whats_App_Image_22026_02_14_at_13_30_28.jpg"
    ];

    let meterInterval, currentPercent = 0, collectedFlowers = 0, activeZoom = null;

    function showScreen(id) {
        const app = document.getElementById('app');
        if (id == 5) app.classList.add('full-canvas');
        else app.classList.remove('full-canvas');

        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById(`screen${id}`).classList.add('active');
        if(id === 3) initMysteryGrid();
        if(id === 4) startTypewriter();
        if(id === 5) startScrapbook();
    }

    function runInfiniteMeter() {
        document.getElementById('start-meter-btn').style.display = 'none';
        meterInterval = setInterval(() => {
            currentPercent += Math.floor(Math.random() * 5) + 2;
            document.getElementById('meter-fill').style.width = (currentPercent % 100) + '%';
            document.getElementById('meter-percent').innerText = currentPercent + '%';
        }, 80);
    }

    function startTypewriter() {
        const text = "I just wanted to say... you are special. There's a genuine softness in your soul.";
        const element = document.getElementById('typewriter-text');
        element.innerText = ''; let i = 0;
        const fL = document.getElementById('finger-l'); const fR = document.getElementById('finger-r');
        function type() {
            if (i < text.length) {
                const char = text.charAt(i).toLowerCase();
                element.innerText += text.charAt(i);
                const targetKey = document.querySelector(`.key[data-char="${char}"]`) || document.querySelector(`.key[data-char=" "]`);
                if (targetKey) {
                    const activeFinger = "qwertaszxcvb".includes(char) ? fL : fR;
                    activeFinger.classList.add('visible');
                    const kR = targetKey.getBoundingClientRect(); const nR = document.querySelector('.note-card').getBoundingClientRect();
                    activeFinger.style.left = (kR.left - nR.left + 5) + "px";
                    activeFinger.style.top = (kR.top - nR.top + 5) + "px";
                    targetKey.classList.add('glow');
                    setTimeout(() => targetKey.classList.remove('glow'), 100);
                }
                i++; setTimeout(type, 100);
            }
        }
        type();
    }

    function startScrapbook() {
        const container = document.getElementById('scatter-container');
        container.innerHTML = '';
        const hero = document.getElementById('hero-photo');
        setTimeout(() => hero.style.transform = "translate(-50%, -50%) scale(1)", 400);

        heartPhotos.forEach((link, i) => {
            const div = document.createElement('div');
            div.className = 'small-polaroid';
            const t = (i / heartPhotos.length) * 2 * Math.PI;
            const heartX = 16 * Math.pow(Math.sin(t), 3);
            const heartY = -(13 * Math.cos(t) - 5 * Math.cos(2 * t) - 2 * Math.cos(3 * t) - Math.cos(4 * t));
            div.style.left = (50 + heartX * 2.2) + '%';
            div.style.top = (45 + heartY * 2.2) + '%';
            div.innerHTML = `<div class="photo-wrapper"><img src="${link}"></div>`;
            div.onclick = () => toggleZoom(div);
            container.appendChild(div);
            setTimeout(() => { div.style.opacity = "1"; div.style.transform = `translate(-50%, -50%) rotate(${Math.random()*20-10}deg) scale(1)`; }, 500 + (i * 40));
        });
    }

    function toggleZoom(el) {
        if (activeZoom === el) { el.classList.remove('zoom-to-center'); activeZoom = null; }
        else { if (activeZoom) activeZoom.classList.remove('zoom-to-center'); activeZoom = el; el.classList.add('zoom-to-center'); }
    }

    function initMysteryGrid() {
        const grid = document.getElementById('mystery-grid'); grid.innerHTML = '';
        for(let i=0; i<9; i++) {
            const box = document.createElement('div'); box.className = 'grid-box'; box.style.background = "var(--real-bg)"; box.style.height="70px"; box.style.borderRadius="10px"; box.style.display="flex"; box.style.alignItems="center"; box.style.justifyContent="center"; box.style.cursor="pointer"; box.innerText = "🎁";
            box.onclick = () => { box.innerText = "✨ Precious"; setTimeout(() => showScreen(4), 800); };
            grid.appendChild(box);
        }
    }

    function collectFlower(el) { el.style.opacity = "0"; collectedFlowers++; if(collectedFlowers >= 4) document.getElementById('seal-btn').style.display = 'block'; }
    function applyMood(mood) { document.body.style.background = mood === 'romantic' ? "linear-gradient(#2c0812, #ff4d6d)" : "linear-gradient(#1a2a6c, #fdbb2d)"; showScreen(5); }
</script>
</body>
</html>
