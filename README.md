
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>음정 퀴즈</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;500;700&display=swap');

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg: #f5f4f0;
    --surface: #ffffff;
    --border: #e0ddd7;
    --text: #1a1917;
    --text-2: #6b6760;
    --green: #0f6e56; --green-bg: #e1f5ee; --green-border: #5dcaa5;
    --purple: #3c3489; --purple-bg: #eeedfe; --purple-border: #afa9ec;
    --amber: #633806; --amber-bg: #faeeda; --amber-border: #ef9f27;
    --ok-bg: #e1f5ee; --ok-text: #085041;
    --ng-bg: #fde8e8; --ng-text: #9b1c1c;
    --warn-bg: #fef3cd; --warn-text: #7d5a00;
    --radius: 10px;
    --shadow: 0 1px 3px rgba(0,0,0,.08);
  }

  body {
    font-family: 'Noto Sans KR', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    padding: 2rem 1rem;
  }

  .container { width: 100%; max-width: 560px; }

  h1 {
    font-size: 22px; font-weight: 700;
    margin-bottom: 1.5rem;
    letter-spacing: -0.5px;
  }

  /* ── Category filter ── */
  .filter-row { display: flex; gap: 8px; margin-bottom: 1.25rem; }
  .fbt {
    flex: 1; padding: 8px 6px;
    border: 1.5px solid var(--border);
    border-radius: var(--radius);
    background: var(--surface);
    color: var(--text-2);
    font-size: 13px; font-family: inherit;
    cursor: pointer; transition: all .15s;
    font-weight: 500;
  }
  .fbt.on-d { background: var(--green-bg); color: var(--green); border-color: var(--green-border); }
  .fbt.on-c { background: var(--purple-bg); color: var(--purple); border-color: var(--purple-border); }
  .fbt.on-g { background: var(--amber-bg); color: var(--amber); border-color: var(--amber-border); }

  /* ── Score row ── */
  .score-row { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; margin-bottom: 1.25rem; }
  .metric {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 10px 8px;
    text-align: center;
    box-shadow: var(--shadow);
  }
  .metric-label { font-size: 10px; color: var(--text-2); margin-bottom: 3px; }
  .metric-val { font-size: 22px; font-weight: 700; }

  /* ── Progress ── */
  .prog-wrap { background: var(--border); height: 3px; border-radius: 3px; margin-bottom: 1.25rem; overflow: hidden; }
  .prog-bar { height: 3px; background: var(--green); border-radius: 3px; transition: width .3s ease; }

  /* ── Question card ── */
  .qcard {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 14px;
    padding: 1.75rem 1.5rem;
    box-shadow: var(--shadow);
    margin-bottom: 1rem;
  }

  .qbadge {
    display: inline-block; font-size: 11px; font-weight: 600;
    padding: 3px 10px; border-radius: 20px; margin-bottom: 1rem;
    letter-spacing: 0.3px;
  }
  .qbadge.d { background: var(--green-bg); color: var(--green); }
  .qbadge.c { background: var(--purple-bg); color: var(--purple); }
  .qbadge.g { background: var(--amber-bg); color: var(--amber); }

  /* ── Notes display ── */
  .notes-wrap {
    display: flex; align-items: baseline; justify-content: center;
    gap: 12px; margin: 1rem 0 1.25rem;
  }
  .note-item {
    display: flex; align-items: flex-start;
    font-size: 40px; font-weight: 700; line-height: 1; color: var(--text);
    letter-spacing: -1px;
  }
  .note-item sup {
    font-size: 13px; font-weight: 500;
    color: var(--text-2);
    margin-top: 2px;
    margin-left: 1px;
    line-height: 1;
  }
  .arrow { font-size: 24px; color: var(--text-2); align-self: center; }

  /* ── Answer dropdowns ── */
  .ans-row { display: flex; gap: 8px; align-items: stretch; }
  .ans-row select {
    flex: 1; height: 40px;
    border: 1.5px solid var(--border);
    border-radius: var(--radius);
    background: var(--surface);
    color: var(--text);
    font-size: 14px; font-family: inherit;
    padding: 0 10px;
    cursor: pointer;
    appearance: auto;
  }
  .ans-row select:focus { outline: 2px solid var(--green); border-color: transparent; }
  #check-btn {
    height: 40px; padding: 0 18px;
    background: var(--text); color: #fff;
    border: none; border-radius: var(--radius);
    font-size: 14px; font-family: inherit; font-weight: 600;
    cursor: pointer; white-space: nowrap;
    transition: opacity .15s;
  }
  #check-btn:hover:not(:disabled) { opacity: .85; }
  #check-btn:disabled { opacity: .4; cursor: not-allowed; }

  /* ── Feedback ── */
  .feedback {
    border-radius: var(--radius);
    padding: 12px 14px; margin-top: 1rem;
    font-size: 14px; line-height: 1.5;
    font-weight: 500;
  }
  .feedback.ok { background: var(--ok-bg); color: var(--ok-text); }
  .feedback.ng { background: var(--ng-bg); color: var(--ng-text); }
  .feedback.wr { background: var(--warn-bg); color: var(--warn-text); }

  /* ── Keyboard explanation ── */
  #keyboard-wrap {
    display: none;
    margin-top: 1rem;
    background: #fafaf9;
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 10px 12px 12px;
  }
  .kb-title {
    font-size: 11px; font-weight: 600;
    color: var(--text-2); margin-bottom: 8px;
    letter-spacing: 0.3px;
  }
  .kb-scroll {
    overflow-x: auto;
    padding-bottom: 4px;
  }
  .kb-legend {
    display: flex; gap: 14px; justify-content: center;
    margin-top: 9px; flex-wrap: wrap;
  }
  .kb-legend-item {
    display: flex; align-items: center; gap: 5px;
    font-size: 11px; color: var(--text-2);
  }
  .kb-legend-dot {
    width: 10px; height: 10px; border-radius: 2px;
    flex-shrink: 0;
  }

  /* ── Next button ── */
  .next-btn {
    margin-top: .75rem; width: 100%;
    background: var(--surface);
    border: 1.5px solid var(--border);
    border-radius: var(--radius);
    padding: 10px; cursor: pointer;
    color: var(--text); font-size: 14px; font-family: inherit; font-weight: 500;
    transition: background .15s;
  }
  .next-btn:hover { background: var(--bg); }

  /* ── Reference table ── */
  details { margin-top: .5rem; }
  summary {
    font-size: 12px; color: var(--text-2);
    cursor: pointer; padding: 5px 0;
    user-select: none; list-style: none;
  }
  summary::before { content: '▸ '; }
  details[open] summary::before { content: '▾ '; }
  .ref-grid {
    display: grid; grid-template-columns: repeat(3, 1fr);
    gap: 10px; margin-top: 8px;
  }
  .ref-col { font-size: 11px; }
  .ref-col h4 {
    font-size: 11px; font-weight: 600; padding: 3px 7px;
    border-radius: 6px; margin-bottom: 5px;
  }
  .ref-col.d h4 { background: var(--green-bg); color: var(--green); }
  .ref-col.c h4 { background: var(--purple-bg); color: var(--purple); }
  .ref-col.g h4 { background: var(--amber-bg); color: var(--amber); }
  .ref-col table { width: 100%; border-collapse: collapse; }
  .ref-col td {
    padding: 2px 5px;
    border-bottom: 1px solid var(--border);
    color: var(--text-2);
  }
  .ref-col td:last-child {
    font-weight: 600; color: var(--text); text-align: right;
  }
