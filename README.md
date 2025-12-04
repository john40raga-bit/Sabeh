<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    <title>Sabeh Cafe & Games</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <style>
        body { font-family: 'Poppins', sans-serif; background-color: #f8f9fa; scroll-behavior: smooth; -webkit-tap-highlight-color: transparent; }
        
        /* Sidebar & General Styles */
        #sidebar { transition: transform 0.3s ease-in-out; transform: translateX(-100%); z-index: 50; }
        #sidebar.open { transform: translateX(0); }
        #overlay { transition: opacity 0.3s ease-in-out; z-index: 40; pointer-events: none; }
        #overlay.open { opacity: 0.5; pointer-events: auto; }
        .text-price { color: #5d4037; }
        section { scroll-margin-top: 80px; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }

        /* --- CHESS STYLES --- */
        .chess-board {
            display: grid; grid-template-columns: repeat(8, 1fr); grid-template-rows: repeat(8, 1fr);
            width: 100%; max-width: 400px; aspect-ratio: 1 / 1;
            border: 5px solid #5d4037; margin: 0 auto; user-select: none;
            background-color: #5d4037;
        }
        .square { width: 100%; height: 100%; display: flex; justify-content: center; align-items: center; font-size: 2.2rem; cursor: pointer; position: relative; outline: none; }
        @media (max-width: 400px) { .square { font-size: 1.6rem; } }
        .white-square { background-color: #f0d9b5; color: black; }
        .black-square { background-color: #b58863; color: black; }
        .selected { outline: 4px solid rgba(123,97,255,0.9); box-shadow: 0 0 12px rgba(123,97,255,0.25) inset; }
        .highlight::after { 
            content: ''; position: absolute; width: 30%; height: 30%; 
            background-color: rgba(0, 0, 0, 0.2); border-radius: 50%; pointer-events: none; 
        }
        .piece { z-index: 10; line-height: 1; pointer-events: none; font-weight: bold; }
        .white-piece { color: #fff; text-shadow: 0 1px 2px rgba(0,0,0,0.6); }
        .black-piece { color: #000; text-shadow: 0 1px 2px rgba(255,255,255,0.4); }

        .game-status {
            background: #fff;
            padding: 10px;
            border-radius: 8px;
            text-align: center;
            margin-bottom: 10px;
            font-weight: bold;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            border: 1px solid #e5e7eb;
        }

        /* check highlight */
        .king-in-check { box-shadow: inset 0 0 0 4px rgba(255,0,0,0.6); }

        /* captured trays */
        .captured-tray {
            display:flex; gap:6px; padding:6px; min-height:46px; align-items:center; justify-content:center;
            border-radius:8px; background:white; border:1px solid #e5e7eb;
        }
        .captured-tray .cap-piece { font-size:1.2rem; line-height:1; }
        
        /* layout in chess section */
        .chess-wrapper { display:flex; gap:16px; align-items:flex-start; justify-content:center; flex-wrap:wrap; }
        .chess-left { display:flex; flex-direction:column; gap:8px; align-items:center; }
        .capture-column { width:120px; display:flex; flex-direction:column; gap:8px; align-items:center; }
        .capture-label { font-size:12px; color:#555; font-weight:600; text-transform:uppercase; letter-spacing:0.06em; }
    </style>
</head>
<body class="bg-gray-50 text-gray-800">

    <div id="overlay" class="fixed inset-0 bg-black opacity-0" onclick="toggleMenu()"></div>

    <!-- Navigation -->
    <nav class="fixed top-0 w-full bg-white shadow-sm z-30 h-16 flex items-center px-4 border-b border-gray-200">
        <button onclick="toggleMenu()" class="p-2 mr-4 text-gray-600 focus:outline-none">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16"></path></svg>
        </button>
        <div class="flex items-center gap-2">
            <div class="text-orange-800 text-2xl">☕️</div> 
            <h1 class="text-xl font-bold text-gray-800 tracking-wide">Sabeh Cafe</h1>
        </div>
        <div class="ml-auto flex items-center gap-2">
             <span class="text-xs font-semibold text-gray-500 uppercase hidden sm:inline">Order</span>
             <div class="text-gray-800 font-bold border border-gray-800 rounded px-3 py-0.5 text-sm bg-orange-50">0</div>
        </div>
    </nav>

    <aside id="sidebar" class="fixed top-0 left-0 h-full bg-white shadow-2xl flex flex-col pt-20 w-[75vw] sm:w-64 border-r border-gray-100 overflow-y-auto no-scrollbar">
        <div class="px-6 pb-4 border-b border-gray-100">
            <h2 class="text-sm font-semibold text-gray-400 uppercase tracking-wider">Menu Categories</h2>
        </div>
        <ul class="flex-col w-full text-left mt-2 space-y-1">
             <li><a href="#breakfast" onclick="toggleMenu()" class="block px-6 py-3 hover:bg-orange-50 text-gray-700 font-medium border-l-4 border-transparent hover:border-orange-500 transition-all">🥞 Breakfast</a></li>
             <li><a href="#ethiopian" onclick="toggleMenu()" class="block px-6 py-3 hover:bg-orange-50 text-gray-700 font-medium border-l-4 border-transparent hover:border-orange-500 transition-all">🥘 Ethiopian</a></li>
             <li><a href="#burgers" onclick="toggleMenu()" class="block px-6 py-3 hover:bg-orange-50 text-gray-700 font-medium border-l-4 border-transparent hover:border-orange-500 transition-all">🍔 Burgers</a></li>
             <li><a href="#pizzas" onclick="toggleMenu()" class="block px-6 py-3 hover:bg-orange-50 text-gray-700 font-medium border-l-4 border-transparent hover:border-orange-500 transition-all">🍕 Pizzas</a></li>
             <li><a href="#wraps" onclick="toggleMenu()" class="block px-6 py-3 hover:bg-orange-50 text-gray-700 font-medium border-l-4 border-transparent hover:border-orange-500 transition-all">🌯 Wraps</a></li>
             <li><a href="#juices" onclick="toggleMenu()" class="block px-6 py-3 hover:bg-orange-50 text-gray-700 font-medium border-l-4 border-transparent hover:border-orange-500 transition-all">🍹 Fresh Juices</a></li>
             <li><a href="#hot-drinks" onclick="toggleMenu()" class="block px-6 py-3 hover:bg-orange-50 text-gray-700 font-medium border-l-4 border-transparent hover:border-orange-500 transition-all">☕️ Hot Drinks</a></li>
             <li><a href="#cold-drinks" onclick="toggleMenu()" class="block px-6 py-3 hover:bg-orange-50 text-gray-700 font-medium border-l-4 border-transparent hover:border-orange-500 transition-all">🍺 Cold Drinks</a></li>
             <li class="mt-4 border-t border-gray-100 pt-2 bg-purple-50">
                <span class="block px-6 py-2 text-xs font-bold text-purple-800 uppercase">Games Arcade</span>
                <a href="#chess-game" onclick="toggleMenu()" class="block px-6 py-2 text-purple-700 font-medium hover:bg-purple-100 border-l-4 border-transparent hover:border-purple-500 transition-all">♟ Play Chess</a>
             </li>
        </ul>
        <div class="mt-auto p-6 bg-gray-50">
            <p class="text-xs text-gray-400">© 2024 Sabeh Cafe</p>
        </div>
    </aside>

    <main class="container mx-auto px-3 py-4 mt-16 pb-20">
        <!-- Breakfast Section -->
        <section id="breakfast" class="mb-8">
            <h2 class="text-lg font-bold text-gray-800 mb-4 pl-1 border-l-4 border-orange-600">Breakfast</h2>
            <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-3 md:gap-6">
                <div class="bg-white rounded-lg shadow-sm overflow-hidden border border-gray-100">
                    <div class="relative h-32 md:h-48 w-full"><img src="https://images.unsplash.com/photo-1567620905732-2d1ec7ab7445?ixlib=rb-1.2.1&auto=format&fit=crop&w=500&q=80" class="w-full h-full object-cover"><div class="absolute bottom-0 left-0 w-full bg-black/50 p-2"><h3 class="text-white font-semibold text-sm">Pancake</h3></div></div><div class="p-3"><p class="text-price font-bold text-lg">200 Birr</p></div>
                </div>
                <div class="bg-white rounded-lg shadow-sm overflow-hidden border border-gray-100">
                    <div class="relative h-32 md:h-48 w-full"><img src="https://images.unsplash.com/photo-1512621776951-a57141f2eefd?q=80&w=500" class="w-full h-full object-cover"><div class="absolute bottom-0 left-0 w-full bg-black/50 p-2"><h3 class="text-white font-semibold text-sm">Special Ful</h3></div></div><div class="p-3"><p class="text-price font-bold text-lg">250 Birr</p></div>
                </div>
                 <div class="bg-white rounded-lg shadow-sm overflow-hidden border border-gray-100">
                    <div class="relative h-32 md:h-48 w-full"><img src="https://images.unsplash.com/photo-1565299624946-b28f40a0ae38?ixlib=rb-1.2.1&auto=format&fit=crop&w=500&q=80" class="w-full h-full object-cover"><div class="absolute bottom-0 left-0 w-full bg-black/50 p-2"><h3 class="text-white font-semibold text-sm">Chechebsa</h3></div></div><div class="p-3"><p class="text-price font-bold text-lg">200 Birr</p></div>
                </div>
                <div class="bg-white rounded-lg shadow-sm overflow-hidden border border-gray-100">
                    <div class="relative h-32 md:h-48 w-full"><img src="https://images.unsplash.com/photo-1596568359553-a56de6970068?ixlib=rb-1.2.1&auto=format&fit=crop&w=500&q=80" class="w-full h-full object-cover"><div class="absolute bottom-0 left-0 w-full bg-black/50 p-2"><h3 class="text-white font-semibold text-sm">Fetira</h3></div></div><div class="p-3"><p class="text-price font-bold text-lg">200 Birr</p></div>
                </div>
            </div>
        </section>

        <!-- Ethiopian Section -->
        <section id="ethiopian" class="mb-8">
            <h2 class="text-lg font-bold text-gray-800 mb-4 pl-1 border-l-4 border-orange-600">Ethiopian Traditional</h2>
            <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-3 md:gap-6">
                <div class="bg-white rounded-lg shadow-sm overflow-hidden border border-gray-100">
                    <div class="relative h-32 md:h-48 w-full">
                        <!-- Updated Beyaynetu image: replace with the uploaded image saved as bayaynetu.jpg -->
                        <img src="bayaynetu.jpg" class="w-full h-full object-cover" alt="Beyaynetu">
                        <div class="absolute bottom-0 left-0 w-full bg-black/50 p-2"><h3 class="text-white font-semibold text-sm">Beyaynetu</h3></div>
                    </div>
                    <div class="p-3"><p class="text-price font-bold text-lg">250 Birr</p></div>
                </div>
                 <div class="bg-white rounded-lg shadow-sm overflow-hidden border border-gray-100">
                    <div class="relative h-32 md:h-48 w-full"><img src="https://images.unsplash.com/photo-1543339308-43e59d6b73a6?ixlib=rb-1.2.1&auto=format&fit=crop&w=500&q=80" class="w-full h-full object-cover"><div class="absolute bottom-0 left-0 w-full bg-black/50 p-2"><h3 class="text-white font-semibold text-sm">Chqna Tibs</h3></div></div><div class="p-3"><p class="text-price font-bold text-lg">500 Birr</p></div>
                </div>
                <div class="bg-white rounded-lg shadow-sm overflow-hidden border border-gray-100">
                    <div class="relative h-32 md:h-48 w-full"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/e0/Kitfo.jpg/800px-Kitfo.jpg" class="w-full h-full object-cover"><div class="absolute bottom-0 left-0 w-full bg-black/50 p-2"><h3 class="text-white font-semibold text-sm">Special Kitfo</h3></div></div><div class="p-3"><p class="text-price font-bold text-lg">550 Birr</p></div>
                </div>
            </div>
        </section>

        <!-- Burgers Section -->
        <section id="burgers" class="mb-8">
            <h2 class="text-lg font-bold text-gray-800 mb-4 pl-1 border-l-4 border-orange-600">Burgers</h2>
            <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-3 md:gap-6">
                <div class="bg-white rounded-lg shadow-sm overflow-hidden border border-gray-100">
                    <div class="relative h-32 md:h-48 w-full"><img src="https://images.unsplash.com/photo-1568901346375-23c9450c58cd?q=80&w=500" class="w-full h-full object-cover"><div class="absolute bottom-0 left-0 w-full bg-black/50 p-2"><h3 class="text-white font-semibold text-sm">Sabeh Special</h3></div></div><div class="p-3"><p class="text-price font-bold text-lg">350 Birr</p></div>
                </div>
                <div class="bg-white rounded-lg shadow-sm overflow-hidden border border-gray-100">
                    <div class="relative h-32 md:h-48 w-full"><img src="https://images.unsplash.com/photo-1550547660-d9450f859349?q=80&w=500" class="w-full h-full object-cover"><div class="absolute bottom-0 left-0 w-full bg-black/50 p-2"><h3 class="text-white font-semibold text-sm">Cheeseburger</h3></div></div><div class="p-3"><p class="text-price font-bold text-lg">300 Birr</p></div>
                </div>
                 <div class="bg-white rounded-lg shadow-sm overflow-hidden border border-gray-100">
                    <div class="relative h-32 md:h-48 w-full"><img src="https://images.unsplash.com/photo-1615297928064-24977384d0f9?q=80&w=500" class="w-full h-full object-cover"><div class="absolute bottom-0 left-0 w-full bg-black/50 p-2"><h3 class="text-white font-semibold text-sm">Chicken Burger</h3></div></div><div class="p-3"><p class="text-price font-bold text-lg">320 Birr</p></div>
                </div>
            </div>
        </section>

        <!-- CHESS SECTION (FULL FUNCTIONAL CHESS) -->
        <section id="chess-game" class="mb-12 border-t-2 border-purple-100 pt-8 mt-12">
            <h2 class="text-lg font-bold text-gray-800 mb-4 pl-1 border-l-4 border-purple-600">Play Chess</h2>
            <div class="chess-wrapper">
                <div class="capture-column">
                    <div class="capture-label">Black Captured</div>
                    <div id="captured-black" class="captured-tray" aria-live="polite"></div>
                </div>

                <div class="chess-left">
                    <div id="status" class="game-status w-full max-w-[400px]">White to move</div>
                    <div id="chessboard" class="chess-board" role="grid" aria-label="Chessboard"></div>
                    <div class="flex gap-2">
                        <button onclick="resetGame()" class="mt-4 px-6 py-2 bg-purple-600 text-white rounded shadow hover:bg-purple-700 font-semibold uppercase text-sm tracking-wide">Restart Chess</button>
                        <button onclick="undoMove()" class="mt-4 px-6 py-2 bg-gray-200 text-gray-800 rounded shadow hover:bg-gray-300 font-semibold uppercase text-sm tracking-wide">Undo</button>
                    </div>
                </div>

                <div class="capture-column">
                    <div class="capture-label">White Captured</div>
                    <div id="captured-white" class="captured-tray" aria-live="polite"></div>
                </div>
            </div>

            <p class="text-xs text-gray-500 mt-4 text-center max-w-xs mx-auto">Select a piece, then tap a highlighted square to move. Promotion auto-queens; castling and en-passant supported. You can also focus a square and press Enter/Space to select/move.</p>
        </section>

    </main>

    <script>
        function toggleMenu() {
            document.getElementById('sidebar').classList.toggle('open');
            document.getElementById('overlay').classList.toggle('open');
        }

        /* -------------------------
           FULL CHESS IMPLEMENTATION
           Features:
            - Full move rules (including en-passant & castling)
            - Legal move generation (filters moves that leave own king in check)
            - Check detection, checkmate & stalemate
            - Highlights legal moves, selected square, king-in-check highlight
            - Captured pieces shown in side trays (separate for white/black)
            - Undo (single-step)
            - Keyboard access to squares (tab to focus, Enter/Space to activate)
           ------------------------- */

        // Board representation: 8x8 array. Each cell is either null or object {type:'p','r','n','b','q','k', color:'w'|'b'}
        const boardEl = document.getElementById('chessboard');
        const statusEl = document.getElementById('status');
        const capturedWhiteEl = document.getElementById('captured-white');
        const capturedBlackEl = document.getElementById('captured-black');

        // separate glyph maps so white pieces can use white-piece glyphs where appropriate
        const UNICODE_WHITE = { 'p': '♙', 'r': '♖', 'n': '♘', 'b': '♗', 'q': '♕', 'k': '♔' };
        const UNICODE_BLACK = { 'p': '♟', 'r': '♜', 'n': '♞', 'b': '♝', 'q': '♛', 'k': '♚' };

        let state = {
            board: [],               // 8x8
            turn: 'w',              // 'w' or 'b'
            selected: null,         // {r,c}
            enPassant: null,        // {r,c} square behind a double pawn which can be captured, or null
            castle: { wK: true, wQ: true, bK: true, bQ: true }, // castling rights
            halfmoveClock: 0,
            fullmoveNumber: 1,
            capturedWhite: [],      // pieces captured belonging to white
            capturedBlack: [],      // pieces captured belonging to black
            history: []             // for undo (stores shallow clones of important fields)
        };

        // initialize standard starting position
        function initBoard() {
            const emptyRow = () => Array(8).fill(null);
            const b = [
                [ {type:'r',color:'b'}, {type:'n',color:'b'}, {type:'b',color:'b'}, {type:'q',color:'b'}, {type:'k',color:'b'}, {type:'b',color:'b'}, {type:'n',color:'b'}, {type:'r',color:'b'} ],
                Array(8).fill({type:'p',color:'b'}),
                ...Array(4).fill(emptyRow()),
                Array(8).fill({type:'p',color:'w'}),
                [ {type:'r',color:'w'}, {type:'n',color:'w'}, {type:'b',color:'w'}, {type:'q',color:'w'}, {type:'k',color:'w'}, {type:'b',color:'w'}, {type:'n',color:'w'}, {type:'r',color:'w'} ]
            ];
            // Deep copy to avoid shared objects
            state.board = b.map(row => row.map(cell => cell ? {...cell} : null));
            state.turn = 'w';
            state.selected = null;
            state.enPassant = null;
            state.castle = { wK:true, wQ:true, bK:true, bQ:true };
            state.halfmoveClock = 0;
            state.fullmoveNumber = 1;
            state.capturedWhite = [];
            state.capturedBlack = [];
            state.history = [];
            render();
        }

        // Helper: inside board
        function inside(r,c){ return r>=0 && r<8 && c>=0 && c<8; }

        // Clone basic state for history (undo)
        function snapshot() {
            return {
                board: state.board.map(row => row.map(cell => cell ? {...cell} : null)),
                turn: state.turn,
                enPassant: state.enPassant ? {...state.enPassant} : null,
                castle: {...state.castle},
                halfmoveClock: state.halfmoveClock,
                fullmoveNumber: state.fullmoveNumber,
                capturedWhite: [...state.capturedWhite],
                capturedBlack: [...state.capturedBlack]
            };
        }

        // restore snapshot
        function restoreSnap(snap) {
            state.board = snap.board.map(row => row.map(cell => cell ? {...cell} : null));
            state.turn = snap.turn;
            state.enPassant = snap.enPassant ? {...snap.enPassant} : null;
            state.castle = {...snap.castle};
            state.halfmoveClock = snap.halfmoveClock;
            state.fullmoveNumber = snap.fullmoveNumber;
            state.capturedWhite = [...snap.capturedWhite];
            state.capturedBlack = [...snap.capturedBlack];
        }

        // render board & UI
        function render() {
            // clear board
            boardEl.innerHTML = '';
            // build squares
            for(let r=0; r<8; r++){
                for(let c=0; c<8; c++){
                    const sq = document.createElement('div');
                    sq.className = `square ${((r+c)%2===0)?'white-square':'black-square'}`;
                    sq.dataset.r = r; sq.dataset.c = c;
                    sq.setAttribute('role','button');
                    sq.setAttribute('tabindex','0'); // make focusable for keyboard
                    const coord = `${String.fromCharCode(97+c)}${8-r}`;
                    sq.setAttribute('aria-label', `square ${coord}`);
                    sq.setAttribute('title', coord);
                    const piece = state.board[r][c];
                    if(piece){
                        const span = document.createElement('span');
                        span.className = 'piece ' + (piece.color === 'w' ? 'white-piece' : 'black-piece');
                        span.textContent = piece.color === 'w' ? UNICODE_WHITE[piece.type] : UNICODE_BLACK[piece.type];
                        sq.appendChild(span);
                    }
                    // selection & highlights applied later
                    sq.onclick = () => handleSquareTap(r,c);
                    // keyboard activation (Enter / Space)
                    sq.onkeydown = (e) => {
                        if(e.key === 'Enter' || e.key === ' ') {
                            e.preventDefault();
                            handleSquareTap(r,c);
                        }
                    };
                    boardEl.appendChild(sq);
                }
            }

            // highlight selection/legal moves
            clearHighlights();
            if(state.selected){
                const {r,c} = state.selected;
                const sqIndex = (r*8 + c);
                const selSquare = boardEl.children[sqIndex];
                if(selSquare) selSquare.classList.add('selected');
                // generate legal moves
                const legal = generateLegalMoves(r,c);
                legal.forEach(m => {
                    const idx = m.r*8 + m.c;
                    if(boardEl.children[idx]) boardEl.children[idx].classList.add('highlight');
                });
            }

            // king check highlight (if the side to move's king is in check)
            const inCheckFor = isKingInCheck(state.turn);
            if(inCheckFor) {
                const kpos = findKing(state.turn);
                if(kpos) {
                    const idx = kpos.r*8 + kpos.c;
                    if(boardEl.children[idx]) boardEl.children[idx].classList.add('king-in-check');
                }
            }

            // update status text
            statusEl.innerText = `${state.turn === 'w' ? "White" : "Black"} to move` + (inCheckFor ? " — CHECK!" : "");

            // render captured
            capturedWhiteEl.innerHTML = '';
            capturedBlackEl.innerHTML = '';
            state.capturedWhite.forEach(p => {
                const s = document.createElement('div'); s.className='cap-piece'; s.textContent = (p.color === 'w' ? UNICODE_WHITE[p.type] : UNICODE_BLACK[p.type]); capturedWhiteEl.appendChild(s);
            });
            state.capturedBlack.forEach(p => {
                const s = document.createElement('div'); s.className='cap-piece'; s.textContent = (p.color === 'w' ? UNICODE_WHITE[p.type] : UNICODE_BLACK[p.type]); capturedBlackEl.appendChild(s);
            });

            // after rendering, check for checkmate or stalemate
            setTimeout(()=> {
                const mate = isCheckmate(state.turn);
                const stal = isStalemate(state.turn);
                if(mate) {
                    statusEl.innerText = `${state.turn === 'w' ? "White" : "Black"} is checkmated. ${state.turn === 'w' ? "Black" : "White"} wins!`;
                } else if(stal) {
                    statusEl.innerText = `Stalemate — Draw`;
                }
            }, 10);
        }

        function clearHighlights() {
            Array.from(boardEl.children).forEach(ch => {
                ch.classList.remove('highlight','selected','king-in-check');
            });
        }

        // locate king of color
        function findKing(color) {
            for(let r=0;r<8;r++) for(let c=0;c<8;c++) {
                const p = state.board[r][c];
                if(p && p.type==='k' && p.color===color) return {r,c};
            }
            return null;
        }

        // is given color's king in check? returns true if color's king is attacked by opponent
        function isKingInCheck(color) {
            const kingPos = findKing(color);
            if(!kingPos) return false;
            return isSquareAttacked(kingPos.r, kingPos.c, color==='w' ? 'b' : 'w');
        }

        // is square (r,c) attacked by color attacker?
        function isSquareAttacked(r,c,attackerColor) {
            // iterate all opponent pieces and check if they could move to (r,c) ignoring pins & leaving king
            for(let rr=0; rr<8; rr++){
                for(let cc=0; cc<8; cc++){
                    const p = state.board[rr][cc];
                    if(!p || p.color !== attackerColor) continue;
                    const moves = pseudoMovesForPiece(rr,cc,p,true); // attack mode true (pawn captures differently)
                    for(const m of moves){
                        if(m.r===r && m.c===c) return true;
                    }
                }
            }
            return false;
        }

        // PSEUDO moves generation (not considering leaving own king in check) 
        // attackOnly flag: for pawns, generate only capture moves when used for attack detection
        function pseudoMovesForPiece(r,c,piece, attackOnly=false) {
            const moves = [];
            const color = piece.color;
            const dir = (color==='w') ? -1 : 1;

            if(piece.type === 'p'){
                // pawn moves
                const startRow = (color==='w') ? 6 : 1;
                // captures
                const caps = [ [r+dir, c-1], [r+dir, c+1] ];
                caps.forEach(([rr,cc])=>{
                    if(inside(rr,cc)){
                        // normal capture
                        if(state.board[rr][cc] && state.board[rr][cc].color !== color) moves.push({r:rr,c:cc});
                        // en-passant (attacking the empty square if matches enPassant target)
                        if(state.enPassant && state.enPassant.r === rr && state.enPassant.c === cc) {
                            moves.push({r:rr,c:cc, enPassant:true});
                        }
                    }
                });
                if(!attackOnly){
                    // forward one
                    const f1r = r+dir;
                    if(inside(f1r,c) && !state.board[f1r][c]) {
                        moves.push({r:f1r,c:c});
                        // forward two from starting
                        const f2r = r+2*dir;
                        if(r === startRow && !state.board[f2r][c]) moves.push({r:f2r,c:c, double:true});
                    }
                }
                return moves;
            }

            if(piece.type === 'n'){
                const deltas = [[-2,-1],[-2,1],[-1,-2],[-1,2],[1,-2],[1,2],[2,-1],[2,1]];
                deltas.forEach(d=>{
                    const rr = r+d[0], cc = c+d[1];
                    if(!inside(rr,cc)) return;
                    const target = state.board[rr][cc];
                    if(!target || target.color !== color) moves.push({r:rr,c:cc});
                });
                return moves;
            }

            if(piece.type === 'b' || piece.type === 'r' || piece.type === 'q'){
                const directions = [];
                if(piece.type==='b' || piece.type==='q') directions.push([-1,-1],[-1,1],[1,-1],[1,1]);
                if(piece.type==='r' || piece.type==='q') directions.push([-1,0],[1,0],[0,-1],[0,1]);
                for(const d of directions){
                    let rr = r + d[0], cc = c + d[1];
                    while(inside(rr,cc)){
                        const target = state.board[rr][cc];
                        if(!target){ moves.push({r:rr,c:cc}); rr += d[0]; cc += d[1]; continue; }
                        if(target.color !== color) moves.push({r:rr,c:cc});
                        break;
                    }
                }
                return moves;
            }

            if(piece.type === 'k'){
                const deltas = [[-1,-1],[-1,0],[-1,1],[0,-1],[0,1],[1,-1],[1,0],[1,1]];
                deltas.forEach(d=>{
                    const rr = r+d[0], cc = c+d[1];
                    if(!inside(rr,cc)) return;
                    const target = state.board[rr][cc];
                    if(!target || target.color !== color) moves.push({r:rr,c:cc});
                });
                // castling - only as pseudo moves; leave legality check to filter
                if(!attackOnly){
                    if(color==='w'){
                        if(state.castle.wK && !state.board[7][5] && !state.board[7][6]) moves.push({r:7,c:6,castle:'K'});
                        if(state.castle.wQ && !state.board[7][1] && !state.board[7][2] && !state.board[7][3]) moves.push({r:7,c:2,castle:'Q'});
                    } else {
                        if(state.castle.bK && !state.board[0][5] && !state.board[0][6]) moves.push({r:0,c:6,castle:'K'});
                        if(state.castle.bQ && !state.board[0][1] && !state.board[0][2] && !state.board[0][3]) moves.push({r:0,c:2,castle:'Q'});
                    }
                }
                return moves;
            }

            return moves;
        }

        // generate legal moves for piece at (r,c) - filters out moves leaving own king in check
        function generateLegalMoves(r,c) {
            const piece = state.board[r][c];
            if(!piece) return [];
            if(piece.color !== state.turn) return [];

            const pseudo = pseudoMovesForPiece(r,c,piece,false);
            const legal = [];
            for(const m of pseudo){
                // create snapshot, play move, test king safety
                const snap = snapshot();
                const captured = makeMoveInternal(r,c,m.r,m.c,m);
                const kingInCheck = isKingInCheck(piece.color);
                // undo
                restoreSnap(snap);
                // if doesn't leave king in check, allowed
                if(!kingInCheck) legal.push(m);
            }
            return legal;
        }

        // makeMoveInternal executes move without saving history; returns captured piece or null. Supports castling and en-passant and promotion (auto-queen).
        function makeMoveInternal(r1,c1,r2,c2, moveMeta={}) {
            const p = state.board[r1][c1];
            if(!p) return null;
            const target = state.board[r2][c2];
            let captured = null;

            // en-passant detection: if pawn and moving to enPassant square and square is empty, capture the pawn behind
            if(p.type === 'p' && state.enPassant && state.enPassant.r === r2 && state.enPassant.c === c2 && !target){
                // the pawn captured is behind the enPassant square (r1, c2)
                const capR = r1;
                const capC = c2;
                captured = state.board[capR][capC];
                state.board[capR][capC] = null;
            } else {
                if(target) captured = {...target};
            }

            // handle castling: if king moves two squares horizontally
            if(p.type === 'k' && Math.abs(c2 - c1) === 2){
                // King side castle
                if(c2 === 6){
                    // move rook from h-file to f-file
                    const rookR = r1, rookC = 7;
                    state.board[r1][5] = state.board[rookR][rookC];
                    state.board[rookR][rookC] = null;
                } else if(c2 === 2){
                    // queen side: move rook from a-file to d-file
                    const rookR = r1, rookC = 0;
                    state.board[r1][3] = state.board[rookR][rookC];
                    state.board[rookR][rookC] = null;
                }
            }

            // move piece
            state.board[r2][c2] = state.board[r1][c1];
            state.board[r1][c1] = null;

            // promotion: if pawn reaches last rank auto-queen
            if(state.board[r2][c2].type === 'p'){
                if(state.board[r2][c2].color === 'w' && r2 === 0){
                    state.board[r2][c2].type = 'q';
                } else if(state.board[r2][c2].color === 'b' && r2 === 7){
                    state.board[r2][c2].type = 'q';
                }
            }

            // update enPassant: reset unless double pawn move
            if(p.type === 'p' && Math.abs(r2 - r1) === 2){
                state.enPassant = { r: (r1 + r2)/2, c: c1 };
            } else {
                state.enPassant = null;
            }

            // update castling rights if king or rook moved or rook captured
            // King moved:
            if(p.type === 'k'){
                if(p.color === 'w'){ state.castle.wK = false; state.castle.wQ = false; }
                else { state.castle.bK = false; state.castle.bQ = false; }
            }
            // Rook moved or captured adjust rights
            // identify rooks initial positions: white rooks at (7,0) and (7,7), black (0,0),(0,7)
            // If rook moved from starting square, remove that side's right
            if(p.type === 'r'){
                if(r1===7 && c1===0) state.castle.wQ = false;
                if(r1===7 && c1===7) state.castle.wK = false;
                if(r1===0 && c1===0) state.castle.bQ = false;
                if(r1===0 && c1===7) state.castle.bK = false;
            }
            if(captured && captured.type === 'r'){
                if(r2===7 && c2===0) state.castle.wQ = false;
                if(r2===7 && c2===7) state.castle.wK = false;
                if(r2===0 && c2===0) state.castle.bQ = false;
                if(r2===0 && c2===7) state.castle.bK = false;
            }

            return captured;
        }

        // execute a move with history push; update captured trays and state variables
        function makeMove(r1,c1,r2,c2, meta={}) {
            const snap = snapshot();
            state.history.push(snap);
            const movingPiece = state.board[r1][c1];
            const captured = makeMoveInternal(r1,c1,r2,c2,meta);
            // update captured arrays
            if(captured){
                if(captured.color === 'w') state.capturedWhite.push(captured);
                else state.capturedBlack.push(captured);
            }
            // update turn and counters
            if(movingPiece.type === 'p' || captured) state.halfmoveClock = 0; else state.halfmoveClock++;
            if(state.turn === 'b') state.fullmoveNumber++;
            state.turn = (state.turn === 'w') ? 'b' : 'w';

            render();
        }

        // undo last move
        function undoMove(){
            if(state.history.length === 0) return;
            const snap = state.history.pop();
            restoreSnap(snap);
            render();
        }

        // handle tap on square
        function handleSquareTap(r,c) {
            const clicked = state.board[r][c];
            // if a piece selected and tapping one of its legal moves -> move
            if(state.selected){
                const sel = state.selected;
                const legal = generateLegalMoves(sel.r, sel.c);
                const match = legal.find(m => m.r === r && m.c === c);
                if(match){
                    makeMove(sel.r, sel.c, r, c, match);
                    state.selected = null;
                    return;
                }
            }

            // otherwise, if tapped own piece -> select it
            if(clicked && clicked.color === state.turn){
                // only allow selecting if it has at least one legal move
                const lst = generateLegalMoves(r,c);
                if(lst.length > 0){
                    state.selected = {r,c};
                } else {
                    state.selected = null;
                }
                render();
                return;
            }

            // tapped empty or opponent piece but not a legal destination -> clear selection
            state.selected = null;
            render();
        }

        // check if player has any legal moves
        function hasAnyLegalMoves(color) {
            for(let r=0;r<8;r++){
                for(let c=0;c<8;c++){
                    const p = state.board[r][c];
                    if(!p || p.color !== color) continue;
                    const legal = generateLegalMoves(r,c);
                    if(legal.length > 0) return true;
                }
            }
            return false;
        }

        // checkmate: color's turn and in check and no legal moves
        function isCheckmate(color) {
            if(!isKingInCheck(color)) return false;
            return !hasAnyLegalMoves(color);
        }
        // stalemate: not in check and no legal moves
        function isStalemate(color) {
            if(isKingInCheck(color)) return false;
            return !hasAnyLegalMoves(color);
        }

        /* ===========================
           Initialize and bind UI
           =========================== */
        function resetGame() { initBoard(); }
        // Undo button already bound in markup 

        // start game
        initBoard();

        // Expose functions for console debugging if needed
        window._chessState = state;
        window.resetGame = resetGame;

    </script>
</body>
</html>
