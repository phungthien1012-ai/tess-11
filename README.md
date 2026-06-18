<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>VocabMaster – Học Từ Vựng</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Noto+Sans+SC:wght@300;400;500;700&display=swap');

  :root {
    --bg: #0f1117;
    --surface: #1a1d27;
    --surface2: #222636;
    --border: #2e3347;
    --accent: #6c63ff;
    --accent2: #00d4aa;
    --accent3: #ff6b6b;
    --accent4: #ffd166;
    --text: #e8eaf0;
    --text2: #8890a4;
    --text3: #5c6478;
    --success: #00d4aa;
    --danger: #ff6b6b;
    --warn: #ffd166;
    --radius: 12px;
    --radius-sm: 8px;
    --shadow: 0 4px 24px rgba(0,0,0,.4);
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }
  html { scroll-behavior: smooth; }
  body { font-family: 'Inter', 'Noto Sans SC', sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; }

  /* ── SIDEBAR ── */
  #app { display: flex; height: 100vh; overflow: hidden; }
  #sidebar { width: 220px; flex-shrink: 0; background: var(--surface); border-right: 1px solid var(--border); display: flex; flex-direction: column; padding: 20px 0; }
  .logo { padding: 0 20px 20px; border-bottom: 1px solid var(--border); margin-bottom: 16px; }
  .logo h1 { font-size: 18px; font-weight: 700; background: linear-gradient(135deg, var(--accent), var(--accent2)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
  .logo span { font-size: 11px; color: var(--text3); font-weight: 400; display: block; margin-top: 2px; }

  .nav-item { display: flex; align-items: center; gap: 10px; padding: 10px 20px; cursor: pointer; color: var(--text2); font-size: 13.5px; font-weight: 500; transition: all .18s; border-left: 3px solid transparent; }
  .nav-item:hover { color: var(--text); background: rgba(108,99,255,.08); }
  .nav-item.active { color: var(--accent); background: rgba(108,99,255,.12); border-left-color: var(--accent); }
  .nav-item .icon { font-size: 16px; width: 20px; text-align: center; }
  .nav-section { font-size: 10px; color: var(--text3); font-weight: 600; letter-spacing: .08em; text-transform: uppercase; padding: 12px 20px 4px; }

  .sidebar-stats { margin-top: auto; padding: 16px 20px; border-top: 1px solid var(--border); }
  .stat-row { display: flex; justify-content: space-between; font-size: 12px; color: var(--text2); margin-bottom: 6px; }
  .stat-row b { color: var(--text); }

  /* ── MAIN ── */
  #main { flex: 1; overflow-y: auto; padding: 28px 32px; }
  #main::-webkit-scrollbar { width: 6px; }
  #main::-webkit-scrollbar-track { background: transparent; }
  #main::-webkit-scrollbar-thumb { background: var(--border); border-radius: 3px; }

  .page { display: none; }
  .page.active { display: block; }

  .page-header { margin-bottom: 24px; }
  .page-header h2 { font-size: 22px; font-weight: 700; }
  .page-header p { color: var(--text2); font-size: 13.5px; margin-top: 4px; }

  /* ── CARDS ── */
  .card { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 20px; }
  .card + .card { margin-top: 14px; }

  /* ── VOCAB LIST ── */
  .filter-bar { display: flex; gap: 10px; margin-bottom: 18px; flex-wrap: wrap; align-items: center; }
  .filter-bar input { flex: 1; min-width: 200px; background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-sm); color: var(--text); font-size: 13.5px; padding: 8px 12px; outline: none; }
  .filter-bar input:focus { border-color: var(--accent); }
  .filter-bar select { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-sm); color: var(--text); font-size: 13px; padding: 8px 10px; outline: none; cursor: pointer; }
  .btn { display: inline-flex; align-items: center; gap: 6px; background: var(--accent); color: #fff; border: none; border-radius: var(--radius-sm); padding: 8px 16px; font-size: 13px; font-weight: 600; cursor: pointer; transition: all .15s; }
  .btn:hover { opacity: .88; transform: translateY(-1px); }
  .btn.secondary { background: var(--surface2); color: var(--text); border: 1px solid var(--border); }
  .btn.secondary:hover { border-color: var(--accent); color: var(--accent); }
  .btn.danger { background: var(--accent3); }
  .btn.success { background: var(--accent2); color: #0f1117; }
  .btn.sm { padding: 5px 10px; font-size: 12px; }

  .vocab-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 14px; }
  .vocab-card { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 16px; position: relative; transition: border-color .15s; }
  .vocab-card:hover { border-color: var(--accent); }
  .vocab-card .word { font-size: 18px; font-weight: 700; color: var(--text); }
  .vocab-card .ipa { font-size: 12.5px; color: var(--accent); font-family: monospace; margin: 2px 0 6px; }
  .vocab-card .definition { font-size: 13px; color: var(--text2); line-height: 1.5; }
  .vocab-card .example { font-size: 12px; color: var(--text3); font-style: italic; margin-top: 6px; border-left: 2px solid var(--border); padding-left: 8px; }
  .vocab-card .synonyms { font-size: 11.5px; color: var(--accent2); margin-top: 6px; }
  .vocab-card .tags { display: flex; gap: 6px; flex-wrap: wrap; margin-top: 8px; }
  .tag { background: rgba(108,99,255,.15); color: var(--accent); font-size: 11px; padding: 2px 8px; border-radius: 20px; font-weight: 500; }
  .tag.status-0 { background: rgba(255,107,107,.15); color: var(--accent3); }
  .tag.status-1 { background: rgba(255,209,102,.15); color: var(--warn); }
  .tag.status-2 { background: rgba(0,212,170,.15); color: var(--success); }
  .fav-btn { position: absolute; top: 12px; right: 12px; background: none; border: none; font-size: 16px; cursor: pointer; opacity: .5; transition: .15s; }
  .fav-btn:hover, .fav-btn.active { opacity: 1; }
  .audio-btn { background: rgba(108,99,255,.15); border: none; color: var(--accent); border-radius: 50%; width: 28px; height: 28px; cursor: pointer; font-size: 13px; display: inline-flex; align-items: center; justify-content: center; transition: .15s; }
  .audio-btn:hover { background: var(--accent); color: #fff; }
  .status-btns { display: flex; gap: 6px; margin-top: 10px; }
  .status-btn { flex: 1; padding: 5px 4px; font-size: 11px; border: 1px solid var(--border); background: var(--surface2); color: var(--text2); border-radius: 6px; cursor: pointer; transition: .15s; text-align: center; }
  .status-btn.active-0 { background: rgba(255,107,107,.15); border-color: var(--accent3); color: var(--accent3); }
  .status-btn.active-1 { background: rgba(255,209,102,.15); border-color: var(--warn); color: var(--warn); }
  .status-btn.active-2 { background: rgba(0,212,170,.15); border-color: var(--success); color: var(--success); }

  /* ── FLASHCARD ── */
  .flashcard-wrap { perspective: 1200px; max-width: 540px; margin: 0 auto 24px; }
  .flashcard { width: 100%; height: 300px; position: relative; transform-style: preserve-3d; transition: transform .5s cubic-bezier(.4,0,.2,1); cursor: pointer; border-radius: var(--radius); }
  .flashcard.flipped { transform: rotateY(180deg); }
  .fc-front, .fc-back { position: absolute; inset: 0; border-radius: var(--radius); backface-visibility: hidden; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 32px; text-align: center; border: 1px solid var(--border); }
  .fc-front { background: linear-gradient(135deg, var(--surface) 0%, var(--surface2) 100%); }
  .fc-back { background: linear-gradient(135deg, rgba(108,99,255,.15) 0%, var(--surface) 100%); transform: rotateY(180deg); }
  .fc-word { font-size: 36px; font-weight: 800; background: linear-gradient(135deg, var(--accent), var(--accent2)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
  .fc-ipa { font-size: 16px; color: var(--accent); margin: 6px 0; font-family: monospace; }
  .fc-hint { font-size: 12.5px; color: var(--text3); margin-top: 12px; }
  .fc-definition { font-size: 18px; color: var(--text); font-weight: 600; }
  .fc-example { font-size: 13px; color: var(--text2); margin-top: 10px; font-style: italic; }
  .fc-controls { display: flex; gap: 12px; justify-content: center; flex-wrap: wrap; }
  .fc-info { display: flex; justify-content: space-between; max-width: 540px; margin: 0 auto 12px; font-size: 13px; color: var(--text2); }
  .progress-bar { height: 4px; background: var(--border); border-radius: 2px; margin-bottom: 20px; overflow: hidden; }
  .progress-fill { height: 100%; background: linear-gradient(90deg, var(--accent), var(--accent2)); border-radius: 2px; transition: width .3s; }

  /* ── QUIZ ── */
  .quiz-wrap { max-width: 600px; margin: 0 auto; }
  .quiz-question { font-size: 16px; font-weight: 600; color: var(--text); margin-bottom: 20px; line-height: 1.6; background: var(--surface2); border-radius: var(--radius-sm); padding: 16px 20px; border-left: 3px solid var(--accent); }
  .quiz-options { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 20px; }
  .quiz-opt { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-sm); padding: 14px 16px; cursor: pointer; font-size: 14px; color: var(--text); transition: all .15s; text-align: left; }
  .quiz-opt:hover { border-color: var(--accent); background: rgba(108,99,255,.08); }
  .quiz-opt.correct { border-color: var(--success); background: rgba(0,212,170,.1); color: var(--success); }
  .quiz-opt.wrong { border-color: var(--danger); background: rgba(255,107,107,.1); color: var(--danger); }
  .quiz-opt:disabled { cursor: default; }
  .quiz-fill input { width: 100%; background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-sm); color: var(--text); font-size: 15px; padding: 12px 16px; outline: none; margin-bottom: 12px; }
  .quiz-fill input:focus { border-color: var(--accent); }
  .quiz-fill input.correct { border-color: var(--success); background: rgba(0,212,170,.08); }
  .quiz-fill input.wrong { border-color: var(--danger); background: rgba(255,107,107,.08); }
  .quiz-feedback { font-size: 13.5px; padding: 10px 14px; border-radius: var(--radius-sm); margin-bottom: 14px; }
  .quiz-feedback.correct { background: rgba(0,212,170,.1); color: var(--success); border: 1px solid rgba(0,212,170,.3); }
  .quiz-feedback.wrong { background: rgba(255,107,107,.1); color: var(--danger); border: 1px solid rgba(255,107,107,.3); }
  .quiz-score { text-align: center; padding: 40px 20px; }
  .quiz-score .big { font-size: 64px; font-weight: 800; background: linear-gradient(135deg, var(--accent), var(--accent2)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
  .quiz-score .label { font-size: 15px; color: var(--text2); margin-bottom: 24px; }
  .quiz-mode-switch { display: flex; gap: 8px; margin-bottom: 20px; }
  .mode-btn { flex: 1; padding: 10px; background: var(--surface2); border: 1px solid var(--border); border-radius: var(--radius-sm); color: var(--text2); font-size: 13px; cursor: pointer; transition: .15s; text-align: center; }
  .mode-btn.active { background: rgba(108,99,255,.15); border-color: var(--accent); color: var(--accent); font-weight: 600; }
  .answer-dir { display: flex; gap: 8px; margin-bottom: 16px; }
  .dir-btn { flex: 1; padding: 8px; background: var(--surface2); border: 1px solid var(--border); border-radius: var(--radius-sm); color: var(--text2); font-size: 12.5px; cursor: pointer; transition: .15s; text-align: center; }
  .dir-btn.active { background: rgba(0,212,170,.12); border-color: var(--accent2); color: var(--accent2); }

  /* ── READING ── */
  .reading-wrap { max-width: 720px; margin: 0 auto; }
  .reading-passage { font-size: 15.5px; line-height: 2; color: var(--text); background: var(--surface); border-radius: var(--radius); padding: 28px 32px; border: 1px solid var(--border); margin-bottom: 20px; }
  .blank-input { display: inline-block; border: none; border-bottom: 2px solid var(--accent); background: transparent; color: var(--accent2); font-size: 15.5px; font-family: inherit; outline: none; min-width: 100px; padding: 0 4px; text-align: center; transition: .2s; }
  .blank-input.correct { border-color: var(--success); color: var(--success); }
  .blank-input.wrong { border-color: var(--danger); color: var(--danger); }
  .word-bank { display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 20px; }
  .word-chip { background: var(--surface2); border: 1px solid var(--border); border-radius: 20px; padding: 5px 14px; font-size: 13px; color: var(--text); cursor: pointer; transition: .15s; }
  .word-chip:hover { border-color: var(--accent); color: var(--accent); }
  .word-chip.used { opacity: .35; }
  .reading-label { font-size: 11px; color: var(--text3); text-transform: uppercase; letter-spacing: .06em; font-weight: 600; margin-bottom: 8px; }

  /* ── CHINESE ── */
  .hanzi-card { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 20px; display: flex; flex-direction: column; align-items: center; }
  .hanzi-char { font-size: 64px; font-family: 'Noto Sans SC', sans-serif; margin-bottom: 8px; }
  .hanzi-writer-box { display: flex; justify-content: center; margin: 12px 0; }
  .hanzi-writer-box canvas { border: 1px solid var(--border); border-radius: 8px; }
  .pinyin { font-size: 18px; color: var(--accent); margin-bottom: 4px; }
  .hanzi-meaning { font-size: 14px; color: var(--text2); }
  .hanzi-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); gap: 14px; }
  .stroke-controls { display: flex; gap: 8px; justify-content: center; margin-top: 10px; }

  /* ── STATS BAR ── */
  .stats-row { display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; margin-bottom: 24px; }
  .stat-card { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius); padding: 16px; text-align: center; }
  .stat-card .num { font-size: 28px; font-weight: 800; }
  .stat-card .lbl { font-size: 12px; color: var(--text2); margin-top: 2px; }
  .stat-card.red .num { color: var(--accent3); }
  .stat-card.yellow .num { color: var(--warn); }
  .stat-card.green .num { color: var(--success); }

  /* ── MISC ── */
  .divider { border: none; border-top: 1px solid var(--border); margin: 20px 0; }
  .empty { text-align: center; color: var(--text3); padding: 40px; font-size: 14px; }
  .badge { display: inline-block; font-size: 11px; font-weight: 600; padding: 2px 8px; border-radius: 10px; }
  .badge.new { background: rgba(255,107,107,.2); color: var(--accent3); }
  .badge.learning { background: rgba(255,209,102,.2); color: var(--warn); }
  .badge.known { background: rgba(0,212,170,.2); color: var(--success); }

  /* ── UPLOAD ── */
  .upload-zone { border: 2px dashed var(--border); border-radius: var(--radius); padding: 40px; text-align: center; color: var(--text2); cursor: pointer; transition: .2s; }
  .upload-zone:hover { border-color: var(--accent); color: var(--text); }

  /* ── MOBILE ── */
  #hamburger { display: none; background: none; border: none; color: var(--text); font-size: 22px; cursor: pointer; padding: 8px; }
  @media (max-width: 768px) {
    #hamburger { display: block; position: fixed; top: 12px; left: 12px; z-index: 200; }
    #sidebar { position: fixed; left: -240px; top: 0; height: 100vh; z-index: 150; transition: left .25s; }
    #sidebar.open { left: 0; }
    #main { padding: 64px 16px 20px; }
    .vocab-grid, .hanzi-grid { grid-template-columns: 1fr; }
    .quiz-options { grid-template-columns: 1fr; }
    .stats-row { grid-template-columns: repeat(3, 1fr); }
  }

  /* scrollbar inside passage */
  .reading-passage::-webkit-scrollbar { height: 4px; }

  /* animation */
  @keyframes pop { 0%{transform:scale(.95);opacity:0} 100%{transform:scale(1);opacity:1} }
  .page.active { animation: pop .2s ease; }

  /* Tooltip */
  [title] { cursor: help; }
</style>
</head>
<body>
<div id="app">
  <!-- Hamburger (mobile) -->
  <button id="hamburger" onclick="toggleSidebar()">☰</button>

  <!-- SIDEBAR -->
  <nav id="sidebar">
    <div class="logo">
      <h1>VocabMaster</h1>
      <span>Học từ vựng thông minh</span>
    </div>
    <div class="nav-section">Học tập</div>
    <div class="nav-item active" onclick="goPage('vocab')"><span class="icon">📚</span> Từ vựng</div>
    <div class="nav-item" onclick="goPage('flashcard')"><span class="icon">🃏</span> Flashcard</div>
    <div class="nav-item" onclick="goPage('quiz')"><span class="icon">✍️</span> Trắc nghiệm / Điền từ</div>
    <div class="nav-item" onclick="goPage('reading')"><span class="icon">📖</span> Đọc hiểu</div>
    <div class="nav-section">Tiếng Trung</div>
    <div class="nav-item" onclick="goPage('chinese')"><span class="icon">🈶</span> Hán tự & Nét chữ</div>
    <div class="nav-section">Công cụ</div>
    <div class="nav-item" onclick="goPage('favorites')"><span class="icon">⭐</span> Yêu thích</div>
    <div class="nav-item" onclick="goPage('upload')"><span class="icon">⬆️</span> Import dữ liệu</div>
    <div class="sidebar-stats">
      <div class="stat-row"><span>Chưa học</span><b id="sb-new">0</b></div>
      <div class="stat-row"><span>Đang học</span><b id="sb-learning">0</b></div>
      <div class="stat-row"><span>Đã nhớ</span><b id="sb-known">0</b></div>
    </div>
  </nav>

  <!-- MAIN -->
  <main id="main">

    <!-- ═══════════════ VOCAB PAGE ═══════════════ -->
    <div id="page-vocab" class="page active">
      <div class="page-header">
        <h2>📚 Kho từ vựng</h2>
        <p>Quản lý và ôn luyện toàn bộ từ vựng của bạn</p>
      </div>
      <div class="stats-row">
        <div class="stat-card red"><div class="num" id="st-new">0</div><div class="lbl">⏳ Chưa học</div></div>
        <div class="stat-card yellow"><div class="num" id="st-learning">0</div><div class="lbl">📝 Đang học</div></div>
        <div class="stat-card green"><div class="num" id="st-known">0</div><div class="lbl">✅ Đã nhớ</div></div>
      </div>
      <div class="filter-bar">
        <input type="text" id="search-input" placeholder="🔍 Tìm kiếm từ vựng..." oninput="renderVocab()">
        <select id="filter-status" onchange="renderVocab()">
          <option value="all">Tất cả trạng thái</option>
          <option value="0">Chưa học</option>
          <option value="1">Đang học</option>
          <option value="2">Đã nhớ</option>
        </select>
        <button class="btn secondary sm" onclick="goPage('upload')">⬆️ Import</button>
      </div>
      <div id="vocab-grid" class="vocab-grid"></div>
    </div>

    <!-- ═══════════════ FLASHCARD PAGE ═══════════════ -->
    <div id="page-flashcard" class="page">
      <div class="page-header">
        <h2>🃏 Flashcard</h2>
        <p>Nhấn vào thẻ để lật – luyện trí nhớ theo Spaced Repetition</p>
      </div>
      <div id="fc-setup" class="card">
        <div style="display:flex;gap:10px;flex-wrap:wrap;align-items:center;">
          <select id="fc-filter" style="background:var(--surface2);border:1px solid var(--border);border-radius:var(--radius-sm);color:var(--text);padding:8px 12px;font-size:13px;outline:none;">
            <option value="all">Tất cả từ vựng</option>
            <option value="0">Chưa học</option>
            <option value="1">Đang học</option>
            <option value="2">Đã nhớ</option>
          </select>
          <button class="btn" onclick="startFlashcard()">▶ Bắt đầu</button>
        </div>
      </div>
      <div id="fc-game" style="display:none;">
        <div class="fc-info">
          <span id="fc-counter">0 / 0</span>
          <span id="fc-score-display">✅ 0 &nbsp; ❌ 0</span>
        </div>
        <div class="progress-bar"><div class="progress-fill" id="fc-progress"></div></div>
        <div class="flashcard-wrap">
          <div class="flashcard" id="flashcard" onclick="flipCard()">
            <div class="fc-front">
              <div class="fc-word" id="fc-word"></div>
              <div class="fc-ipa" id="fc-ipa"></div>
              <div class="fc-hint">Nhấn để xem nghĩa</div>
            </div>
            <div class="fc-back">
              <div class="fc-definition" id="fc-def"></div>
              <div class="fc-example" id="fc-ex"></div>
            </div>
          </div>
        </div>
        <div class="fc-controls">
          <button class="btn danger" onclick="rateCard(0)">❌ Chưa nhớ</button>
          <button class="btn secondary" onclick="speakWord()">🔊 Phát âm</button>
          <button class="btn success" onclick="rateCard(1)">✅ Đã nhớ</button>
        </div>
        <div style="text-align:center;margin-top:16px;">
          <button class="btn secondary sm" onclick="endFlashcard()">🔚 Kết thúc</button>
        </div>
      </div>
    </div>

    <!-- ═══════════════ QUIZ PAGE ═══════════════ -->
    <div id="page-quiz" class="page">
      <div class="page-header">
        <h2>✍️ Trắc nghiệm & Điền từ</h2>
        <p>Kiểm tra kiến thức theo 2 dạng câu hỏi</p>
      </div>
      <div id="quiz-setup" class="card">
        <div style="margin-bottom:14px;">
          <div class="reading-label">Dạng câu hỏi</div>
          <div class="quiz-mode-switch">
            <div class="mode-btn active" id="qm-mc" onclick="setQuizMode('mc')">🔘 Trắc nghiệm 4 đáp án</div>
            <div class="mode-btn" id="qm-fill" onclick="setQuizMode('fill')">✏️ Điền từ</div>
          </div>
        </div>
        <div>
          <div class="reading-label">Chiều hỏi</div>
          <div class="answer-dir">
            <div class="dir-btn active" id="dir-en-vi" onclick="setQuizDir('en-vi')">🇬🇧 Hỏi Tiếng Anh → 🇻🇳 Trả lời Tiếng Việt</div>
            <div class="dir-btn" id="dir-vi-en" onclick="setQuizDir('vi-en')">🇻🇳 Hỏi Tiếng Việt → 🇬🇧 Trả lời Tiếng Anh</div>
          </div>
        </div>
        <div style="margin-top:14px;display:flex;gap:10px;align-items:center;">
          <label style="font-size:13px;color:var(--text2);">Số câu:</label>
          <input type="number" id="quiz-count" value="10" min="5" max="50" style="width:70px;background:var(--surface2);border:1px solid var(--border);border-radius:6px;color:var(--text);padding:6px 10px;font-size:13px;outline:none;">
          <button class="btn" onclick="startQuiz()">▶ Bắt đầu kiểm tra</button>
        </div>
      </div>
      <div id="quiz-game" style="display:none;">
        <div class="quiz-wrap">
          <div class="fc-info">
            <span id="quiz-counter">Câu 0/0</span>
            <span id="quiz-score-disp">Điểm: 0</span>
          </div>
          <div class="progress-bar"><div class="progress-fill" id="quiz-progress"></div></div>
          <div class="quiz-question" id="quiz-q"></div>
          <div id="quiz-mc-opts" class="quiz-options"></div>
          <div id="quiz-fill-area" class="quiz-fill" style="display:none;">
            <input type="text" id="quiz-fill-input" placeholder="Nhập câu trả lời..." onkeydown="if(event.key==='Enter')checkFill()">
            <button class="btn" onclick="checkFill()">Kiểm tra</button>
          </div>
          <div id="quiz-feedback" class="quiz-feedback" style="display:none;"></div>
          <div style="display:flex;gap:10px;margin-top:10px;">
            <button class="btn secondary" id="quiz-audio-btn" onclick="speakCurrent()">🔊</button>
            <button class="btn" id="quiz-next-btn" onclick="nextQuiz()" style="display:none;">Câu tiếp →</button>
          </div>
        </div>
      </div>
      <div id="quiz-result" style="display:none;">
        <div class="quiz-score">
          <div class="big" id="quiz-final-score"></div>
          <div class="label" id="quiz-final-label"></div>
          <button class="btn" onclick="startQuiz()">🔄 Làm lại</button>
          &nbsp;
          <button class="btn secondary" onclick="backToQuizSetup()">⚙️ Cài đặt</button>
        </div>
      </div>
    </div>

    <!-- ═══════════════ READING PAGE ═══════════════ -->
    <div id="page-reading" class="page">
      <div class="page-header">
        <h2>📖 Đọc hiểu</h2>
        <p>Điền từ vào đoạn văn dựa theo ngữ cảnh</p>
      </div>
      <div id="reading-select" class="card">
        <div class="reading-label">Chọn bài đọc</div>
        <div id="reading-list" style="display:flex;flex-direction:column;gap:8px;margin-top:8px;"></div>
      </div>
      <div id="reading-game" style="display:none;">
        <div class="reading-wrap">
          <div style="margin-bottom:16px;">
            <div class="reading-label">Danh sách từ vựng</div>
            <div id="reading-wordbank" class="word-bank"></div>
          </div>
          <div class="reading-label">Bài đọc – Điền từ thích hợp</div>
          <div class="reading-passage" id="reading-passage"></div>
          <div style="display:flex;gap:10px;flex-wrap:wrap;">
            <button class="btn success" onclick="checkReading()">✅ Kiểm tra</button>
            <button class="btn secondary" onclick="resetReading()">🔄 Làm lại</button>
            <button class="btn secondary" onclick="showReadingHint()">💡 Gợi ý</button>
            <button class="btn secondary" onclick="backToReadingSelect()">← Chọn bài khác</button>
          </div>
          <div id="reading-result" style="display:none;margin-top:16px;"></div>
        </div>
      </div>
    </div>

    <!-- ═══════════════ CHINESE PAGE ═══════════════ -->
    <div id="page-chinese" class="page">
      <div class="page-header">
        <h2>🈶 Tiếng Trung – Hán tự & Nét chữ</h2>
        <p>Học nét chữ với HanziWriter animation</p>
      </div>
      <div class="filter-bar">
        <input type="text" id="zh-search" placeholder="🔍 Tìm Hán tự..." oninput="renderChinese()">
        <button class="btn secondary sm" onclick="goPage('upload')">⬆️ Import từ vựng Trung</button>
      </div>
      <div id="hanzi-grid" class="hanzi-grid"></div>
    </div>

    <!-- ═══════════════ FAVORITES PAGE ═══════════════ -->
    <div id="page-favorites" class="page">
      <div class="page-header">
        <h2>⭐ Từ yêu thích</h2>
        <p>Những từ bạn đã đánh dấu để ôn luyện</p>
      </div>
      <div id="fav-grid" class="vocab-grid"></div>
    </div>

    <!-- ═══════════════ UPLOAD PAGE ═══════════════ -->
    <div id="page-upload" class="page">
      <div class="page-header">
        <h2>⬆️ Import dữ liệu</h2>
        <p>Tải lên file từ vựng JSON hoặc CSV</p>
      </div>
      <div class="card">
        <div class="reading-label">File JSON – Từ vựng Tiếng Anh</div>
        <p style="font-size:13px;color:var(--text2);margin-bottom:12px;">Định dạng: mảng các object với các trường: <code style="color:var(--accent);">word, ipa, definition, example, synonyms, antonyms, status</code></p>
        <div class="upload-zone" onclick="document.getElementById('json-upload').click()">
          📂 Nhấn để chọn file JSON hoặc kéo thả vào đây
          <input type="file" id="json-upload" accept=".json" style="display:none;" onchange="importJSON(event)">
        </div>
        <div id="import-status" style="margin-top:12px;font-size:13.5px;color:var(--accent2);display:none;"></div>
      </div>
      <div class="card">
        <div class="reading-label">File JSON – Từ vựng Tiếng Trung</div>
        <p style="font-size:13px;color:var(--text2);margin-bottom:12px;">Định dạng: mảng với trường: <code style="color:var(--accent);">hanzi, pinyin, meaning, example, level</code></p>
        <div class="upload-zone" onclick="document.getElementById('json-zh-upload').click()">
          📂 Nhấn để chọn file JSON Tiếng Trung
          <input type="file" id="json-zh-upload" accept=".json" style="display:none;" onchange="importChineseJSON(event)">
        </div>
      </div>
      <div class="card">
        <div class="reading-label">Mẫu JSON tham khảo – Tiếng Anh</div>
        <pre style="background:var(--surface2);padding:14px;border-radius:8px;font-size:12px;color:var(--accent2);overflow-x:auto;">[
  {
    "word": "perseverance",
    "ipa": "/ˌpərsəˈvɪrəns/",
    "definition": "Kiên trì; sự cố gắng liên tục dù gặp khó khăn",
    "definitionEN": "Continued effort to achieve despite difficulties",
    "example": "She showed great perseverance in learning English every day.",
    "synonyms": "Persistence, Tenacity",
    "antonyms": "Laziness, Giving up",
    "status": 0,
    "favorite": false
  }
]</pre>
        <button class="btn secondary sm" style="margin-top:10px;" onclick="downloadSample()">⬇️ Tải file mẫu</button>
      </div>
    </div>

  </main>
</div>

<!-- HanziWriter CDN -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/hanzi-writer/3.5.2/hanzi-writer.min.js"></script>

<script>
// ═══════════════════════════════════════════════════════
//  DATA STORE
// ═══════════════════════════════════════════════════════
let vocabData = [];
let chineseData = [];
let favorites = new Set();
let statusMap = {};  // word -> 0,1,2

// Pre-loaded vocabulary from the user's PDF file
const BUILT_IN_VOCAB = [
  { word: "Perseverance", ipa: "/ˌpərsəˈvɪrəns/", definition: "Kiên trì; sự cố gắng liên tục dù gặp khó khăn", definitionEN: "Continued effort to do or achieve something despite difficulties", example: "She showed great perseverance in learning English every day.", synonyms: "Persistence, Tenacity", antonyms: "Laziness, Giving up", status: 1 },
  { word: "Eloquent", ipa: "/ˈɛləkwənt/", definition: "Hùng hồn, diễn đạt lưu loát và thuyết phục", definitionEN: "Fluent or persuasive in speaking or writing", example: "The speaker was so eloquent that everyone listened attentively.", synonyms: "Articulate, Fluent", antonyms: "Inarticulate, Tongue-tied", status: 3 },
  { word: "Ambiguous", ipa: "/æmˈbɪgjuəs/", definition: "Mơ hồ, có thể hiểu theo nhiều cách", definitionEN: "Open to more than one interpretation; not having one obvious meaning", example: "The instructions were ambiguous and confused the students.", synonyms: "Unclear, Vague", antonyms: "Clear, Explicit", status: 2 },
  { word: "Resilient", ipa: "/rɪˈzɪljənt/", definition: "Kiên cường; có khả năng phục hồi nhanh từ khó khăn", definitionEN: "Able to recover quickly from difficult conditions", example: "Children are often more resilient than adults think.", synonyms: "Tough, Adaptable", antonyms: "Fragile, Vulnerable", status: 2 },
  { word: "Preliminary", ipa: "/prɪˈlɪməˌnɛri/", definition: "Sơ bộ; diễn ra trước sự việc quan trọng hơn", definitionEN: "Coming before a more important action or event", example: "The team underwent preliminary tests before the main competition started.", synonyms: "Initial, Preparatory", antonyms: "Final, Closing", status: 5 },
  { word: "Trivial", ipa: "/ˈtrɪviəl/", definition: "Tầm thường, không quan trọng", definitionEN: "Of little importance or value", example: "The mistake was so trivial that it didn't affect the final result.", synonyms: "Insignificant, Minor", antonyms: "Important, Significant", status: 2 },
  { word: "Culprit", ipa: "/ˈkəlprɪt/", definition: "Thủ phạm, người gây ra tội lỗi hoặc vấn đề", definitionEN: "A person responsible for a crime or problem", example: "The police finally found the culprit behind the robbery.", synonyms: "Offender, Criminal", antonyms: "Victim, Innocent", status: 2 },
  { word: "Mobilize", ipa: "/ˈmoʊbəˌlaɪz/", definition: "Huy động, tổ chức và chuẩn bị cho hành động", definitionEN: "To organize and prepare for action", example: "The government mobilized resources to deal with the natural disaster.", synonyms: "Organize, Deploy", antonyms: "Demobilize, Disperse", status: 2 },
  { word: "Topple", ipa: "/ˈtɑpəl/", definition: "Lật đổ; rơi hoặc làm rơi", definitionEN: "To fall or cause something to fall", example: "The strong winds toppled several trees in the area.", synonyms: "Overthrow, Knock down", antonyms: "Stabilize, Support", status: 2 },
  { word: "Frayed", ipa: "/freɪd/", definition: "Sờn, căng thẳng; bị mòn hoặc mỏi mệt", definitionEN: "Worn out or strained", example: "After a long argument, their patience was completely frayed.", synonyms: "Worn, Strained", antonyms: "Fresh, Calm", status: 2 },
  { word: "Piled up", ipa: "/paɪld əp/", definition: "Chồng chất, tích lũy với số lượng lớn", definitionEN: "To accumulate in large amounts", example: "Work has piled up while I was on vacation.", synonyms: "Accumulate, Build up", antonyms: "Decrease, Reduce", status: 2 },
  { word: "Intact", ipa: "/ɪnˈtækt/", definition: "Nguyên vẹn; không bị hư hại hoặc thay đổi", definitionEN: "Not damaged or altered", example: "The ancient building remained intact after the earthquake.", synonyms: "Undamaged, Whole", antonyms: "Damaged, Broken", status: 2 },
  { word: "Ruminate", ipa: "/ˈrumɪˌneɪt/", definition: "Suy ngẫm sâu; nhai lại (nghĩa bóng là ngẫm nghĩ)", definitionEN: "Think deeply about something", example: "He tends to ruminate on past mistakes instead of moving forward.", synonyms: "Contemplate, Ponder", antonyms: "Ignore, Disregard", status: 2 },
  { word: "Erratic", ipa: "/ɪˈrætɪk/", definition: "Thất thường; không thể đoán trước hoặc nhất quán", definitionEN: "Unpredictable or inconsistent", example: "His erratic behavior made everyone uncomfortable.", synonyms: "Unpredictable, Irregular", antonyms: "Stable, Consistent", status: 2 },
  { word: "Endowment", ipa: "/ɛnˈdaʊmənt/", definition: "Quỹ tài trợ; năng khiếu tự nhiên", definitionEN: "A donation or natural gift", example: "The university received a large endowment to support research.", synonyms: "Fund, Grant", antonyms: "Lack, Deficiency", status: 2 },
  { word: "Upheaval", ipa: "/əˈphivəlz/", definition: "Biến động lớn; sự xáo trộn hoặc thay đổi triệt để", definitionEN: "Great changes or disturbances that cause problems", example: "The company underwent massive financial upheavals during the recession.", synonyms: "Turmoil, Disruption", antonyms: "Stability, Order", status: 2 },
  { word: "Contemplation", ipa: "/ˌkɑntəmˈpleɪʃən/", definition: "Sự suy ngẫm, trầm tư; cân nhắc kỹ lưỡng", definitionEN: "Deep reflective thought", example: "She spent hours in deep contemplation before making her final decision.", synonyms: "Meditation, Reflection", antonyms: "Ignorance, Neglect", status: 1 },
  { word: "Robust", ipa: "/roʊˈbəst/", definition: "Mạnh mẽ, kiên cố, vững vàng", definitionEN: "Strong and healthy; vigorous; sturdy in construction", example: "The company developed a robust economic model to withstand market crashes.", synonyms: "Strong, Sturdy, Resilient", antonyms: "Weak, Fragile", status: 1 },
  { word: "Bribe", ipa: "/braɪb/", definition: "Hối lộ, đút lót", definitionEN: "Dishonestly persuade someone by a gift of money", example: "He was accused of offering a bribe to a government official.", synonyms: "Payoff, Kickback, Induce", antonyms: "N/A", status: 1 },
  { word: "Unconventional", ipa: "/ˌənkənˈvɛnʃənəl/", definition: "Độc đáo, trái với thông thường, không theo quy lối cũ", definitionEN: "Not conforming to what is generally done or believed", example: "She is known for her unconventional teaching methods that students love.", synonyms: "Unusual, Unorthodox", antonyms: "Conventional, Traditional", status: 1 },
  { word: "Fraud", ipa: "/frɔd/", definition: "Sự gian lận, lừa gạt", definitionEN: "Wrongful deception intended to result in financial gain", example: "The investment scheme turned out to be a massive corporate fraud.", synonyms: "Deception, Scam, Trickery", antonyms: "Honesty, Integrity", status: 1 },
  { word: "Embezzle", ipa: "/ɪmˈbɛzəlɪŋ/", definition: "Tham ô, biển thủ công quỹ", definitionEN: "Stealing or misappropriating money placed in one's trust", example: "The accountant was arrested for embezzling millions from the charity fund.", synonyms: "Misappropriating, Stealing", antonyms: "Returning, Compensating", status: 1 },
  { word: "Patron", ipa: "/ˈpeɪtrən/", definition: "Nhà tài trợ, khách hàng quen, hộ pháp", definitionEN: "A person who gives financial or other support to a cause", example: "The college art museum is looking for new patrons.", synonyms: "Sponsor, Backer, Supporter", antonyms: "Opponent, Detractor", status: 1 },
  { word: "Trajectory", ipa: "/trəˈdʒektəri/", definition: "Quỹ đạo, đường đạn", definitionEN: "The path followed by an object moving under forces", example: "The missile's trajectory was constantly tracked by radar.", synonyms: "Path, Course, Route", antonyms: "N/A", status: 1 },
  { word: "Astray", ipa: "/əˈstreɪ/", definition: "Lạc đường, lầm đường lạc lối", definitionEN: "Away from the correct path or direction", example: "The letter must have gone astray in the post.", synonyms: "Lost, Off track, Wandering", antonyms: "On track, Right", status: 1 },
  { word: "Inferior", ipa: "/ɪnˈfɪəriər/", definition: "Kém hơn, hạ đẳng, cấp dưới", definitionEN: "Lower in rank, status, or quality", example: "His later work was vastly inferior to his early masterpieces.", synonyms: "Substandard, Second-rate", antonyms: "Superior, Better", status: 1 },
  { word: "Recalibrate", ipa: "/ˌriːˈkælɪbreɪtɪd/", definition: "Hiệu chỉnh lại, điều chỉnh lại thông số", definitionEN: "Calibrate or adjust an instrument again", example: "The scientists recalibrated the laboratory equipment before the experiment.", synonyms: "Readjust, Fine-tune", antonyms: "Desensitize", status: 1 },
  { word: "Dabble", ipa: "/ˈdæbəl/", definition: "Học đòi, làm chơi bời; tham gia hời hợt", definitionEN: "Take part in an activity in a casual or superficial way", example: "I try everything and I'll just dabble my feet into certain things.", synonyms: "Tinker, Flirt with", antonyms: "Take seriously, Dedicate", status: 1 },
  { word: "Demeanor", ipa: "/dɪˈmiːnər/", definition: "Thái độ, cách cư xử, diện mạo", definitionEN: "Outward behavior or bearing; attitude", example: "She has the calm demeanor of a natural leader.", synonyms: "Behavior, Manner, Conduct", antonyms: "N/A", status: 1 },
  { word: "Dismantle", ipa: "/dɪsˈmæntl/", definition: "Tháo dỡ (máy móc), phá hủy", definitionEN: "Take a machine or structure to pieces", example: "The mechanic had to dismantle the engine to find the problem.", synonyms: "Take apart, Disassemble", antonyms: "Assemble, Build, Construct", status: 1 },
  { word: "Emulate", ipa: "/ˈemjuleɪt/", definition: "Tranh đua, bắt chước; cố vượt qua ai đó", definitionEN: "Match or surpass, typically by imitation", example: "They hope to emulate the success of other software companies.", synonyms: "Imitate, Copy, Rival", antonyms: "Neglect, Ignore", status: 1 },
  { word: "Indebted", ipa: "/ɪnˈdetɪd/", definition: "Mắc nợ, mang ơn, biết ơn", definitionEN: "Owing money or gratitude for a service", example: "Giving that envelope to you, you feel indebted to them.", synonyms: "Grateful, Obligated", antonyms: "Ungrateful, Unthankful", status: 1 },
  { word: "Psychotic", ipa: "/saɪˈkɒtɪk/", definition: "Mắc bệnh tâm thần phân liệt, loạn thần", definitionEN: "Relating to psychosis; having a severe mental disorder", example: "He showed psychotic symptoms after the trauma.", synonyms: "Deranged, Disturbed", antonyms: "Sane, Rational", status: 0 },
  { word: "Bane", ipa: "/beɪn/", definition: "Nguyên nhân của sự phiền não hoặc bất hạnh", definitionEN: "A cause of great distress or annoyance", example: "Mosquitoes are the bane of summer evenings.", synonyms: "Curse, Affliction, Plague", antonyms: "Blessing, Benefit", status: 0 },
  { word: "Endeavor", ipa: "/ɪnˈdevər/", definition: "Nỗ lực, cố gắng; công việc hoặc dự án nghiêm túc", definitionEN: "An attempt to achieve a goal; earnest work", example: "Space exploration is humanity's greatest endeavor.", synonyms: "Effort, Attempt, Undertaking", antonyms: "Laziness, Inactivity", status: 0 },
  { word: "Revival", ipa: "/rɪˈvaɪvəl/", definition: "Sự hồi sinh, sự khôi phục lại", definitionEN: "Improvement in condition, strength, or popularity", example: "The city saw a cultural revival in the arts district.", synonyms: "Resurgence, Renewal, Renaissance", antonyms: "Decline, Downfall", status: 0 },
  { word: "Sentiment", ipa: "/ˈsɛntɪmənt/", definition: "Tình cảm, cảm xúc, quan điểm", definitionEN: "A view or opinion held based on feeling", example: "Public sentiment shifted quickly after the announcement.", synonyms: "Feeling, Emotion, Opinion", antonyms: "Indifference, Apathy", status: 0 },
  { word: "Subdued", ipa: "/səbˈdjuːd/", definition: "Yên lặng, trầm lắng; kém mạnh mẽ hơn bình thường", definitionEN: "Quiet and reflective; lacking in intensity", example: "After the loss, the team was noticeably subdued.", synonyms: "Quiet, Muted, Restrained", antonyms: "Exuberant, Lively", status: 0 },
  { word: "Hostage", ipa: "/ˈhɒstɪdʒ/", definition: "Con tin", definitionEN: "A person held as security for the fulfillment of a condition", example: "The kidnappers demanded ransom for the hostage.", synonyms: "Captive, Prisoner", antonyms: "Captor, Liberator", status: 0 },
  { word: "Redact", ipa: "/rɪˈdækt/", definition: "Biên tập; che giấu thông tin nhạy cảm", definitionEN: "Edit text for publication by removing sensitive parts", example: "The classified documents were heavily redacted before release.", synonyms: "Censor, Edit, Expunge", antonyms: "Reveal, Disclose", status: 0 },
  { word: "Accommodate", ipa: "/əˈkɒmədeɪt/", definition: "Chứa chỗ; thích nghi; đáp ứng nhu cầu của ai", definitionEN: "Provide space or adapt to needs", example: "The hotel can accommodate up to 300 guests.", synonyms: "House, Adapt, Contain", antonyms: "Exclude, Reject", status: 0 },
  { word: "Disposable", ipa: "/dɪˈspoʊzəbl/", definition: "Dùng một lần; có thể bỏ đi; thu nhập khả dụng", definitionEN: "Designed to be used once then discarded", example: "The café switched from disposable cups to reusable ones.", synonyms: "Throwaway, Single-use", antonyms: "Reusable, Permanent", status: 0 },
  { word: "Hijack", ipa: "/ˈhaɪdʒæk/", definition: "Chiếm đoạt; cướp (máy bay, xe); lấy lại quyền kiểm soát", definitionEN: "Illegally seize control of a vehicle or redirect for one's own purpose", example: "Hackers hijacked the social media accounts.", synonyms: "Seize, Commandeer, Usurp", antonyms: "Surrender, Release", status: 0 },
  { word: "Synthetic", ipa: "/sɪnˈθɛtɪk/", definition: "Tổng hợp, nhân tạo; không tự nhiên", definitionEN: "Made by chemical synthesis rather than natural processes", example: "Synthetic fibers are more durable than natural ones.", synonyms: "Artificial, Man-made", antonyms: "Natural, Organic", status: 0 },
  { word: "Potent", ipa: "/ˈpoʊtənt/", definition: "Mạnh mẽ, hiệu lực cao; có ảnh hưởng lớn", definitionEN: "Having great power, influence, or effect", example: "The new drug is far more potent than previous treatments.", synonyms: "Powerful, Effective, Strong", antonyms: "Weak, Impotent", status: 0 },
  { word: "Lucrative", ipa: "/ˈluːkrətɪv/", definition: "Sinh lời, mang lại nhiều lợi nhuận", definitionEN: "Producing a great deal of profit", example: "The tech startup proved to be extremely lucrative.", synonyms: "Profitable, Rewarding", antonyms: "Unprofitable, Costly", status: 0 },
  { word: "Integrity", ipa: "/ɪnˈtɛgrɪti/", definition: "Sự chính trực, liêm chính; tính toàn vẹn", definitionEN: "The quality of being honest and having strong moral principles", example: "Her integrity made her the most trusted person on the team.", synonyms: "Honesty, Uprightness", antonyms: "Dishonesty, Corruption", status: 0 },
  { word: "Insidious", ipa: "/ɪnˈsɪdiəs/", definition: "Thâm hiểm, xảo quyệt; nguy hiểm theo cách tinh vi", definitionEN: "Proceeding in a gradual, subtle but harmful way", example: "The insidious spread of misinformation threatened democracy.", synonyms: "Sneaky, Treacherous, Subtle", antonyms: "Harmless, Benign", status: 0 },
  { word: "Susceptible", ipa: "/səˈsɛptɪbl/", definition: "Dễ bị tổn thương; dễ bị ảnh hưởng bởi", definitionEN: "Likely to be influenced or harmed by a particular thing", example: "Young children are more susceptible to infections.", synonyms: "Vulnerable, Prone, Sensitive", antonyms: "Resistant, Immune", status: 0 },
  { word: "Stipulate", ipa: "/ˈstɪpjuleɪt/", definition: "Quy định rõ ràng, điều kiện hóa; nêu như điều kiện", definitionEN: "Demand or specify as part of an agreement", example: "The contract stipulated that payment was due within 30 days.", synonyms: "Specify, Require, Mandate", antonyms: "Allow, Permit", status: 0 },
  { word: "Abstain", ipa: "/əbˈsteɪn/", definition: "Kiêng, tự kiềm chế; bỏ phiếu trắng", definitionEN: "Restrain oneself from doing or enjoying something", example: "He chose to abstain from alcohol during the competition.", synonyms: "Refrain, Withhold, Forbear", antonyms: "Indulge, Partake", status: 0 },
  { word: "Anhedonia", ipa: "/ˌænhɪˈdoʊniə/", definition: "Mất khả năng cảm nhận niềm vui", definitionEN: "The inability to feel pleasure in normally pleasurable activities", example: "Depression often causes anhedonia in patients.", synonyms: "Joylessness, Apathy", antonyms: "Pleasure, Enjoyment", status: 0 },
];

// Built-in Chinese vocabulary
const BUILT_IN_CHINESE = [
  { hanzi: "学习", pinyin: "xuéxí", meaning: "Học tập / to study", example: "我每天都在学习。", level: 1 },
  { hanzi: "朋友", pinyin: "péngyou", meaning: "Bạn bè / friend", example: "他是我的好朋友。", level: 1 },
  { hanzi: "工作", pinyin: "gōngzuò", meaning: "Công việc / work", example: "我喜欢我的工作。", level: 1 },
  { hanzi: "语言", pinyin: "yǔyán", meaning: "Ngôn ngữ / language", example: "汉语是一门美丽的语言。", level: 2 },
  { hanzi: "知识", pinyin: "zhīshi", meaning: "Kiến thức / knowledge", example: "知识就是力量。", level: 2 },
  { hanzi: "机会", pinyin: "jīhuì", meaning: "Cơ hội / opportunity", example: "不要错过这个好机会。", level: 2 },
  { hanzi: "努力", pinyin: "nǔlì", meaning: "Nỗ lực / to work hard", example: "他很努力地学习中文。", level: 1 },
  { hanzi: "成功", pinyin: "chénggōng", meaning: "Thành công / success", example: "成功来自努力。", level: 2 },
  { hanzi: "梦想", pinyin: "mèngxiǎng", meaning: "Ước mơ / dream", example: "我的梦想是环游世界。", level: 2 },
  { hanzi: "进步", pinyin: "jìnbù", meaning: "Tiến bộ / progress", example: "她的汉语进步很快。", level: 2 },
  { hanzi: "经验", pinyin: "jīngyàn", meaning: "Kinh nghiệm / experience", example: "工作经验很重要。", level: 3 },
  { hanzi: "挑战", pinyin: "tiǎozhàn", meaning: "Thách thức / challenge", example: "学习语言是一个挑战。", level: 3 },
];

// ═══════════════════════════════════════════════════════
//  INIT
// ═══════════════════════════════════════════════════════
function init() {
  // Load from localStorage
  const saved = localStorage.getItem('vm_vocab');
  if (saved) {
    vocabData = JSON.parse(saved);
  } else {
    vocabData = BUILT_IN_VOCAB.map((v, i) => ({ ...v, id: i, favorite: false, status: v.status || 0 }));
    saveVocab();
  }
  const savedZh = localStorage.getItem('vm_chinese');
  if (savedZh) {
    chineseData = JSON.parse(savedZh);
  } else {
    chineseData = BUILT_IN_CHINESE.map((v, i) => ({ ...v, id: i }));
    saveChinese();
  }
  const savedFavs = localStorage.getItem('vm_favs');
  if (savedFavs) favorites = new Set(JSON.parse(savedFavs));
  updateStats();
  renderVocab();
  initReadingPassages();
}

function saveVocab() { localStorage.setItem('vm_vocab', JSON.stringify(vocabData)); }
function saveChinese() { localStorage.setItem('vm_chinese', JSON.stringify(chineseData)); }
function saveFavs() { localStorage.setItem('vm_favs', JSON.stringify([...favorites])); }

function updateStats() {
  const c = [0, 0, 0, 0, 0, 0];
  vocabData.forEach(v => {
    if (v.status === 0) c[0]++;
    else if (v.status <= 2) c[1]++;
    else c[2]++;
  });
  document.getElementById('st-new').textContent = c[0];
  document.getElementById('st-learning').textContent = c[1];
  document.getElementById('st-known').textContent = c[2];
  document.getElementById('sb-new').textContent = c[0];
  document.getElementById('sb-learning').textContent = c[1];
  document.getElementById('sb-known').textContent = c[2];
}

// ═══════════════════════════════════════════════════════
//  NAVIGATION
// ═══════════════════════════════════════════════════════
function goPage(name) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  document.getElementById('page-' + name).classList.add('active');
  document.querySelectorAll('.nav-item').forEach(n => {
    if (n.getAttribute('onclick') && n.getAttribute('onclick').includes("'" + name + "'")) n.classList.add('active');
  });
  // Page-specific refresh
  if (name === 'vocab') renderVocab();
  if (name === 'favorites') renderFavorites();
  if (name === 'chinese') renderChinese();
  if (name === 'reading') renderReadingSelect();
  // Close sidebar on mobile
  document.getElementById('sidebar').classList.remove('open');
}

