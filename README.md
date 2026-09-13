<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Steve's Kitchen Live Bingo</title>
<style>
  :root {
    --bg: #1a1a2e;
    --card: #16213e;
    --accent: #e94560;
    --gold: #f0c14b;
    --text: #eee;
    --muted: #aaa;
    --called: #0f3460;
    --dab: #e94560;
    --bonus: #9b59b6;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    font-family: 'Segoe UI', system-ui, sans-serif;
    background: linear-gradient(135deg, #1a1a2e 0%, #0f3460 100%);
    color: var(--text);
    min-height: 100vh;
    padding: 20px;
  }
  .container { max-width: 1100px; margin: 0 auto; }
  h1, h2, h3 { text-align: center; margin-bottom: 12px; }
  h1 { color: var(--gold); font-size: 2.1rem; text-shadow: 0 0 10px rgba(240,193,75,0.4); }
  .card {
    background: var(--card);
    border-radius: 16px;
    padding: 22px;
    box-shadow: 0 10px 30px rgba(0,0,0,0.4);
    margin-bottom: 18px;
  }
  button {
    background: var(--accent);
    color: white;
    border: none;
    padding: 11px 20px;
    border-radius: 8px;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: transform 0.15s, background 0.2s;
  }
  button:hover { transform: translateY(-2px); background: #ff6b81; }
  button:disabled { opacity: 0.45; cursor: not-allowed; transform: none; }
  button.secondary { background: #555; }
  button.bonus-btn { background: var(--bonus); }
  button.success { background: #27ae60; }
  input {
    padding: 11px 14px;
    border-radius: 8px;
    border: 2px solid #333;
    background: #0f0f23;
    color: white;
    font-size: 1rem;
    width: 100%;
    max-width: 280px;
  }
  .hidden { display: none !important; }

  .home-btns { display: flex; gap: 18px; justify-content: center; flex-wrap: wrap; margin-top: 28px; }

  .number-board {
    display: grid;
    grid-template-columns: repeat(10, 1fr);
    gap: 5px;
    margin-top: 14px;
  }
  .num-cell {
    aspect-ratio: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #0f0f23;
    border-radius: 6px;
    font-weight: 700;
    font-size: 0.9rem;
  }
  .num-cell.called { background: var(--called); color: var(--gold); }
  .num-cell.bonus { outline: 3px solid var(--bonus); }

  .big-number {
    font-size: 4.6rem;
    font-weight: 900;
    color: var(--gold);
    text-align: center;
    line-height: 1;
    margin: 10px 0;
    text-shadow: 0 0 18px rgba(240,193,75,0.5);
  }
  .call-phrase {
    text-align: center;
    font-size: 1.25rem;
    color: var(--accent);
    margin-bottom: 12px;
    min-height: 1.4em;
  }

  .bingo-grid {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 7px;
    max-width: 400px;
    margin: 16px auto;
  }
  .bingo-cell {
    aspect-ratio: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #0f0f23;
    border: 3px solid #333;
    border-radius: 10px;
    font-size: 1.35rem;
    font-weight: 700;
    cursor: pointer;
    user-select: none;
    transition: all 0.15s;
  }
  .bingo-cell.dabbed {
    background: var(--dab);
    border-color: var(--gold);
    color: white;
    transform: scale(0.95);
  }
  .bingo-cell.auto-marked {
    background: #2ecc71;
    border-color: #27ae60;
  }

  .leaderboard {
    background: #0f0f23;
    border-radius: 12px;
    padding: 14px;
    max-height: 260px;
    overflow-y: auto;
  }
  .leaderboard table { width: 100%; border-collapse: collapse; }
  .leaderboard th, .leaderboard td {
    padding: 8px 10px;
    text-align: left;
    border-bottom: 1px solid #333;
  }
  .leaderboard th { color: var(--gold); font-size: 0.9rem; }
  .leaderboard tr:last-child td { border-bottom: none; }
  .leaderboard .rank { color: var(--muted); width: 30px; }
  .leaderboard .pts { color: var(--gold); font-weight: 700; text-align: right; }

  .bonus-badge {
    display: inline-block;
    background: var(--bonus);
    color: white;
    padding: 6px 14px;
    border-radius: 20px;
    font-weight: 700;
    font-size: 1.1rem;
    margin: 8px 0;
  }

  .bingo-alert {
    background: #27ae60;
    color: white;
    padding: 13px;
    border-radius: 10px;
    text-align: center;
    font-size: 1.2rem;
    font-weight: 700;
    margin: 12px 0;
    animation: pulse 1.5s infinite;
  }
  @keyframes pulse {
    0%, 100% { transform: scale(1); }
    50% { transform: scale(1.03); }
  }

  /* New-round prompt */
  .new-round-prompt {
    background: #2c3e50;
    border: 3px solid var(--gold);
    border-radius: 14px;
    padding: 20px;
    margin: 18px 0;
    text-align: center;
  }
  .new-round-prompt h3 { color: var(--gold); margin-bottom: 10px; }
  .new-round-prompt .btns { display: flex; gap: 12px; justify-content: center; flex-wrap: wrap; margin-top: 14px; }

  .center { text-align: center; }
  .mt { margin-top: 14px; }
  .mb { margin-bottom: 12px; }
  .status { text-align: center; font-size: 1.05rem; color: var(--gold); min-height: 1.4em; }
  .login-box { max-width: 360px; margin: 40px auto; }
  label { display: block; margin-bottom: 5px; color: var(--muted); }
  .form-group { margin-bottom: 16px; }
  .flex { display: flex; gap: 16px; flex-wrap: wrap; }
  .flex > * { flex: 1; min-width: 260px; }
  .round-locked { opacity: 0.55; pointer-events: none; }
</style>
</head>
<body>
<div class="container">

  <!-- HOME -->
  <div id="homePage">
    <div class="card center">
      <h1>🍳 Steve's Kitchen Live Bingo</h1>
      <p style="color:var(--muted); margin:12px 0 22px;">Fun family bingo – not for gambling</p>
      <div class="home-btns">
        <button onclick="showPlayer()">Play as Player</button>
        <button onclick="showLogin()" class="secondary">Caller Login</button>
      </div>
    </div>
  </div>

  <!-- LOGIN -->
  <div id="loginPage" class="hidden">
    <div class="card login-box">
      <h2>Caller Login</h2>
      <div class="form-group">
        <label>Username</label>
        <input type="text" id="loginUser" autocomplete="off">
      </div>
      <div class="form-group">
        <label>Password</label>
        <input type="password" id="loginPass">
      </div>
      <button onclick="doLogin()" style="width:100%;">Log In</button>
      <p id="loginError" class="center mt" style="color:var(--accent);"></p>
      <p class="center mt"><a href="#" onclick="showHome()" style="color:var(--muted);">← Back</a></p>
    </div>
  </div>

  <!-- CALLER -->
  <div id="callerPage" class="hidden">
    <div class="card">
      <div style="display:flex;justify-content:space-between;align-items:center;flex-wrap:wrap;gap:10px;">
        <h2>Caller Desk – Steve</h2>
        <button onclick="logout()" class="secondary" style="padding:8px 14px;">Logout</button>
      </div>

      <div class="center">
        <div class="big-number" id="callerBigNum">–</div>
        <div class="call-phrase" id="callPhrase">Press “Call Next Number”</div>

        <div class="mt">
          <span style="color:var(--muted);">Bonus Number this round:</span><br>
          <span class="bonus-badge" id="bonusDisplay">–</span>
        </div>

        <div class="mt">
          <button id="callBtn" onclick="callNextNumber()">Call Next Number</button>
          <button onclick="startNewRound()" class="secondary" style="margin-left:8px;">New Round (new bonus)</button>
          <button onclick="fullReset()" class="secondary" style="margin-left:8px;">Full Reset</button>
        </div>
      </div>

      <div id="bingoClaims" class="mt"></div>

      <h3 class="mt">Called Numbers Board (1–90)</h3>
      <div class="number-board" id="numberBoard"></div>
    </div>

    <div class="flex">
      <div class="card">
        <h3>Check Player Card</h3>
        <p style="color:var(--muted);margin-bottom:10px;font-size:0.95rem;">
          Search a name → card is auto-marked. Confirm Bingo to award points.
        </p>
        <div style="display:flex;gap:8px;flex-wrap:wrap;">
          <input type="text" id="searchName" placeholder="Player name">
          <button onclick="searchPlayer()">Show Card</button>
        </div>
        <div id="playerCheckArea" class="mt"></div>
      </div>

      <div class="card">
        <h3>🏆 Leaderboard</h3>
        <div class="leaderboard" id="leaderboardCaller"></div>
      </div>
    </div>
  </div>

  <!-- PLAYER -->
  <div id="playerPage" class="hidden">
    <div class="card center">
      <h2>Your Bingo Card</h2>

      <!-- New-round decision prompt -->
      <div id="newRoundPrompt" class="new-round-prompt hidden">
        <h3>🔄 New Round Started!</h3>
        <p>Do you want to stay on the same name and keep your current card?</p>
        <div class="btns">
          <button class="success" onclick="keepSameCard()">Yes – Keep same name & card</button>
          <button class="secondary" onclick="getNewCardAfterRound()">No – Get a new card</button>
        </div>
      </div>

      <div id="playerSetup">
        <p class="mb">Enter your name – a brand new random 15-number card will be generated.</p>
        <input type="text" id="playerName" placeholder="Your name" maxlength="20">
        <button class="mt" id="getCardBtn" onclick="createPlayerCard()">Get My Card</button>
        <p id="roundLockedMsg" class="mt hidden" style="color:var(--accent);">
          A round is in progress – you cannot get a new card until the next round starts.
        </p>
      </div>

      <div id="playerGame" class="hidden">
        <p>Playing as: <strong id="displayName"></strong></p>

        <div class="big-number" id="playerBigNum">–</div>
        <div class="call-phrase" id="playerPhrase">Waiting for first number…</div>

        <div class="mt">
          <span style="color:var(--muted);">Bonus Number:</span>
          <span class="bonus-badge" id="playerBonus">–</span>
        </div>

        <div class="bingo-grid" id="playerGrid"></div>

        <button id="bingoBtn" onclick="claimBingo()" class="mt">🎉 BINGO!</button>
        <p class="status mt" id="playerStatus"></p>

        <!-- New Card button is hidden / disabled during a live round -->
        <button id="newCardBtn" onclick="tryNewCard()" class="secondary mt">New Card</button>
        <p id="newCardLockedMsg" class="mt hidden" style="color:var(--accent);font-size:0.95rem;">
          Cannot change card while a round is in progress.
        </p>
      </div>
    </div>

    <div class="card">
      <h3>🏆 Leaderboard</h3>
      <div class="leaderboard" id="leaderboardPlayer"></div>
    </div>
  </div>

</div>

<script>
// ========== STATE ==========
const STORAGE_KEY = 'stevesKitchenBingo_v3';
let state = {
  called: [],
  players: {},
  claims: [],
  lastCalled: null,
  bonusNumber: null,
  points: {},
  roundId: 0          // increments every new round so players can detect it
};

let localPlayerName = null;   // current player's name on this device
let lastSeenRoundId = 0;      // to detect new rounds

function loadState() {
  try {
    const raw = localStorage.getItem(STORAGE_KEY);
    if (raw) state = JSON.parse(raw);
  } catch(e) {}
}
function saveState() {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
}

// Traditional UK-style call phrases
const phrases = {
  1:"Kelly's Eye", 2:"One little duck", 3:"Cup of tea", 4:"Knock at the door",
  5:"Man alive", 6:"Tom Mix", 7:"Lucky seven", 8:"Garden gate", 9:"Doctor's orders",
  10:"Boris's den", 11:"Legs eleven", 12:"One dozen", 13:"Unlucky for some",
  14:"Valentine's Day", 15:"Young and keen", 16:"Sweet sixteen", 17:"Dancing queen",
  18:"Coming of age", 19:"Goodbye teens", 20:"One score", 21:"Key of the door",
  22:"Two little ducks", 23:"The Lord is my shepherd", 24:"Two dozen", 25:"Duck and dive",
  26:"Pick and mix", 27:"Gateway to heaven", 28:"Overweight", 29:"Rise and shine",
  30:"Dirty Gertie", 31:"Get up and run", 32:"Buckle my shoe", 33:"All the threes",
  34:"Ask for more", 35:"Jump and jive", 36:"Three dozen", 37:"More than eleven",
  38:"Christmas cake", 39:"Steps", 40:"Life begins", 41:"Time for fun",
  42:"Winnie the Pooh", 43:"Down on your knees", 44:"Droopy drawers", 45:"Halfway there",
  46:"Up to tricks", 47:"Four and seven", 48:"Four dozen", 49:"PC", 50:"Half a century",
  51:"Tweak of the thumb", 52:"Danny La Rue", 53:"Here comes Herbie", 54:"Clean the floor",
  55:"Snakes alive", 56:"Was she worth it?", 57:"Heinz varieties", 58:"Make them wait",
  59:"Brighton line", 60:"Five dozen", 61:"Baker's bun", 62:"Turn the screw",
  63:"Tickety boo", 64:"Red raw", 65:"Old age pension", 66:"Clickety click",
  67:"Made in heaven", 68:"Saving grace", 69:"Anyway up", 70:"Three score and ten",
  71:"Bang on the drum", 72:"Six dozen", 73:"Queen bee", 74:"Candy store",
  75:"Strive and strive", 76:"Trombones", 77:"Sunset strip", 78:"Heaven's gate",
  79:"One more time", 80:"Eight and blank", 81:"Stop and run", 82:"Straight on through",
  83:"Time for tea", 84:"Seven dozen", 85:"Staying alive", 86:"Between the sticks",
  87:"Torquay in Devon", 88:"Two fat ladies", 89:"Nearly there", 90:"Top of the shop"
};

// ========== NAV ==========
function showHome() {
  ['homePage','loginPage','callerPage','playerPage'].forEach(id => {
    document.getElementById(id).classList.toggle('hidden', id !== 'homePage');
  });
}
function showLogin() {
  ['homePage','loginPage','callerPage','playerPage'].forEach(id => {
    document.getElementById(id).classList.toggle('hidden', id !== 'loginPage');
  });
  document.getElementById('loginError').textContent = '';
}
function showPlayer() {
  ['homePage','loginPage','callerPage','playerPage'].forEach(id => {
    document.getElementById(id).classList.toggle('hidden', id !== 'playerPage');
  });
  document.getElementById('playerSetup').classList.remove('hidden');
  document.getElementById('playerGame').classList.add('hidden');
  document.getElementById('newRoundPrompt').classList.add('hidden');
  updatePlayerCardLock();
}
function showCaller() {
  document.getElementById('loginPage').classList.add('hidden');
  document.getElementById('callerPage').classList.remove('hidden');
  if (!state.bonusNumber) pickBonusNumber();
  renderCaller();
  startPolling();
}

// ========== AUTH ==========
function doLogin() {
  const u = document.getElementById('loginUser').value.trim();
  const p = document.getElementById('loginPass').value;
  if (u === 'SCFC' && p === 'STEVES KITCHEN') {
    sessionStorage.setItem('steveLoggedIn', '1');
    showCaller();
  } else {
    document.getElementById('loginError').textContent = 'Wrong username or password';
  }
}
function logout() {
  sessionStorage.removeItem('steveLoggedIn');
  showHome();
}
function checkAuth() {
  if (sessionStorage.getItem('steveLoggedIn') === '1') showCaller();
}

// ========== BONUS & ROUNDS ==========
function pickBonusNumber() {
  state.bonusNumber = Math.floor(Math.random() * 90) + 1;
  saveState();
}
function startNewRound() {
  if (!confirm('Start a new round?\n• Clears called numbers & claims\n• Picks a new bonus number\n• Points stay on the leaderboard\n• Players will be asked if they want to keep their card')) return;
  state.called = [];
  state.lastCalled = null;
  state.claims = [];
  state.roundId = (state.roundId || 0) + 1;   // signal new round to players
  pickBonusNumber();
  saveState();
  renderCaller();
}
function fullReset() {
  if (!confirm('FULL RESET?\nThis clears everything including the leaderboard points.')) return;
  state = { called: [], players: {}, claims: [], lastCalled: null, bonusNumber: null, points: {}, roundId: 0 };
  pickBonusNumber();
  saveState();
  renderCaller();
}

// ========== CALLER ==========
function buildNumberBoard() {
  const board = document.getElementById('numberBoard');
  board.innerHTML = '';
  for (let i = 1; i <= 90; i++) {
    const cell = document.createElement('div');
    let cls = 'num-cell';
    if (state.called.includes(i)) cls += ' called';
    if (i === state.bonusNumber) cls += ' bonus';
    cell.className = cls;
    cell.textContent = i;
    board.appendChild(cell);
  }
}

function callNextNumber() {
  loadState();
  const remaining = [];
  for (let i = 1; i <= 90; i++) if (!state.called.includes(i)) remaining.push(i);
  if (remaining.length === 0) {
    alert('All numbers have been called!');
    return;
  }
  const num = remaining[Math.floor(Math.random() * remaining.length)];
  state.called.push(num);
  state.lastCalled = num;
  saveState();
  renderCaller();
}

function renderCaller() {
  loadState();
  const num = state.lastCalled;
  document.getElementById('callerBigNum').textContent = num || '–';
  document.getElementById('callPhrase').textContent = num ? (phrases[num] || '') + ` – ${num}` : 'Press “Call Next Number”';
  document.getElementById('bonusDisplay').textContent = state.bonusNumber || '–';
  buildNumberBoard();

  const claimsDiv = document.getElementById('bingoClaims');
  if (state.claims.length) {
    claimsDiv.innerHTML = state.claims.map(c =>
      `<div class="bingo-alert">🎉 ${c.name} has claimed BINGO! 🎉</div>`
    ).join('');
  } else {
    claimsDiv.innerHTML = '';
  }
  renderLeaderboard('leaderboardCaller');
}

function searchPlayer() {
  loadState();
  const name = document.getElementById('searchName').value.trim();
  const area = document.getElementById('playerCheckArea');
  if (!name || !state.players[name]) {
    area.innerHTML = '<p style="color:var(--accent)">Player not found. They need to generate a card first.</p>';
    return;
  }
  const nums = state.players[name];
  const hitCount = nums.filter(n => state.called.includes(n)).length;
  const isFullHouse = hitCount === 15;

  let html = `<h4>${name}'s Card</h4><div class="bingo-grid">`;
  nums.forEach(n => {
    const marked = state.called.includes(n);
    html += `<div class="bingo-cell ${marked ? 'auto-marked' : ''}">${n}</div>`;
  });
  html += `</div>
    <p class="center mt">Marked: <strong>${hitCount} / 15</strong>
      ${isFullHouse ? ' ✅ FULL HOUSE!' : ''}</p>`;

  if (isFullHouse) {
    const isBonus = state.lastCalled === state.bonusNumber;
    const pts = isBonus ? 2 : 1;
    html += `<div class="center mt">
      <button class="success" onclick="awardBingo('${name}', ${pts})">
        Confirm Bingo & Award ${pts} Point${pts > 1 ? 's' : ''}
        ${isBonus ? ' (BONUS!)' : ''}
      </button>
    </div>`;
  } else {
    html += `<p class="center mt" style="color:var(--muted);font-size:0.9rem;">Not a full house yet – cannot award points.</p>`;
  }
  area.innerHTML = html;
}

function awardBingo(name, points) {
  loadState();
  if (!state.points[name]) state.points[name] = 0;
  state.points[name] += points;
  state.claims = state.claims.filter(c => c.name !== name);
  saveState();
  alert(`${name} awarded ${points} point${points > 1 ? 's' : ''}!`);
  renderCaller();
  searchPlayer();
}

// ========== PLAYER ==========
function isRoundInProgress() {
  return state.called && state.called.length > 0;
}

function updatePlayerCardLock() {
  const locked = isRoundInProgress();
  const getBtn = document.getElementById('getCardBtn');
  const newBtn = document.getElementById('newCardBtn');
  const lockedMsg = document.getElementById('roundLockedMsg');
  const newLockedMsg = document.getElementById('newCardLockedMsg');

  if (getBtn) {
    getBtn.disabled = locked;
    lockedMsg.classList.toggle('hidden', !locked);
  }
  if (newBtn) {
    newBtn.disabled = locked;
    newLockedMsg.classList.toggle('hidden', !locked);
  }
}

function createPlayerCard() {
  if (isRoundInProgress()) {
    alert('A round is already in progress. You cannot get a new card until the next round starts.');
    return;
  }
  const name = document.getElementById('playerName').value.trim();
  if (!name) { alert('Please enter a name'); return; }
  loadState();
  const pool = Array.from({length:90}, (_,i)=>i+1);
  const card = [];
  for (let i = 0; i < 15; i++) {
    const idx = Math.floor(Math.random() * pool.length);
    card.push(pool.splice(idx, 1)[0]);
  }
  card.sort((a,b)=>a-b);
  state.players[name] = card;
  if (!state.points[name]) state.points[name] = 0;
  saveState();

  localPlayerName = name;
  lastSeenRoundId = state.roundId || 0;

  document.getElementById('playerSetup').classList.add('hidden');
  document.getElementById('playerGame').classList.remove('hidden');
  document.getElementById('newRoundPrompt').classList.add('hidden');
  document.getElementById('displayName').textContent = name;
  renderPlayerCard(card);
  startPolling();
  renderLeaderboard('leaderboardPlayer');
  updatePlayerCardLock();
}

function renderPlayerCard(card) {
  const grid = document.getElementById('playerGrid');
  grid.innerHTML = '';
  const dabKey = 'dab_' + (localPlayerName || document.getElementById('displayName').textContent);
  let dabbed = [];
  try { dabbed = JSON.parse(localStorage.getItem(dabKey) || '[]'); } catch(e){}

  card.forEach(n => {
    const cell = document.createElement('div');
    cell.className = 'bingo-cell' + (dabbed.includes(n) ? ' dabbed' : '');
    cell.textContent = n;
    cell.onclick = () => {
      cell.classList.toggle('dabbed');
      const nowDabbed = [...grid.querySelectorAll('.bingo-cell.dabbed')].map(c => +c.textContent);
      localStorage.setItem(dabKey, JSON.stringify(nowDabbed));
    };
    grid.appendChild(cell);
  });
}

function tryNewCard() {
  if (isRoundInProgress()) {
    alert('Cannot change card while a round is in progress.');
    return;
  }
  // clear dab marks for old card
  if (localPlayerName) {
    localStorage.removeItem('dab_' + localPlayerName);
  }
  document.getElementById('playerSetup').classList.remove('hidden');
  document.getElementById('playerGame').classList.add('hidden');
  document.getElementById('newRoundPrompt').classList.add('hidden');
  document.getElementById('playerName').value = localPlayerName || '';
  localPlayerName = null;
  updatePlayerCardLock();
}

function keepSameCard() {
  // just hide the prompt and continue with current card
  document.getElementById('newRoundPrompt').classList.add('hidden');
  document.getElementById('playerGame').classList.remove('hidden');
  lastSeenRoundId = state.roundId || 0;
  // clear any previous dab marks? optional – keep them or clear?
  // we keep them so player can continue marking
  updatePlayerCardLock();
}

function getNewCardAfterRound() {
  // player chose to get a new card after the round change
  if (localPlayerName) {
    localStorage.removeItem('dab_' + localPlayerName);
  }
  document.getElementById('newRoundPrompt').classList.add('hidden');
  document.getElementById('playerGame').classList.add('hidden');
  document.getElementById('playerSetup').classList.remove('hidden');
  document.getElementById('playerName').value = localPlayerName || '';
  localPlayerName = null;
  lastSeenRoundId = state.roundId || 0;
  updatePlayerCardLock();
}

function claimBingo() {
  const name = localPlayerName || document.getElementById('displayName').textContent;
  loadState();
  if (state.claims.some(c => c.name === name)) {
    document.getElementById('playerStatus').textContent = 'You already claimed Bingo this round!';
    return;
  }
  state.claims.push({ name, time: Date.now() });
  saveState();
  document.getElementById('playerStatus').textContent = 'Bingo claimed! Waiting for Steve to verify…';
}

// ========== LEADERBOARD ==========
function renderLeaderboard(elementId) {
  loadState();
  const el = document.getElementById(elementId);
  const entries = Object.entries(state.points || {}).sort((a,b) => b[1] - a[1]);

  if (entries.length === 0) {
    el.innerHTML = '<p style="color:var(--muted);text-align:center;padding:10px;">No points yet</p>';
    return;
  }
  let html = `<table>
    <thead><tr><th class="rank">#</th><th>Name</th><th class="pts">Pts</th></tr></thead>
    <tbody>`;
  entries.forEach(([name, pts], i) => {
    html += `<tr>
      <td class="rank">${i+1}</td>
      <td>${name}</td>
      <td class="pts">${pts}</td>
    </tr>`;
  });
  html += '</tbody></table>';
  el.innerHTML = html;
}

// ========== LIVE POLLING ==========
let pollTimer = null;
function startPolling() {
  if (pollTimer) clearInterval(pollTimer);
  pollTimer = setInterval(() => {
    const prevRoundId = lastSeenRoundId;
    loadState();

    // Detect new round
    if (localPlayerName && state.roundId > lastSeenRoundId) {
      // show the prompt
      document.getElementById('newRoundPrompt').classList.remove('hidden');
      document.getElementById('playerGame').classList.add('hidden');
      document.getElementById('playerSetup').classList.add('hidden');
      // do NOT update lastSeenRoundId yet – wait for player decision
    }

    // Player side live number updates
    if (document.getElementById('playerPage') && !document.getElementById('playerPage').classList.contains('hidden')) {
      const num = state.lastCalled;
      document.getElementById('playerBigNum').textContent = num || '–';
      document.getElementById('playerPhrase').textContent = num ? (phrases[num] || '') + ` – ${num}` : 'Waiting for first number…';
      document.getElementById('playerBonus').textContent = state.bonusNumber || '–';
      renderLeaderboard('leaderboardPlayer');
      updatePlayerCardLock();
    }

    // Caller side
    if (document.getElementById('callerPage') && !document.getElementById('callerPage').classList.contains('hidden')) {
      renderCaller();
    }
  }, 900);
}

// ========== INIT ==========
loadState();
if (!state.bonusNumber) {
  pickBonusNumber();
  saveState();
}
lastSeenRoundId = state.roundId || 0;
checkAuth();
if (sessionStorage.getItem('steveLoggedIn') !== '1') {
  showHome();
}
</script>
</body>
</html>