</style>
</head>
<body>
<div class="container">
  <h1>🎵 음정 퀴즈</h1>

  <div class="filter-row">
    <button class="fbt on-d" id="btn-d" onclick="toggleCat('d')">온음계</button>
    <button class="fbt on-c" id="btn-c" onclick="toggleCat('c')">반음계</button>
    <button class="fbt on-g" id="btn-g" onclick="toggleCat('g')">겹음정</button>
  </div>

  <div class="score-row">
    <div class="metric"><div class="metric-label">맞힌 문제</div><div class="metric-val" id="cnt-ok">0</div></div>
    <div class="metric"><div class="metric-label">틀린 문제</div><div class="metric-val" id="cnt-ng">0</div></div>
    <div class="metric"><div class="metric-label">정답률</div><div class="metric-val" id="accuracy">—</div></div>
    <div class="metric"><div class="metric-label">문제 번호</div><div class="metric-val" id="q-num">1</div></div>
  </div>

  <div class="prog-wrap"><div class="prog-bar" id="prog-bar" style="width:0%"></div></div>

  <div class="qcard">
    <div class="qbadge d" id="q-badge">온음계적 음정</div>

    <div class="notes-wrap" id="notes-wrap">
      <div class="note-item">—</div>
      <div class="arrow">→</div>
      <div class="note-item">—</div>
    </div>

    <div class="ans-row">
      <select id="q-sel">
        <option value="">— 성질 —</option>
        <option value="완전">완전</option>
        <option value="장">장</option>
        <option value="단">단</option>
        <option value="증">증</option>
        <option value="감">감</option>
      </select>
      <select id="d-sel">
        <option value="">— 도수 —</option>
        <option value="1">1도</option><option value="2">2도</option>
        <option value="3">3도</option><option value="4">4도</option>
        <option value="5">5도</option><option value="6">6도</option>
        <option value="7">7도</option><option value="8">8도</option>
        <option value="9">9도</option><option value="10">10도</option>
        <option value="11">11도</option><option value="12">12도</option>
        <option value="13">13도</option><option value="14">14도</option>
      </select>
      <button id="check-btn" onclick="checkAns()">확인 ✓</button>
    </div>

    <div id="feedback" class="feedback" style="display:none"></div>

    <!-- 건반 해설 -->
    <div id="keyboard-wrap">
      <div class="kb-title">🎹 건반 해설</div>
      <div class="kb-scroll" id="kb-svg-container"></div>
      <div class="kb-legend" id="kb-legend"></div>
    </div>

    <button class="next-btn" id="next-btn" style="display:none" onclick="nextQ()">다음 문제 →</button>
  </div>

  <details>
    <summary>음정 참고표</summary>
    <div class="ref-grid">
      <div class="ref-col d">
        <h4>온음계</h4>
        <table>
          <tr><td>0반음 / 1도</td><td>완전1도</td></tr>
          <tr><td>1반음 / 2도</td><td>단2도</td></tr>
          <tr><td>2반음 / 2도</td><td>장2도</td></tr>
          <tr><td>3반음 / 3도</td><td>단3도</td></tr>
          <tr><td>4반음 / 3도</td><td>장3도</td></tr>
          <tr><td>5반음 / 4도</td><td>완전4도</td></tr>
          <tr><td>6반음 / 4도</td><td>증4도</td></tr>
          <tr><td>6반음 / 5도</td><td>감5도</td></tr>
          <tr><td>7반음 / 5도</td><td>완전5도</td></tr>
          <tr><td>8반음 / 6도</td><td>단6도</td></tr>
          <tr><td>9반음 / 6도</td><td>장6도</td></tr>
          <tr><td>10반음 / 7도</td><td>단7도</td></tr>
          <tr><td>11반음 / 7도</td><td>장7도</td></tr>
          <tr><td>12반음 / 8도</td><td>완전8도</td></tr>
        </table>
      </div>
      <div class="ref-col c">
        <h4>반음계</h4>
        <table>
          <tr><td>1반음 / 1도</td><td>증1도</td></tr>
          <tr><td>0반음 / 2도</td><td>감2도</td></tr>
          <tr><td>3반음 / 2도</td><td>증2도</td></tr>
          <tr><td>2반음 / 3도</td><td>감3도</td></tr>
          <tr><td>5반음 / 3도</td><td>증3도</td></tr>
          <tr><td>4반음 / 4도</td><td>감4도</td></tr>
          <tr><td>8반음 / 5도</td><td>증5도</td></tr>
          <tr><td>7반음 / 6도</td><td>감6도</td></tr>
          <tr><td>10반음 / 6도</td><td>증6도</td></tr>
          <tr><td>9반음 / 7도</td><td>감7도</td></tr>
          <tr><td>12반음 / 7도</td><td>증7도</td></tr>
          <tr><td>11반음 / 8도</td><td>감8도</td></tr>
        </table>
      </div>
      <div class="ref-col g">
        <h4>겹음정 (+1옥타브)</h4>
        <table>
          <tr><td>13반음 / 9도</td><td>단9도</td></tr>
          <tr><td>14반음 / 9도</td><td>장9도</td></tr>
          <tr><td>15반음 / 10도</td><td>단10도</td></tr>
          <tr><td>16반음 / 10도</td><td>장10도</td></tr>
          <tr><td>17반음 / 11도</td><td>완전11도</td></tr>
          <tr><td>18반음 / 11도</td><td>증11도</td></tr>
          <tr><td>19반음 / 12도</td><td>완전12도</td></tr>
          <tr><td>20반음 / 13도</td><td>단13도</td></tr>
          <tr><td>21반음 / 13도</td><td>장13도</td></tr>
          <tr><td>22반음 / 14도</td><td>단14도</td></tr>
          <tr><td>23반음 / 14도</td><td>장14도</td></tr>
        </table>
      </div>
    </div>
  </details>