function toggleSidebar() { document.getElementById('sidebar').classList.toggle('open'); }

// ═══════════════════════════════════════════════════════
//  VOCAB LIST
// ═══════════════════════════════════════════════════════
function renderVocab() {
  const q = document.getElementById('search-input').value.toLowerCase();
  const sf = document.getElementById('filter-status').value;
  let filtered = vocabData.filter(v => {
    const matchQ = !q || v.word.toLowerCase().includes(q) || v.definition.toLowerCase().includes(q);
    const matchS = sf === 'all' || (sf === '0' && v.status === 0) || (sf === '1' && v.status >= 1 && v.status <= 2) || (sf === '2' && v.status >= 3);
    return matchQ && matchS;
  });
  const grid = document.getElementById('vocab-grid');
  if (!filtered.length) { grid.innerHTML = '<div class="empty">Không tìm thấy từ nào</div>'; return; }
  grid.innerHTML = filtered.map(v => vocabCardHTML(v)).join('');
  updateStats();
}

function vocabCardHTML(v) {
  const statusLabel = ['Chưa học', 'Đang học', 'Đang học', 'Đã nhớ', 'Đã nhớ', 'Cần ôn'][Math.min(v.status, 5)] || 'Chưa học';
  const statusClass = v.status === 0 ? 0 : v.status <= 2 ? 1 : 2;
  const isFav = favorites.has(v.word);
  return `<div class="vocab-card">
    <button class="fav-btn ${isFav ? 'active' : ''}" onclick="toggleFav('${v.word}')" title="Đánh dấu yêu thích">${isFav ? '⭐' : '☆'}</button>
    <div style="display:flex;align-items:center;gap:8px;">
      <div class="word">${v.word}</div>
      <button class="audio-btn" onclick="speak('${v.word.replace(/'/g,"\\'")}','en-US')" title="Phát âm">🔊</button>
    </div>
    <div class="ipa">${v.ipa || ''}</div>
    <div class="definition">${v.definition}</div>
    ${v.definitionEN ? `<div class="definition" style="color:var(--text3);font-size:12px;margin-top:3px;">${v.definitionEN}</div>` : ''}
    ${v.example ? `<div class="example">${v.example}</div>` : ''}
    ${v.synonyms ? `<div class="synonyms">↔️ ${v.synonyms}${v.antonyms && v.antonyms !== 'N/A' ? ' | ⚡ ' + v.antonyms : ''}</div>` : ''}
    <div class="tags"><span class="tag status-${statusClass}">${statusLabel}</span></div>
    <div class="status-btns">
      <div class="status-btn ${v.status === 0 ? 'active-0' : ''}" onclick="setStatus('${v.word}', 0)">⏳ Mới</div>
      <div class="status-btn ${v.status >= 1 && v.status <= 2 ? 'active-1' : ''}" onclick="setStatus('${v.word}', 1)">📝 Học</div>
      <div class="status-btn ${v.status >= 3 ? 'active-2' : ''}" onclick="setStatus('${v.word}', 3)">✅ Nhớ</div>
    </div>
  </div>`;
}

