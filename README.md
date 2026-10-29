<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ワンケア / WanCare</title>
  <style>
    :root { --primary: #4A7C59; --primary-light: #EAF2EC; --secondary: #E9B44C; --accent: #D95D39; --bg: #F4F6F4; --card-bg: #FFFFFF; --text: #333333; --text-muted: #666666; --border: #E2E8F0; }
    * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, sans-serif; }
    body { background-color: var(--bg); color: var(--text); padding-bottom: 70px; }
    header { background: var(--primary); color: white; padding: 16px; text-align: center; position: sticky; top: 0; z-index: 100; display: flex; justify-content: space-between; align-items: center; }
    header h1 { font-size: 1.2rem; margin: 0; }
    .icon-btn { background: none; border: none; color: white; font-size: 1.3rem; cursor: pointer; }
    .container { max-width: 600px; margin: 0 auto; padding: 16px; }
    .card { background: var(--card-bg); border-radius: 12px; padding: 16px; margin-bottom: 16px; box-shadow: 0 2px 4px rgba(0,0,0,0.05); }
    .card-title { font-size: 1rem; font-weight: bold; margin-bottom: 12px; display: flex; align-items: center; gap: 8px; color: var(--primary); }
    .profile-header { display: flex; align-items: center; gap: 16px; }
    .profile-avatar { width: 70px; height: 70px; border-radius: 50%; object-fit: cover; border: 3px solid var(--primary-light); background: #ddd; }
    .quick-actions { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; margin-bottom: 16px; }
    .quick-btn { background: var(--card-bg); border: 1px solid var(--border); border-radius: 12px; padding: 12px 8px; text-align: center; cursor: pointer; font-size: 0.8rem; }
    .quick-btn span { display: block; font-size: 1.5rem; margin-bottom: 4px; }
    .nav-tabs { position: fixed; bottom: 0; left: 0; right: 0; background: white; display: flex; border-top: 1px solid var(--border); z-index: 100; }
    .tab-btn { flex: 1; padding: 10px 0; text-align: center; border: none; background: none; font-size: 0.75rem; color: var(--text-muted); cursor: pointer; }
    .tab-btn.active { color: var(--primary); font-weight: bold; }
    .tab-btn span { display: block; font-size: 1.2rem; }
    .tab-content { display: none; }
    .tab-content.active { display: block; }
    .form-group { margin-bottom: 12px; }
    label { display: block; font-size: 0.85rem; color: var(--text-muted); margin-bottom: 4px; }
    input, select { width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: 8px; font-size: 0.95rem; }
    .btn { background: var(--primary); color: white; border: none; padding: 10px 16px; border-radius: 8px; font-weight: bold; cursor: pointer; margin-top: 8px; width: 100%; }
    .btn-secondary { background: var(--secondary); }
    .btn-danger { background: var(--accent); }
    .flex-gap { display: flex; gap: 8px; }
    .log-item { padding: 10px 0; border-bottom: 1px solid var(--border); font-size: 0.85rem; display: flex; justify-content: space-between; align-items: center; }
    .log-item:last-child { border-bottom: none; }
    .link-item { display: flex; justify-content: space-between; align-items: center; padding: 12px 0; border-bottom: 1px solid var(--border); }
    .link-item:last-child { border-bottom: none; }
    .link-btn { color: var(--primary); font-weight: bold; text-decoration: none; word-break: break-all; }
    .icon-action-btn { background: none; border: none; font-size: 1.1rem; cursor: pointer; padding: 4px; margin-left: 4px; }
  </style>
</head>
<body>

<header>
  <h1>🐾 ワンケア / WanCare</h1>
  <button class="icon-btn" onclick="switchTab('settings')">⚙️</button>
</header>

<div class="container">
  <div class="quick-actions">
    <div class="quick-btn" onclick="quickFeed()"><span>🍚</span>ごはん</div>
    <div class="quick-btn" onclick="quickPoo('便')"><span>💩</span>うんち</div>
    <div class="quick-btn" onclick="quickPoo('尿')"><span>💧</span>おしっこ</div>
    <div class="quick-btn" onclick="toggleWalkTimer()"><span id="walk-icon">🦮</span>散歩タイマー</div>
  </div>

  <!-- ホームタブ -->
  <div id="tab-dashboard" class="tab-content active">
    <div class="card">
      <div class="profile-header">
        <img id="dash-photo" src="https://images.unsplash.com/photo-1543466835-00a7907e9de1?w=200" class="profile-avatar" alt="愛犬">
        <div>
          <h2 id="dash-name" style="font-size:1.2rem;">愛犬名未設定</h2>
          <p id="dash-info" style="font-size:0.85rem; color:var(--text-muted);">犬種未設定 / 性別未設定 / 年齢未設定 / 体重未設定</p>
        </div>
      </div>
    </div>

    <div class="card">
      <div class="card-title">⏱️ 散歩距離の自動計算</div>
      <p id="timer-display" style="font-size: 1rem; font-weight: bold; margin-bottom: 8px;">ステータス: 停止中</p>
      <div class="form-group">
        <label>歩数を入力:</label>
        <div class="flex-gap">
          <input type="number" id="walk-steps" placeholder="例: 3000" oninput="calculateDistance()">
          <select id="stride-length" onchange="calculateDistance()" style="width: 140px;">
            <option value="0.5">小型犬 (50cm)</option>
            <option value="0.65" selected>標準 (65cm)</option>
            <option value="0.75">しっかり (75cm)</option>
          </select>
        </div>
      </div>
      <div class="form-group">
        <label>計算結果 (km):</label>
        <input type="number" id="walk-dist" step="0.01" readonly style="background:#f0f0f0;">
      </div>
      <button class="btn" onclick="saveWalkLog()">散歩ログを保存</button>
    </div>

    <!-- 本日の記録一覧（修正・削除機能付き） -->
    <div class="card">
      <div class="card-title">📝 本日の記録</div>
      <div id="today-logs"></div>
    </div>
  </div>

  <!-- 予定タブ -->
  <div id="tab-schedule" class="tab-content">
    <div class="card">
      <div class="card-title">📅 予定追加</div>
      <div class="form-group"><label>予定名:</label><input type="text" id="sched-title" placeholder="例: 狂犬病予防注射"></div>
      <div class="form-group"><label>日付:</label><input type="date" id="sched-date"></div>
      <button class="btn" onclick="addSchedule()">予定を保存する</button>
    </div>
    <div class="card">
      <div class="card-title">📅 予定一覧</div>
      <div id="schedule-list"></div>
    </div>
  </div>

  <!-- ショップタブ -->
  <div id="tab-links" class="tab-content">
    <div class="card">
      <div class="card-title">🛍️ ショップ・病院の登録</div>
      <div class="form-group">
        <label>ショップ・病院の名前:</label>
        <input type="text" id="link-name" placeholder="例: かかりつけ動物病院">
      </div>
      <div class="form-group">
        <label>ホームページのURL:</label>
        <input type="url" id="link-url" placeholder="https://example.com">
      </div>
      <button class="btn" onclick="addCustomLink()">リンクを追加する</button>
    </div>

    <div class="card">
      <div class="card-title">🔗 登録済みショップ・病院一覧</div>
      <div id="custom-links-list"></div>
    </div>
  </div>

  <!-- 設定タブ -->
  <div id="tab-settings" class="tab-content">
    <div class="card">
      <div class="card-title">⚙️ 愛犬設定</div>
      <div class="form-group"><label>愛犬の名前:</label><input type="text" id="set-name" placeholder="例: こなつ"></div>
      <div class="form-group"><label>犬種:</label><input type="text" id="set-breed" placeholder="例: トイプードル"></div>
      
      <div class="form-group">
        <label>性別:</label>
        <select id="set-gender">
          <option value="男の子 ♂">男の子 ♂</option>
          <option value="女の子 ♀">女の子 ♀</option>
          <option value="男の子(去勢済) ♂">男の子(去勢済) ♂</option>
          <option value="女の子(避妊済) ♀">女の子(避妊済) ♀</option>
        </select>
      </div>
      
      <div class="form-group">
        <label>生年月日（誕生日）:</label>
        <input type="date" id="set-birthdate">
      </div>

      <div class="form-group">
        <label>体重 (kg):</label>
        <input type="number" id="set-weight" step="0.1" placeholder="例: 3.2">
      </div>
      
      <div class="form-group"><label>写真変更:</label><input type="file" id="set-photo" accept="image/*" onchange="previewImage(event)"></div>
      <button class="btn" onclick="saveSettings()">設定保存</button>
    </div>
    
    <div class="card">
      <button class="btn btn-danger" onclick="resetAllData()">🗑️ 全データ初期化（リセット）</button>
    </div>
  </div>
</div>

<div class="nav-tabs">
  <button class="tab-btn active" onclick="switchTab('dashboard')"><span>🏠</span>ホーム</button>
  <button class="tab-btn" onclick="switchTab('schedule')"><span>📅</span>予定</button>
  <button class="tab-btn" onclick="switchTab('links')"><span>🛍️</span>ショップ</button>
  <button class="tab-btn" onclick="switchTab('settings')"><span>⚙️</span>設定</button>
</div>

<script>
  let logs = [];
  let settings = {};
  let schedules = [];
  let customLinks = [];
  let pendingPhoto = null;
  let walkStartTime = null;

  function calculateAge(birthdateStr) {
    if (!birthdateStr) return '年齢未設定';
    const birthDate = new Date(birthdateStr);
    const today = new Date();
    
    let years = today.getFullYear() - birthDate.getFullYear();
    let months = today.getMonth() - birthDate.getMonth();
    
    if (months < 0 || (months === 0 && today.getDate() < birthDate.getDate())) {
      years--;
      months += 12;
    }
    if (today.getDate() < birthDate.getDate()) {
      months--;
      if (months < 0) months += 12;
    }

    if (years < 0) return '0歳 0ヶ月';
    return `${years}歳 ${months}ヶ月`;
  }

  function init() {
    logs = JSON.parse(localStorage.getItem('wancare_logs') || '[]');
    settings = JSON.parse(localStorage.getItem('wancare_settings') || '{}');
    schedules = JSON.parse(localStorage.getItem('wancare_schedules') || '[]');
    customLinks = JSON.parse(localStorage.getItem('wancare_links') || '[]');

    document.getElementById('dash-name').innerText = settings.name || '愛犬名未設定';
    const ageText = calculateAge(settings.birthdate);
    const genderText = settings.gender || '性別未設定';
    const breedText = settings.breed || '犬種未設定';
    const weightText = settings.weight ? `${settings.weight} kg` : '体重未設定';
    
    document.getElementById('dash-info').innerText = `${breedText} / ${genderText} / ${ageText} / ${weightText}`;

    const defaultPhoto = "https://images.unsplash.com/photo-1543466835-00a7907e9de1?w=200";
    document.getElementById('dash-photo').src = settings.photo || defaultPhoto;

    document.getElementById('set-name').value = settings.name || '';
    document.getElementById('set-breed').value = settings.breed || '';
    document.getElementById('set-gender').value = settings.gender || '男の子 ♂';
    document.getElementById('set-birthdate').value = settings.birthdate || '';
    document.getElementById('set-weight').value = settings.weight || '';

    renderLogs();
    renderSchedules();
    renderLinks();
  }

  function switchTab(tabId) {
    document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
    document.querySelectorAll('.tab-btn').forEach(el => el.classList.remove('active'));
    document.getElementById('tab-' + tabId).classList.add('active');
  }

  function quickFeed() {
    const time = new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
    logs.unshift({ type: 'ごはん', text: '給餌完了', time: time });
    saveLogs();
  }

  function quickPoo(type) {
    const time = new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
    logs.unshift({ type: type, text: '記録完了', time: time });
    saveLogs();
  }

  function toggleWalkTimer() {
    const display = document.getElementById('timer-display');
    const icon = document.getElementById('walk-icon');
    if (!walkStartTime) {
      walkStartTime = new Date();
      display.innerText = 'ステータス: 散歩中...';
      icon.innerText = '⏹️';
    } else {
      const duration = Math.round((new Date() - walkStartTime) / 60000);
      const time = new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
      logs.unshift({ type: '散歩', text: `${duration}分間`, time: time });
      saveLogs();
      walkStartTime = null;
      display.innerText = 'ステータス: 停止中';
      icon.innerText = '🦮';
    }
  }

  function calculateDistance() {
    const steps = parseFloat(document.getElementById('walk-steps').value) || 0;
    const stride = parseFloat(document.getElementById('stride-length').value) || 0.65;
    const dist = (steps * stride) / 1000;
    document.getElementById('walk-dist').value = dist.toFixed(2);
  }

  function saveWalkLog() {
    const steps = document.getElementById('walk-steps').value;
    const dist = document.getElementById('walk-dist').value;
    if (!dist) return;
    const time = new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
    logs.unshift({ type: '散歩', text: `${dist} km (${steps}歩)`, time: time });
    saveLogs();
  }

  function saveLogs() {
    localStorage.setItem('wancare_logs', JSON.stringify(logs));
    renderLogs();
  }

  /* 記録の修正・削除関数 */
  function editLog(index) {
    const newText = prompt('記録の内容を修正してください:', logs[index].text);
    if (newText !== null && newText.trim() !== '') {
      logs[index].text = newText.trim();
      saveLogs();
    }
  }

  function deleteLog(index) {
    if (confirm('この記録を削除しますか？')) {
      logs.splice(index, 1);
      saveLogs();
    }
  }

  function renderLogs() {
    const container = document.getElementById('today-logs');
    if (logs.length === 0) {
      container.innerHTML = '<p style="color:var(--text-muted); font-size:0.85rem;">本日の記録はありません。</p>';
      return;
    }
    container.innerHTML = logs.map((log, index) => `
      <div class="log-item">
        <div>
          <span><strong>[${log.type}]</strong> ${log.text}</span>
          <span style="color:var(--text-muted); font-size:0.75rem; margin-left:6px;">${log.time}</span>
        </div>
        <div>
          <button class="icon-action-btn" onclick="editLog(${index})">✏️</button>
          <button class="icon-action-btn" onclick="deleteLog(${index})">🗑️</button>
        </div>
      </div>
    `).join('');
  }

  function addSchedule() {
    const titleInput = document.getElementById('sched-title');
    const dateInput = document.getElementById('sched-date');
    const title = titleInput.value.trim();
    const date = dateInput.value;

    if (!title || !date) {
      alert('「予定名」と「日付」の両方を入力してください。');
      return;
    }

    schedules.push({ title: title, date: date });
    schedules.sort((a, b) => new Date(a.date) - new Date(b.date));
    localStorage.setItem('wancare_schedules', JSON.stringify(schedules));

    titleInput.value = '';
    dateInput.value = '';
    renderSchedules();
    alert('予定を保存しました！');
  }

  function deleteSchedule(index) {
    if (confirm('この予定を削除しますか？')) {
      schedules.splice(index, 1);
      localStorage.setItem('wancare_schedules', JSON.stringify(schedules));
      renderSchedules();
    }
  }

  function renderSchedules() {
    const container = document.getElementById('schedule-list');
    if (schedules.length === 0) {
      container.innerHTML = '<p style="color:var(--text-muted); font-size:0.85rem;">登録された予定はありません。</p>';
      return;
    }
    container.innerHTML = schedules.map((item, index) => `
      <div class="log-item">
        <span>📌 <strong>${item.date}</strong> : ${item.title}</span>
        <button class="icon-action-btn" onclick="deleteSchedule(${index})">🗑️</button>
      </div>
    `).join('');
  }

  function addCustomLink() {
    const nameInput = document.getElementById('link-name');
    const urlInput = document.getElementById('link-url');
    const name = nameInput.value.trim();
    let url = urlInput.value.trim();

    if (!name || !url) {
      alert('「名前」と「ホームページのURL」の両方を入力してください。');
      return;
    }

    if (!url.startsWith('http://') && !url.startsWith('https://')) {
      url = 'https://' + url;
    }

    customLinks.push({ name: name, url: url });
    localStorage.setItem('wancare_links', JSON.stringify(customLinks));
    
    nameInput.value = '';
    urlInput.value = '';
    renderLinks();
    alert('ショップ・病院を追加しました！');
  }

  function deleteLink(index) {
    if (confirm('このリンクを削除しますか？')) {
      customLinks.splice(index, 1);
      localStorage.setItem('wancare_links', JSON.stringify(customLinks));
      renderLinks();
    }
  }

  function renderLinks() {
    const container = document.getElementById('custom-links-list');
    if (customLinks.length === 0) {
      container.innerHTML = '<p style="color:var(--text-muted); font-size:0.85rem;">登録されたショップ・病院はありません。</p>';
      return;
    }
    container.innerHTML = customLinks.map((link, index) => `
      <div class="link-item">
        <a href="${link.url}" target="_blank" class="link-btn">🔗 ${link.name}</a>
        <button class="icon-action-btn" onclick="deleteLink(${index})">🗑️</button>
      </div>
    `).join('');
  }

  function saveSettings() {
    try {
      settings.name = document.getElementById('set-name').value;
      settings.breed = document.getElementById('set-breed').value;
      settings.gender = document.getElementById('set-gender').value;
      settings.birthdate = document.getElementById('set-birthdate').value;
      settings.weight = document.getElementById('set-weight').value;
      
      if (pendingPhoto) {
        settings.photo = pendingPhoto;
      }

      localStorage.setItem('wancare_settings', JSON.stringify(settings));
      init();
      alert('設定を保存しました！');
      switchTab('dashboard');
    } catch(e) {
      alert('写真の容量が大きすぎるため保存できませんでした。');
    }
  }

  function previewImage(event) {
    const file = event.target.files[0];
    if (!file) return;

    const reader = new FileReader();
    reader.onload = function(e) {
      const img = new Image();
      img.onload = function() {
        const canvas = document.createElement('canvas');
        const max_size = 300;
        let width = img.width;
        let height = img.height;

        if (width > height) {
          if (width > max_size) {
            height *= max_size / width;
            width = max_size;
          }
        } else {
          if (height > max_size) {
            width *= max_size / height;
            height = max_size;
          }
        }
        canvas.width = width;
        canvas.height = height;
        const ctx = canvas.getContext('2d');
        ctx.drawImage(img, 0, 0, width, height);

        pendingPhoto = canvas.toDataURL('image/jpeg', 0.7);
        document.getElementById('dash-photo').src = pendingPhoto;
      };
      img.src = e.target.result;
    };
    reader.readAsDataURL(file);
  }

  function resetAllData() {
    if (confirm('すべてのデータや愛犬の設定を完全に削除して初期化しますか？')) {
      localStorage.removeItem('wancare_logs');
      localStorage.removeItem('wancare_settings');
      localStorage.removeItem('wancare_schedules');
      localStorage.removeItem('wancare_links');
      pendingPhoto = null;
      init();
      alert('すべてのデータを初期化しました。');
      switchTab('dashboard');
    }
  }

  window.onload = init;
</script>
</body>
</html>