</div>

<script>
const DEGS = ['도','레','미','파','솔','라','시'];
const BASE = [0, 2, 4, 5, 7, 9, 11];

function nn(deg, acc) {
  return DEGS[deg] + (acc === -1 ? '♭' : acc === 1 ? '♯' : '');
}
function excluded(deg, acc) {
  return (deg === 0 && acc === -1) || (deg === 2 && acc === 1) ||
         (deg === 3 && acc === -1) || (deg === 6 && acc === 1);
}

const IVLS = {
  d: [
    {n:'완전1도',g:1,s:0},{n:'단2도',g:2,s:1},{n:'장2도',g:2,s:2},
    {n:'단3도',g:3,s:3},{n:'장3도',g:3,s:4},{n:'완전4도',g:4,s:5},
    {n:'증4도',g:4,s:6},{n:'감5도',g:5,s:6},{n:'완전5도',g:5,s:7},
    {n:'단6도',g:6,s:8},{n:'장6도',g:6,s:9},{n:'단7도',g:7,s:10},
    {n:'장7도',g:7,s:11},{n:'완전8도',g:8,s:12}
  ],
  c: [
    {n:'증1도',g:1,s:1},{n:'감2도',g:2,s:0},{n:'증2도',g:2,s:3},
    {n:'감3도',g:3,s:2},{n:'증3도',g:3,s:5},{n:'감4도',g:4,s:4},
    {n:'증5도',g:5,s:8},{n:'감6도',g:6,s:7},{n:'증6도',g:6,s:10},
    {n:'감7도',g:7,s:9},{n:'증7도',g:7,s:12},{n:'감8도',g:8,s:11}
  ],
  g: [
    {n:'단9도',g:9,s:13},{n:'장9도',g:9,s:14},
    {n:'단10도',g:10,s:15},{n:'장10도',g:10,s:16},
    {n:'완전11도',g:11,s:17},{n:'증11도',g:11,s:18},
    {n:'완전12도',g:12,s:19},
    {n:'단13도',g:13,s:20},{n:'장13도',g:13,s:21},
    {n:'단14도',g:14,s:22},{n:'장14도',g:14,s:23}
  ]
};