function setStatus(word, s) {
  const v = vocabData.find(x => x.word === word);
  if (v) { v.status = s; saveVocab(); renderVocab(); updateStats(); }
}

function toggleFav(word) {
  if (favorites.has(word)) favorites.delete(word);
  else favorites.add(word);
  saveFavs();
  renderVocab();
}

function renderFavorites() {
  const grid = document.getElementById('fav-grid');
  const favs = vocabData.filter(v => favorites.has(v.word));
  if (!favs.length) { grid.innerHTML = '<div class="empty">Chưa có từ yêu thích nào.<br>Nhấn ☆ trên thẻ từ để thêm.</div>'; return; }
  grid.innerHTML = favs.map(v => vocabCardHTML(v)).join('');
}

// ═══════════════════════════════════════════════════════
//  AUDIO / TTS
// ═══════════════════════════════════════════════════════
function speak(text, lang = 'en-US') {
  if (!window.speechSynthesis) return;
  window.speechSynthesis.cancel();
  const u = new SpeechSynthesisUtterance(text);
  u.lang = lang; u.rate = 0.85;
  const voices = window.speechSynthesis.getVoices();
  const v = voices.find(x => x.lang === lang) || voices.find(x => x.lang.startsWith(lang.split('-')[0]));
  if (v) u.voice = v;
  window.speechSynthesis.speak(u);
}

