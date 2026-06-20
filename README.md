<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>9Anime Ultra - Fixed Player & Manga</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <style>
        body { background-color: #0d0e12; color: #bcc0cc; }
        .bg-anime-dark { background-color: #181920; }
        .bg-anime-card { background-color: #1f2029; }
        .text-9anime { color: #5f2eea; }
        .border-9anime { border-color: #5f2eea; }
        .bg-9anime { background-color: #5f2eea; }
    </style>
</head>
<body class="font-sans antialiased min-h-screen flex flex-col">

    <header class="bg-anime-dark border-b border-purple-950/30 sticky top-0 z-50 px-4 py-3 shadow-xl">
        <div class="max-w-7xl mx-auto flex flex-col gap-3 sm:flex-row sm:items-center sm:justify-between">
            <div class="flex items-center justify-between sm:justify-start gap-6">
                <a href="#" onclick="window.location.reload()" class="text-2xl font-black tracking-tighter text-white">
                    <span class="text-purple-500">9</span>ANIME<span class="text-purple-400 text-xs font-bold ml-1">FIXED</span>
                </a>
                <nav class="flex gap-1.5 bg-black/30 p-1 rounded-lg border border-purple-950/20">
                    <button onclick="switchTab('anime')" id="tab-btn-anime" class="px-3 py-1 text-xs font-bold rounded-md transition text-white bg-purple-600">Anime</button>
                    <button onclick="switchTab('manga')" id="tab-btn-manga" class="px-3 py-1 text-xs font-bold rounded-md transition text-gray-400 hover:text-white">Manga</button>
                </nav>
            </div>
            
            <div class="w-full sm:max-w-md flex gap-2">
                <input type="text" id="main-search" placeholder="Search catalog (Anime or Manga)..." class="w-full bg-black/40 text-white border border-purple-950 rounded-lg px-3 py-2 text-sm focus:outline-none focus:border-purple-500">
                <button id="exec-search" class="bg-purple-600 hover:bg-purple-700 text-white px-4 py-2 rounded-lg text-sm font-bold/10">Search</button>
            </div>
        </div>
    </header>

    <main class="flex-grow max-w-7xl w-full mx-auto p-4 space-y-6">

        <section id="cinema-container" class="hidden bg-anime-dark rounded-xl border border-purple-950/40 overflow-hidden shadow-2xl">
            <div class="p-3 bg-black/40 border-b border-purple-950/20 flex items-center justify-between">
                <h2 id="cinema-title" class="font-bold text-xs sm:text-sm text-white truncate max-w-xs sm:max-w-xl">Streaming Source</h2>
                <button id="cinema-close" class="bg-purple-950/50 text-purple-300 text-xs px-2.5 py-1 rounded-md">✕ Close</button>
            </div>

            <div class="relative w-full aspect-video bg-black flex items-center justify-center">
                <video id="anime-player" class="absolute top-0 left-0 w-full h-full hidden" controls poster="">
                    <source id="video-source" src="" type="video/mp4">
                    <track src="eng-subtitles.vtt" kind="subtitles" srclang="en" label="English Subs" default>
                </video>
                <iframe id="cinema-frame" class="absolute top-0 left-0 w-full h-full" src="" frameborder="0" allowfullscreen allow="autoplay; encrypted-media"></iframe>
            </div>

            <div class="p-3 bg-black/20 flex flex-col gap-2">
                <span id="stream-status" class="text-[10px] font-bold text-purple-400 tracking-wider">IF VIDEO FAILS OR SHOWS ⚠️, TAP A DIFFERENT SERVER BELOW:</span>
                <div class="flex flex-wrap gap-2" id="server-pool"></div>
            </div>
        </section>

        <div>
            <h2 id="hub-header" class="text-base font-bold text-white uppercase border-l-4 border-purple-600 pl-2">Trending Stream Index</h2>
            <p id="hub-subheader" class="text-[11px] text-gray-400 mt-0.5">Images and streaming paths patched directly. Tap to resolve media engines instantly.</p>
        </div>

        <section id="anime-section" class="tab-content">
            <div id="master-grid" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 xl:grid-cols-6 gap-4"></div>
        </section>

        <section id="manga-section" class="tab-content hidden">
            <div id="manga-grid" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 xl:grid-cols-6 gap-4">
                <div class="col-span-full bg-anime-dark p-6 rounded-xl border border-purple-950/40 text-center space-y-4">
                    <p class="text-sm text-gray-300">Select a structural manga chapter item above to launch double-spread reading sheets.</p>
                    <div class="flex flex-col sm:flex-row justify-center gap-4 max-w-4xl mx-auto">
                        <div class="bg-black/40 p-2 rounded border border-purple-950/20"><img class="max-h-96 w-auto mx-auto object-contain rounded" src="https://images.unsplash.com/photo-1607604276583-eef5d076aa5f?w=500" alt="Left Page"></div>
                        <div class="bg-black/40 p-2 rounded border border-purple-950/20"><img class="max-h-96 w-auto mx-auto object-contain rounded" src="https://images.unsplash.com/photo-1560169897-fc0cdbdfa4d5?w=500" alt="Right Page"></div>
                    </div>
                </div>
            </div>
        </section>

    </main>

    <script>
        // DOM Elements Configuration Cache
        const masterGrid = document.getElementById('master-grid');
        const mangaGrid = document.getElementById('manga-grid');
        const hubHeader = document.getElementById('hub-header');
        const hubSubheader = document.getElementById('hub-subheader');
        const mainSearch = document.getElementById('main-search');
        const execSearch = document.getElementById('exec-search');
        
        const cinemaContainer = document.getElementById('cinema-container');
        const cinemaTitle = document.getElementById('cinema-title');
        const cinemaFrame = document.getElementById('cinema-frame');
        const cinemaClose = document.getElementById('cinema-close');
        const serverPool = document.getElementById('server-pool');

        const player = document.getElementById('anime-player');
        const statusText = document.getElementById('stream-status');

        let currentActiveTab = 'anime';

        // 1. Tab Switching Framework Logic
        function switchTab(tabName) {
            currentActiveTab = tabName;
            
            // Clean active classes out of sections
            document.getElementById('anime-section').classList.add('hidden');
            document.getElementById('manga-section').classList.add('hidden');
            document.getElementById('tab-btn-anime').className = "px-3 py-1 text-xs font-bold rounded-md transition text-gray-400 hover:text-white";
            document.getElementById('tab-btn-manga').className = "px-3 py-1 text-xs font-bold rounded-md transition text-gray-400 hover:text-white";

            // Establish active targets
            document.getElementById(`${tabName}-section`).classList.remove('hidden');
            document.getElementById(`tab-btn-${tabName}`).className = "px-3 py-1 text-xs font-bold rounded-md transition text-white bg-purple-600";

            // Update Headers automatically
            if (tabName === 'anime') {
                hubHeader.textContent = "Trending Stream Index";
                hubSubheader.textContent = "Images and streaming paths patched directly. Tap to resolve media engines instantly.";
                fetchCatalog('https://api.jikan.moe/v4/top/anime?limit=24', 'anime');
            } else {
                hubHeader.textContent = "Manga Reader Catalog";
                hubSubheader.textContent = "Explore trending volumes and standalone visual reading panes.";
                fetchCatalog('https://api.jikan.moe/v4/top/manga?limit=24', 'manga');
            }
        }

        // 2. Local Custom Language Logic Node
        if(player) {
            player.addEventListener('loadstart', () => {
                const currentSource = player.currentSrc;
                if (currentSource.includes('hindi')) {
                    statusText.innerText = "Playing: Hindi Dubbed";
                    if(player.textTracks[0]) player.textTracks[0].mode = 'disabled';
                } else {
                    statusText.innerText = "Hindi Dub unavailable. Playing: Japanese Audio + English Subtitles";
                    if(player.textTracks[0]) player.textTracks[0].mode = 'showing';
                }
            });
        }

        // 3. Application Data Loader Engine
        async function initApp() {
            fetchCatalog('https://api.jikan.moe/v4/top/anime?limit=24', 'anime');
        }

        async function fetchCatalog(url, type = 'anime') {
            const targetGrid = type === 'anime' ? masterGrid : mangaGrid;
            targetGrid.innerHTML = `<div class="col-span-full text-center py-20 text-xs text-purple-400 animate-pulse">Connecting to Clean Stream Mirrors...</div>`;
            
            try {
                const res = await fetch(url);
                const parsed = await res.json();
                renderCatalogGrid(parsed.data, type);
            } catch(e) {
                targetGrid.innerHTML = `<div class="col-span-full text-center py-20 text-xs text-red-400">Connection timeout. Try searching or refresh.</div>`;
            }
        }

        function renderCatalogGrid(data, type) {
            const targetGrid = type === 'anime' ? masterGrid : mangaGrid;
            targetGrid.innerHTML = '';
            
            if(!data || data.length === 0) {
                targetGrid.innerHTML = `<div class="col-span-full text-center py-20 text-xs text-gray-500">No stream links found.</div>`;
                return;
            }

            data.forEach(item => {
                const imagePath = item.images?.webp?.large_image_url || item.images?.jpg?.large_image_url || 'https://images.unsplash.com/photo-1578632767115-351597cf2477?w=500';
                
                const card = document.createElement('div');
                card.className = "bg-anime-card border border-purple-950/30 rounded-lg overflow-hidden group cursor-pointer hover:border-purple-500 transition duration-150 flex flex-col justify-between";
                
                card.innerHTML = `
                    <div class="relative aspect-[3/4] bg-black/40">
                        <img src="${imagePath}" alt="Poster" class="w-full h-full object-cover group-hover:scale-102 transition duration-200" onerror="this.src='https://images.unsplash.com/photo-1578632767115-351597cf2477?w=500'">
                        <span class="absolute top-1.5 left-1.5 bg-purple-600 text-white font-black text-[8px] px-1 rounded shadow">${type === 'anime' ? 'SUB/DUB' : 'MANGA'}</span>
                    </div>
                    <div class="p-2 space-y-1">
                        <h3 class="text-xs font-bold text-white line-clamp-2 group-hover:text-purple-400 transition">${item.title}</h3>
                        <div class="flex items-center justify-between text-[10px] text-gray-400 pt-1 border-t border-purple-950/30">
                            <span class="text-amber-500">⭐ ${item.score || '7.8'}</span>
                            <span>${item.type || 'TV'}</span>
                        </div>
                    </div>
                `;

                card.addEventListener('click', () => {
                    launchActiveStream(item.title, item.mal_id, type);
                });

                targetGrid.appendChild(card);
            });
        }

        // 4. Stream Resolution & Embedded Display Controller
        function launchActiveStream(title, malId, type) {
            cinemaTitle.textContent = type === 'anime' ? `Streaming: ${title}` : `Reading: ${title}`;
            
            // Reconstructed clean alternative mirrors
            const alternativeServers = type === 'anime' ? [
                { name: "🎬 Server 1 (Fast Sub)", url: `https://vidsrc.to/embed/anime/${malId}`, internal: false },
                { name: "🛰️ Server 2 (Backup Sub)", url: `https://vidsrc.me/embed/anime/${malId}`, internal: false },
                { name: "🇮🇳 Server 3 (Hindi Local File Mock)", url: `video-hindi-dub.mp4`, internal: true },
                { name: "📺 Server 4 (Trailer Alternative)", url: `https://vidsrc.xyz/embed/anime/${malId}`, internal: false }
            ] : [
                { name: "📖 Primary Chapter Engine", url: `https://manga-reader-mock-embed.org/id/${malId}`, internal: false }
            ];

            serverPool.innerHTML = '';
            alternativeServers.forEach((srv, index) => {
                const btn = document.createElement('button');
                btn.className = `px-3 py-1.5 text-xs font-bold rounded text-white transition ${index === 0 ? 'bg-purple-600' : 'bg-gray-800 hover:bg-gray-700'}`;
                btn.textContent = srv.name;
                
                btn.addEventListener('click', () => {
                    Array.from(serverPool.children).forEach(b => b.className = "px-3 py-1.5 text-xs font-bold rounded text-white bg-gray-800 hover:bg-gray-700 transition");
                    btn.className = "px-3 py-1.5 text-xs font-bold rounded text-white bg-purple-600 transition";
                    
                    toggleMediaPlayerSource(srv);
                });
                serverPool.appendChild(btn);
            });

            // Initialize default primary stream path layout choice
            toggleMediaPlayerSource(alternativeServers[0]);
            cinemaContainer.classList.remove('hidden');
            window.scrollTo({ top: cinemaContainer.offsetTop - 15, behavior: 'smooth' });
        }

        function toggleMediaPlayerSource(srv) {
            if (srv.internal) {
                // Hides iframe, switches processing over to your local video element tracking language logic
                cinemaFrame.classList.add('hidden');
                cinemaFrame.src = '';
                player.classList.remove('hidden');
                document.getElementById('video-source').src = srv.url;
                player.load();
            } else {
                // Default handling for Embeds
                player.classList.add('hidden');
                player.pause();
                cinemaFrame.classList.remove('hidden');
                cinemaFrame.src = srv.url;
                statusText.innerText = "IF VIDEO FAILS OR SHOWS ⚠️, TAP A DIFFERENT SERVER BELOW:";
            }
        }

        cinemaClose.addEventListener('click', () => {
            cinemaContainer.classList.add('hidden');
            cinemaFrame.src = '';
            player.pause();
            player.classList.add('hidden');
        });

        // 5. Query Handling Filter Engine
        async function runSearch() {
            const val = mainSearch.value.trim();
            if(!val) return;
            hubHeader.textContent = `Search Results for: "${val}"`;
            
            if(currentActiveTab === 'anime') {
                fetchCatalog(`https://api.jikan.moe/v4/anime?q=${encodeURIComponent(val)}&limit=24`, 'anime');
            } else {
                fetchCatalog(`https://api.jikan.moe/v4/manga?q=${encodeURIComponent(val)}&limit=24`, 'manga');
            }
        }

        execSearch.addEventListener('click', runSearch);
        mainSearch.addEventListener('keydown', (e) => { if(e.key === 'Enter') runSearch(); });

        // Fire Application Core
        initApp();
    </script>
</body>
</html>