Object.entries(IVLS).forEach(([cat, list]) => list.forEach(iv => iv.cat = cat));
const ALL_NAMES = new Set(Object.values(IVLS).flat().map(i => i.n));
const CAT_LABELS = { d:'온음계적 음정', c:'반음계적 음정', g:'겹음정' };

const enabled = { d: true, c: true, g: true };
let curQ = null, answered = false, nOk = 0, nNg = 0, qNum = 0;

function pool() {
  return Object.entries(enabled).filter(([,v]) => v).flatMap(([k]) => IVLS[k]);
}

function toggleCat(cat) {
  if (enabled[cat] && Object.values(enabled).filter(Boolean).length === 1) return;
  enabled[cat] = !enabled[cat];
  const btn = document.getElementById('btn-' + cat);
  btn.className = 'fbt' + (enabled[cat] ? ' on-' + cat : '');
}

function genQ() {
  const p = pool();
  if (!p.length) return null;
  const ACC_POOL = [-1, 0, 0, 0, 1];
  for (let i = 0; i < 400; i++) {
    const iv = p[Math.floor(Math.random() * p.length)];
    const bd = Math.floor(Math.random() * 7);
    const ba = ACC_POOL[Math.floor(Math.random() * ACC_POOL.length)];
    if (excluded(bd, ba)) continue;
    const bst = BASE[bd] + ba;
    const off = iv.g - 1;
    const td  = (bd + off) % 7;
    const oc  = Math.floor((bd + off) / 7);
    if (iv.g >= 9 && oc !== 1) continue;
    const ets   = bst + iv.s;
    const tbase = BASE[td] + oc * 12;
    const ta    = ets - tbase;
    if (ta < -1 || ta > 1) continue;
    if (excluded(td, ta)) continue;
    return { bot: {d:bd, a:ba}, top: {d:td, a:ta, oc}, iv };
  }
  return null;
}