// ═══════════════════════════════════════════════════════
//  FLASHCARD
// ═══════════════════════════════════════════════════════
let fcDeck = [], fcIdx = 0, fcCorrect = 0, fcWrong = 0, fcFlipped = false;

function startFlashcard() {
  const filter = document.getElementById('fc-filter').value;
  fcDeck = vocabData.filter(v => filter === 'all' || (filter === '0' && v.status === 0) || (filter === '1' && v.status >= 1 && v.status <= 2) || (filter === '2' && v.status >= 3));
  fcDeck = shuffle([...fcDeck]);
  if (!fcDeck.length) { alert('Không có từ nào phù hợp!'); return; }
  fcIdx = 0; fcCorrect = 0; fcWrong = 0;
  document.getElementById('fc-setup').style.display = 'none';
  document.getElementById('fc-game').style.display = 'block';
  showFC();
}

function showFC() {
  if (fcIdx >= fcDeck.length) { endFlashcard(true); return; }
  const v = fcDeck[fcIdx];
  document.getElementById('fc-word').textContent = v.word;
  document.getElementById('fc-ipa').textContent = v.ipa || '';
  document.getElementById('fc-def').textContent = v.definition;
  document.getElementById('fc-ex').textContent = v.example || '';
  document.getElementById('fc-counter').textContent = `${fcIdx + 1} / ${fcDeck.length}`;
  document.getElementById('fc-score-display').textContent = `✅ ${fcCorrect}   ❌ ${fcWrong}`;
  document.getElementById('fc-progress').style.width = ((fcIdx / fcDeck.length) * 100) + '%';
  const card = document.getElementById('flashcard');
  card.classList.remove('flipped'); fcFlipped = false;
}