function noteHTML(deg, acc, octNum) {
  return `<div class="note-item">${nn(deg,acc)}<sup>${octNum}</sup></div>`;
}

function renderQ() {
  answered = false;
  const q = genQ();
  if (!q) return;
  curQ = q;
  qNum++;

  document.getElementById('q-num').textContent = qNum;
  const badge = document.getElementById('q-badge');
  badge.className = 'qbadge ' + q.iv.cat;
  badge.textContent = CAT_LABELS[q.iv.cat];

  const botOct = 1;
  const topOct = 1 + q.top.oc;
  document.getElementById('notes-wrap').innerHTML =
    noteHTML(q.bot.d, q.bot.a, botOct) +
    '<div class="arrow">→</div>' +
    noteHTML(q.top.d, q.top.a, topOct);

  document.getElementById('q-sel').value = '';
  document.getElementById('d-sel').value = '';
  document.getElementById('feedback').style.display = 'none';
  document.getElementById('keyboard-wrap').style.display = 'none';
  document.getElementById('next-btn').style.display = 'none';
  document.getElementById('check-btn').disabled = false;
  document.getElementById('prog-bar').style.width = Math.min((qNum-1)/20*100,100)+'%';
}

function checkAns() {
  if (answered) return;
  const qs = document.getElementById('q-sel').value;
  const ds = document.getElementById('d-sel').value;
  const fb = document.getElementById('feedback');
  if (!qs || !ds) {
    fb.className = 'feedback wr';
    fb.textContent = '성질과 도수를 모두 선택해 주세요.';
    fb.style.display = 'block'; return;
  }
  answered = true;
  document.getElementById('check-btn').disabled = true;

  const attempt = qs + ds + '도';
  const correct = curQ.iv.n;
  const isRight = attempt === correct;
  if (isRight) nOk++; else nNg++;
  document.getElementById('cnt-ok').textContent = nOk;
  document.getElementById('cnt-ng').textContent = nNg;
  document.getElementById('accuracy').textContent = Math.round(nOk/(nOk+nNg)*100)+'%';

  const { g, s, n } = curQ.iv;
  const semisExpl = g >= 9
    ? `반음 ${s}개 (단순 ${s-12}반음 + 1옥타브)`
    : `반음 ${s}개`;

  fb.style.display = 'block';
  if (!ALL_NAMES.has(attempt)) {
    fb.className = 'feedback wr';
    fb.innerHTML = `⚠ <b>${attempt}</b>은(는) 존재하지 않는 음정입니다.<br>정답: <b>${n}</b> — 도수 ${g}도, ${semisExpl}`;
  } else if (isRight) {
    fb.className = 'feedback ok';
    fb.innerHTML = `✓ 정답! <b>${n}</b> — 도수 ${g}도, ${semisExpl}`;
  } else {
    fb.className = 'feedback ng';
    fb.innerHTML = `✗ 오답. 정답: <b>${n}</b> — 도수 ${g}도, ${semisExpl}`;
  }

  // 건반 해설 렌더링
  const botSemi = BASE[curQ.bot.d] + curQ.bot.a;
  const topSemi = BASE[curQ.top.d] + curQ.top.a + curQ.top.oc * 12;
  const botLabel = nn(curQ.bot.d, curQ.bot.a);
  const topLabel = nn(curQ.top.d, curQ.top.a) + (curQ.top.oc ? '\u00b2' : '\u00b9');
  const botLabelPlain = nn(curQ.bot.d, curQ.bot.a) + '\u00b9';

  renderKeyboard(botSemi, topSemi, botLabelPlain, topLabel, n, s);

  document.getElementById('next-btn').style.display = 'block';
}