function flipCard() {
  document.getElementById('flashcard').classList.toggle('flipped');
  fcFlipped = !fcFlipped;
  if (fcFlipped) speak(fcDeck[fcIdx]?.word || '', 'en-US');
}

function rateCard(correct) {
  if (correct) { fcCorrect++; setStatus(fcDeck[fcIdx].word, Math.min((fcDeck[fcIdx].status || 0) + 1, 5)); }
  else fcWrong++;
  fcIdx++;
  showFC();
}

function speakWord() { speak(fcDeck[fcIdx]?.word || '', 'en-US'); }

function endFlashcard(finished) {
  document.getElementById('fc-setup').style.display = 'block';
  document.getElementById('fc-game').style.display = 'none';
  if (finished) alert(`Hoàn thành! ✅ ${fcCorrect} đúng  ❌ ${fcWrong} sai`);
}

// ═══════════════════════════════════════════════════════
//  QUIZ
// ═══════════════════════════════════════════════════════
let quizMode = 'mc', quizDir = 'en-vi';
let quizQuestions = [], quizIdx = 0, quizScore = 0;

function setQuizMode(m) {
  quizMode = m;
  ['mc','fill'].forEach(x => document.getElementById('qm-'+x).classList.toggle('active', x === m));
}
function setQuizDir(d) {
  quizDir = d;
  ['en-vi','vi-en'].forEach(x => document.getElementById('dir-'+x).classList.toggle('active', x === d));
}

function startQuiz() {
  const n = Math.min(parseInt(document.getElementById('quiz-count').value) || 10, vocabData.length);
  quizQuestions = shuffle([...vocabData]).slice(0, n);
  quizIdx = 0; quizScore = 0;
  document.getElementById('quiz-setup').style.display = 'none';
  document.getElementById('quiz-result').style.display = 'none';
  document.getElementById('quiz-game').style.display = 'block';
  showQuestion();
}

function showQuestion() {
  if (quizIdx >= quizQuestions.length) { showQuizResult(); return; }
  const v = quizQuestions[quizIdx];
  document.getElementById('quiz-counter').textContent = `Câu ${quizIdx + 1}/${quizQuestions.length}`;
  document.getElementById('quiz-score-disp').textContent = `Điểm: ${quizScore}`;
  document.getElementById('quiz-progress').style.width = ((quizIdx / quizQuestions.length) * 100) + '%';
  document.getElementById('quiz-feedback').style.display = 'none';
  document.getElementById('quiz-next-btn').style.display = 'none';

  // Question text
  const qText = quizDir === 'en-vi'
    ? `Từ tiếng Anh: <strong>${v.word}</strong> ${v.ipa ? `<span style="color:var(--accent);font-size:14px;">${v.ipa}</span>` : ''}`
    : `Nghĩa tiếng Việt: <strong>${v.definition}</strong>`;
  document.getElementById('quiz-q').innerHTML = qText;

  if (quizMode === 'mc') {
    document.getElementById('quiz-mc-opts').style.display = 'grid';
    document.getElementById('quiz-fill-area').style.display = 'none';
    // Build 4 options
    let wrong = shuffle(vocabData.filter(x => x.word !== v.word)).slice(0, 3);
    let opts = shuffle([v, ...wrong]);
    document.getElementById('quiz-mc-opts').innerHTML = opts.map((opt, i) => {
      const label = quizDir === 'en-vi' ? opt.definition : opt.word;
      return `<button class="quiz-opt" onclick="checkMC(this, '${opt.word === v.word ? 'correct' : 'wrong'}', '${v.word.replace(/'/g, "\\'")}', '${v.definition.replace(/'/g, "\\'")}')">${label}</button>`;
    }).join('');
  } else {
    document.getElementById('quiz-mc-opts').style.display = 'none';
    document.getElementById('quiz-fill-area').style.display = 'block';
    document.getElementById('quiz-fill-input').value = '';
    document.getElementById('quiz-fill-input').className = '';
    setTimeout(() => document.getElementById('quiz-fill-input').focus(), 100);
  }
}