/* ─────────────────────────────────────────
   건반 해설 렌더러
───────────────────────────────────────── */
function renderKeyboard(botSemi, topSemi, botLabel, topLabel, ivName, semitones) {
  const WW = 26;   // 흰 건반 너비
  const WH = 72;   // 흰 건반 높이
  const BW = 15;   // 검은 건반 너비
  const BH = 45;   // 검은 건반 높이
  const TOP_PAD = 28; // 브래킷 공간
  const BOT_PAD = 20; // 음이름 레이블 공간
  const WHITE_SET = new Set([0,2,4,5,7,9,11]);

  // 표시 범위: 아래음 옥타브의 C ~ 위음 옥타브의 B+1
  const botOct = Math.floor(botSemi / 12);
  const topOct = Math.floor(topSemi / 12);
  const startSemi = botOct * 12;
  const endSemi   = topOct * 12 + 12; // 위음 옥타브 끝(다음 C 포함)

  // 건반 목록
  const keys = [];
  for (let s = startSemi; s <= endSemi; s++) {
    keys.push({ s, c: s % 12, isWhite: WHITE_SET.has(s % 12) });
  }

  // 흰 건반 x 좌표
  let wx = 4;
  const pos = {};
  keys.forEach(k => {
    if (k.isWhite) { pos[k.s] = wx; wx += WW; }
  });
  // 검은 건반 x 좌표
  keys.forEach(k => {
    if (!k.isWhite && pos[k.s - 1] !== undefined) {
      pos[k.s] = pos[k.s - 1] + WW - BW / 2;
    }
  });

  const svgW = wx + 4;
  const svgH = TOP_PAD + WH + BOT_PAD;
  const KY = TOP_PAD; // 건반 시작 y

  const parts = [];

  // 범위 배경 (아래음~위음 사이 반음 영역 표시)
  if (botSemi !== topSemi) {
    const bx = pos[botSemi] !== undefined
      ? pos[botSemi] + (WHITE_SET.has(botSemi % 12) ? 0 : -1)
      : null;
    const tx = pos[topSemi] !== undefined
      ? pos[topSemi] + (WHITE_SET.has(topSemi % 12) ? WW - 1 : BW)
      : null;
    if (bx !== null && tx !== null) {
      parts.push(`<rect x="${bx}" y="${KY}" width="${tx - bx}" height="${WH}" rx="0" fill="rgba(99,102,241,0.06)" />`);
    }
  }

  // 흰 건반
  keys.filter(k => k.isWhite).forEach(k => {
    const x = pos[k.s];
    const isB = k.s === botSemi, isT = k.s === topSemi;
    const isBoth = isB && isT;
    let fill = '#fff';
    if (isBoth) fill = '#a78bfa';
    else if (isB) fill = '#6ee7b7';
    else if (isT) fill = '#93c5fd';
    parts.push(`<rect x="${x}" y="${KY}" width="${WW - 1}" height="${WH}" rx="3" fill="${fill}" stroke="#ccc" stroke-width="1"/>`);
  });

  // 검은 건반
  keys.filter(k => !k.isWhite).forEach(k => {
    const x = pos[k.s];
    if (x === undefined) return;
    const isB = k.s === botSemi, isT = k.s === topSemi;
    const isBoth = isB && isT;
    let fill = '#1e1e1e';
    if (isBoth) fill = '#7c3aed';
    else if (isB) fill = '#059669';
    else if (isT) fill = '#2563eb';
    parts.push(`<rect x="${x}" y="${KY}" width="${BW}" height="${BH}" rx="2" fill="${fill}"/>`);
  });

  // 브래킷 (위음과 아래음 사이)
  const getCx = s => {
    if (pos[s] === undefined) return null;
    return pos[s] + (WHITE_SET.has(s % 12) ? WW / 2 : BW / 2);
  };
  const bCx = getCx(botSemi);
  const tCx = getCx(topSemi);

  if (bCx !== null && tCx !== null) {
    const bracketY = KY - 7;
    const tickH = 6;
    if (botSemi === topSemi) {
      // 유니즌: 단순 세로선
      parts.push(`<line x1="${bCx}" y1="${KY}" x2="${bCx}" y2="${bracketY - 2}" stroke="#6366f1" stroke-width="2" stroke-linecap="round"/>`);
    } else {
      // ㄷ 형 브래킷
      parts.push(`<line x1="${bCx}" y1="${KY}" x2="${bCx}" y2="${bracketY}" stroke="#6366f1" stroke-width="2" stroke-linecap="round"/>`);
      parts.push(`<line x1="${tCx}" y1="${KY}" x2="${tCx}" y2="${bracketY}" stroke="#6366f1" stroke-width="2" stroke-linecap="round"/>`);
      parts.push(`<line x1="${bCx}" y1="${bracketY}" x2="${tCx}" y2="${bracketY}" stroke="#6366f1" stroke-width="2" stroke-linecap="round"/>`);
    }
    // 음정 이름 레이블
    const midX = (bCx + tCx) / 2;
    const labelY = bracketY - 5;
    // 배경 pill
    const lblW = ivName.length * 8 + 14;
    parts.push(`<rect x="${midX - lblW/2}" y="${labelY - 12}" width="${lblW}" height="14" rx="7" fill="#6366f1"/>`);
    parts.push(`<text x="${midX}" y="${labelY - 2}" text-anchor="middle" font-size="10" font-family="'Noto Sans KR',sans-serif" fill="#fff" font-weight="700">${ivName}</text>`);
  }

  // 음이름 레이블 (건반 아래)
  [[botSemi, botLabel, '#065f46'], [topSemi, topLabel, '#1e3a8a']].forEach(([s, lbl, color]) => {
    if (pos[s] === undefined) return;
    const isW = WHITE_SET.has(s % 12);
    const cx = pos[s] + (isW ? WW / 2 - 0.5 : BW / 2);
    // 검은 건반은 흰 건반 위에 있어서 레이블이 겹칠 수 있음 → 흰 건반 아래쪽에만 표시
    // 검은 건반이면 살짝 오른쪽으로 밀기
    const lx = isW ? cx : cx + 3;
    parts.push(`<text x="${lx}" y="${KY + WH + 14}" text-anchor="middle" font-size="11" font-family="'Noto Sans KR',sans-serif" fill="${color}" font-weight="700">${lbl}</text>`);
  });

  // 반음 수 카운터 (건반 사이 반음들에 점)
  if (botSemi !== topSemi) {
    // 반음 사이 건반에 작은 번호 표시 (선택적: 너무 복잡하면 생략)
    // 대신 아래 레전드로 표시
  }

  const svg = `<svg width="${svgW}" height="${svgH}" xmlns="http://www.w3.org/2000/svg" style="display:block;min-width:${svgW}px">${parts.join('')}</svg>`;

  document.getElementById('kb-svg-container').innerHTML = svg;

  // 레전드
  const sameNote = botSemi === topSemi;
  let legendHTML = '';
  if (sameNote) {
    legendHTML = `<div class="kb-legend-item"><div class="kb-legend-dot" style="background:#7c3aed"></div><span>${botLabel} (동일음)</span></div>`;
  } else {
    legendHTML = `
      <div class="kb-legend-item"><div class="kb-legend-dot" style="background:#059669"></div><span>아래음 ${botLabel}</span></div>
      <div class="kb-legend-item"><div class="kb-legend-dot" style="background:#2563eb"></div><span>위음 ${topLabel}</span></div>
      <div class="kb-legend-item" style="color:var(--text)"><span>반음 간격: <b>${semitones}개</b></span></div>
    `;
  }
  document.getElementById('kb-legend').innerHTML = legendHTML;
  document.getElementById('keyboard-wrap').style.display = 'block';
}

function nextQ() { renderQ(); }
renderQ();
</script>
</body>
</html>