function checkMC(btn, result, word, def) {
  document.querySelectorAll('.quiz-opt').forEach(b => b.disabled = true);
  btn.classList.add(result);
  if (result === 'correct') {
    quizScore++;
    showFeedback(true, `✅ Chính xác!`);
    setStatus(word, Math.min((vocabData.find(v => v.word === word)?.status || 0) + 1, 5));
  } else {
    document.querySelectorAll('.quiz-opt').forEach(b => {
      if (b.getAttribute('onclick')?.includes("'correct'")) b.classList.add('correct');
    });
    showFeedback(false, `❌ Sai! Đáp án đúng: ${quizDir === 'en-vi' ? def : word}`);
  }
  document.getElementById('quiz-next-btn').style.display = 'inline-flex';
  speak(word, 'en-US');
}

function checkFill() {
  const inp = document.getElementById('quiz-fill-input');
  const v = quizQuestions[quizIdx];
  const answer = quizDir === 'en-vi' ? v.definition.toLowerCase() : v.word.toLowerCase();
  const userAnswer = inp.value.trim().toLowerCase();
  const correct = userAnswer === answer || answer.includes(userAnswer) && userAnswer.length > 3;
  inp.className = correct ? 'correct' : 'wrong';
  if (correct) { quizScore++; showFeedback(true, `✅ Chính xác!`); }
  else showFeedback(false, `❌ Đáp án đúng: ${quizDir === 'en-vi' ? v.definition : v.word}`);
  document.getElementById('quiz-next-btn').style.display = 'inline-flex';
  speak(v.word, 'en-US');
}

function showFeedback(ok, msg) {
  const fb = document.getElementById('quiz-feedback');
  fb.className = 'quiz-feedback ' + (ok ? 'correct' : 'wrong');
  fb.textContent = msg;
  fb.style.display = 'block';
}

function nextQuiz() { quizIdx++; showQuestion(); }

function speakCurrent() { speak(quizQuestions[quizIdx]?.word || '', 'en-US'); }

function showQuizResult() {
  document.getElementById('quiz-game').style.display = 'none';
  document.getElementById('quiz-result').style.display = 'block';
  const pct = Math.round((quizScore / quizQuestions.length) * 100);
  document.getElementById('quiz-final-score').textContent = pct + '%';
  document.getElementById('quiz-final-label').textContent = `${quizScore}/${quizQuestions.length} câu đúng – ${pct >= 80 ? '🎉 Xuất sắc!' : pct >= 60 ? '👍 Khá tốt!' : '📚 Cần ôn thêm!'}`;
}

function backToQuizSetup() {
  document.getElementById('quiz-game').style.display = 'none';
  document.getElementById('quiz-result').style.display = 'none';
  document.getElementById('quiz-setup').style.display = 'block';
}

// ═══════════════════════════════════════════════════════
//  READING COMPREHENSION
// ═══════════════════════════════════════════════════════
const READING_PASSAGES = [
  {
    title: "The Power of Perseverance",
    level: "Intermediate",
    words: ["perseverance", "resilient", "endeavor", "emulate", "robust"],
    passage: `In every __1__ (endeavor) of human achievement, the quality of __2__ (perseverance) has proven to be one of the most decisive factors. Those who succeed are not always the most talented; they are often the most __3__ (resilient) individuals who refuse to give up when faced with adversity. To become truly successful, one must __4__ (emulate) the habits of great achievers while building a __5__ (robust) foundation of discipline and focus.`,
    blanks: ["endeavor", "perseverance", "resilient", "emulate", "robust"],
  },
  {
    title: "Ethics in Modern Business",
    level: "Upper-Intermediate",
    words: ["integrity", "fraud", "bribe", "embezzle", "insidious"],
    passage: `Maintaining __1__ (integrity) in business is essential for long-term success. Companies that engage in __2__ (fraud) or allow employees to __3__ (embezzle) funds inevitably face legal consequences and public backlash. The most __4__ (insidious) threat to business ethics is the normalization of small acts of corruption—executives who accept a __5__ (bribe) often justify it as a minor compromise, unaware of the systemic damage they cause.`,
    blanks: ["integrity", "fraud", "embezzle", "insidious", "bribe"],
  },
  {
    title: "Language and Communication",
    level: "Intermediate",
    words: ["eloquent", "ambiguous", "demeanor", "unconventional", "articulate"],
    passage: `An __1__ (eloquent) speaker can transform a difficult idea into a message that resonates with any audience. However, language becomes problematic when it is __2__ (ambiguous)—open to multiple interpretations. A speaker's __3__ (demeanor) on stage often matters as much as the words themselves. The most memorable communicators tend to use __4__ (unconventional) approaches that challenge their audience to think differently, while remaining precise and __5__ (articulate) in their delivery.`,
    blanks: ["eloquent", "ambiguous", "demeanor", "unconventional", "articulate"],
  },
];

let currentReading = null, readingBlanks = [];

function initReadingPassages() { renderReadingSelect(); }

function renderReadingSelect() {
  const list = document.getElementById('reading-list');
  list.innerHTML = READING_PASSAGES.map((p, i) => `
    <div class="card" style="cursor:pointer;transition:.15s;" onmouseover="this.style.borderColor='var(--accent)'" onmouseout="this.style.borderColor='var(--border)'" onclick="startReading(${i})">
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <div>
          <div style="font-weight:600;font-size:15px;">${p.title}</div>
          <div style="font-size:12px;color:var(--text2);margin-top:4px;">Từ vựng: ${p.words.join(', ')}</div>
        </div>
        <span class="badge ${p.level === 'Intermediate' ? 'learning' : 'new'}">${p.level}</span>
      </div>
    </div>`).join('');
}

function startReading(idx) {
  currentReading = READING_PASSAGES[idx];
  document.getElementById('reading-select').style.display = 'none';
  document.getElementById('reading-game').style.display = 'block';
  document.getElementById('reading-result').style.display = 'none';

  // Word bank
  const wb = document.getElementById('reading-wordbank');
  const shuffledWords = shuffle([...currentReading.words]);
  wb.innerHTML = shuffledWords.map(w => `<span class="word-chip" data-word="${w}" onclick="fillFromBank(this,'${w}')">${w}</span>`).join('');

  // Render passage with blanks
  let passageHTML = currentReading.passage;
  passageHTML = passageHTML.replace(/__(\d+)\s*\(([^)]+)\)/g, (m, num, word) =>
    `<input class="blank-input" data-answer="${word}" data-num="${num}" size="${word.length + 2}" placeholder="${'_'.repeat(word.length)}" oninput="onBlankInput(this)">`
  );
  document.getElementById('reading-passage').innerHTML = passageHTML;
  readingBlanks = document.querySelectorAll('.blank-input');
}

function fillFromBank(chip, word) {
  const active = [...readingBlanks].find(b => b === document.activeElement) || [...readingBlanks].find(b => !b.value);
  if (active) { active.value = word; chip.classList.add('used'); onBlankInput(active); }
}

function onBlankInput(inp) {
  inp.className = 'blank-input'; // reset color on type
}

function checkReading() {
  let correct = 0;
  readingBlanks.forEach(b => {
    const ans = b.value.trim().toLowerCase();
    const expected = b.dataset.answer.toLowerCase();
    if (ans === expected) { b.classList.add('correct'); correct++; }
    else { b.classList.add('wrong'); b.value = b.dataset.answer; }
  });
  const total = readingBlanks.length;
  const res = document.getElementById('reading-result');
  res.style.display = 'block';
  res.innerHTML = `<div class="quiz-feedback ${correct === total ? 'correct' : 'wrong'}">
    ${correct === total ? '🎉 Hoàn hảo! Tất cả đều đúng!' : `✅ ${correct}/${total} câu đúng – Đáp án đã được điền vào ô sai`}
  </div>`;
}

function resetReading() { startReading(READING_PASSAGES.indexOf(currentReading)); }

function showReadingHint() {
  const firstWrong = [...readingBlanks].find(b => !b.value);
  if (firstWrong) {
    firstWrong.placeholder = firstWrong.dataset.answer.substring(0, 2) + '...';
    firstWrong.focus();
  }
}

function backToReadingSelect() {
  document.getElementById('reading-select').style.display = 'block';
  document.getElementById('reading-game').style.display = 'none';
}

// ═══════════════════════════════════════════════════════
//  CHINESE / HANZIWRITER
// ═══════════════════════════════════════════════════════
let hanzWriters = {};

function renderChinese() {
  const q = document.getElementById('zh-search').value.toLowerCase();
  const filtered = chineseData.filter(v => !q || v.hanzi.includes(q) || v.pinyin.includes(q) || v.meaning.toLowerCase().includes(q));
  const grid = document.getElementById('hanzi-grid');
  if (!filtered.length) { grid.innerHTML = '<div class="empty">Không tìm thấy Hán tự nào</div>'; return; }
  grid.innerHTML = filtered.map((v, i) => `
    <div class="hanzi-card">
      <div class="hanzi-char">${v.hanzi}</div>
      <div class="pinyin">${v.pinyin}</div>
      <div class="hanzi-meaning">${v.meaning}</div>
      ${v.example ? `<div class="example" style="text-align:center;margin:8px 0;">${v.example}</div>` : ''}
      <div class="hanzi-writer-box" id="hzbox-${i}">
        <div id="hz-target-${i}"></div>
      </div>
      <div class="stroke-controls">
        <button class="btn sm secondary" onclick="animateHanzi(${i},'${v.hanzi}')">▶ Xem nét</button>
        <button class="btn sm secondary" onclick="quizHanzi(${i},'${v.hanzi}')">✏️ Tập viết</button>
        <button class="btn sm secondary" onclick="speak('${v.hanzi}','zh-CN')">🔊</button>
      </div>
    </div>`).join('');
}

function animateHanzi(idx, char) {
  const el = document.getElementById('hz-target-' + idx);
  el.innerHTML = '';
  if (typeof HanziWriter === 'undefined') { el.textContent = '⚠️ Cần kết nối internet để tải HanziWriter'; return; }
  try {
    const w = HanziWriter.create(el, char, { width: 120, height: 120, padding: 8, strokeColor: '#6c63ff', radicalColor: '#00d4aa', showOutline: true });
    hanzWriters[idx] = w;
    w.animateCharacter();
  } catch(e) { el.textContent = char; }
}

function quizHanzi(idx, char) {
  const el = document.getElementById('hz-target-' + idx);
  el.innerHTML = '';
  if (typeof HanziWriter === 'undefined') { el.textContent = '⚠️ Cần kết nối internet'; return; }
  try {
    const w = HanziWriter.create(el, char, { width: 120, height: 120, padding: 8, strokeColor: '#6c63ff', showOutline: false, drawingColor: '#00d4aa', drawingWidth: 4 });
    hanzWriters[idx] = w;
    w.quiz({ onMistake: () => {}, onComplete: () => { setTimeout(() => animateHanzi(idx, char), 800); } });
  } catch(e) { el.textContent = char; }
}

// ═══════════════════════════════════════════════════════
//  IMPORT / EXPORT
// ═══════════════════════════════════════════════════════
function importJSON(e) {
  const file = e.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = ev => {
    try {
      const data = JSON.parse(ev.target.result);
      if (!Array.isArray(data)) throw new Error('Phải là mảng JSON');
      const newWords = data.map((v, i) => ({ word: v.word || '', ipa: v.ipa || '', definition: v.definition || '', definitionEN: v.definitionEN || '', example: v.example || '', synonyms: v.synonyms || '', antonyms: v.antonyms || '', status: v.status || 0, favorite: false, id: vocabData.length + i }));
      vocabData = [...vocabData, ...newWords];
      saveVocab(); updateStats();
      const st = document.getElementById('import-status');
      st.style.display = 'block';
      st.textContent = `✅ Đã import thành công ${newWords.length} từ vựng!`;
    } catch(err) { alert('Lỗi đọc file: ' + err.message); }
  };
  reader.readAsText(file, 'UTF-8');
}

function importChineseJSON(e) {
  const file = e.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = ev => {
    try {
      const data = JSON.parse(ev.target.result);
      const newChars = data.map((v, i) => ({ hanzi: v.hanzi || '', pinyin: v.pinyin || '', meaning: v.meaning || '', example: v.example || '', level: v.level || 1, id: chineseData.length + i }));
      chineseData = [...chineseData, ...newChars];
      saveChinese();
      alert(`✅ Đã import ${newChars.length} Hán tự!`);
    } catch(err) { alert('Lỗi: ' + err.message); }
  };
  reader.readAsText(file, 'UTF-8');
}

function downloadSample() {
  const sample = [
    { word: "example", ipa: "/ɪɡˈzæmpəl/", definition: "Ví dụ, mẫu để noi theo", definitionEN: "A thing characteristic of its kind", example: "This is an example sentence.", synonyms: "Instance, Case", antonyms: "Exception", status: 0, favorite: false }
  ];
  const blob = new Blob([JSON.stringify(sample, null, 2)], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href = url; a.download = 'vocab_sample.json'; a.click();
  URL.revokeObjectURL(url);
}

// ═══════════════════════════════════════════════════════
//  UTILS
// ═══════════════════════════════════════════════════════
function shuffle(arr) {
  for (let i = arr.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
  return arr;
}

// Preload voices
window.speechSynthesis?.addEventListener?.('voiceschanged', () => window.speechSynthesis.getVoices());
window.speechSynthesis?.getVoices?.();

// ═══════════════════════════════════════════════════════
//  BOOT
// ═══════════════════════════════════════════════════════
document.addEventListener('DOMContentLoaded', init);
</script>
</body>
</html>
