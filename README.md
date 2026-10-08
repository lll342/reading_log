<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>読書感想文記録システム</title>
    <style>
        :root {
            --primary-color: #007bff;
            --success-color: #28a745;
            --bg-color: #f4f6f9;
            --card-bg: #ffffff;
            --border-color: #dee2e6;
        }

        body {
            font-family: 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic ProN', 'Hiragino Sans', sans-serif;
            background-color: var(--bg-color);
            color: #333;
            line-height: 1.6;
            margin: 0;
            padding: 20px;
        }

        .container {
            max-width: 900px;
            margin: 0 auto;
        }

        h1, h2, h3 {
            color: #2c3e50;
        }

        .card {
            background: var(--card-bg);
            border-radius: 8px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.05);
            border: 1px solid var(--border-color);
        }

        .form-group {
            margin-bottom: 15px;
        }

        label {
            display: block;
            font-weight: bold;
            margin-bottom: 5px;
            font-size: 0.9em;
        }

        input[type="text"], input[type="date"], select, textarea {
            width: 100%;
            padding: 10px;
            border: 1px solid var(--border-color);
            border-radius: 4px;
            box-sizing: border-box;
            font-size: 1em;
        }

        textarea {
            resize: vertical;
            min-height: 80px;
        }

        .btn {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 1em;
            font-weight: bold;
            transition: background 0.2s;
        }

        .btn:hover {
            opacity: 0.9;
        }

        .btn-success {
            background-color: var(--success-color);
        }

        .btn-danger {
            background-color: #dc3545;
            padding: 5px 10px;
            font-size: 0.85em;
        }

        /* 検索・フィルターエリア */
        .filter-group {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            margin-bottom: 15px;
        }

        .kana-buttons {
            display: flex;
            flex-wrap: wrap;
            gap: 4px;
            margin-top: 10px;
        }

        .kana-btn {
            background: #e9ecef;
            border: 1px solid var(--border-color);
            padding: 4px 8px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 0.85em;
        }

        .kana-btn.active {
            background: var(--primary-color);
            color: white;
            border-color: var(--primary-color);
        }

        /* 年別統計カード集計 */
        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
            gap: 10px;
            margin-top: 10px;
        }

        .stat-item {
            background: #eef5ff;
            border: 1px solid #cce5ff;
            border-radius: 6px;
            padding: 10px;
            text-align: center;
        }

        .stat-year {
            font-size: 0.85em;
            color: #555;
        }

        .stat-count {
            font-size: 1.4em;
            font-weight: bold;
            color: var(--primary-color);
        }

        /* 本の記録リスト */
        .book-item {
            border-bottom: 1px solid var(--border-color);
            padding: 15px 0;
        }

        .book-item:last-child {
            border-bottom: none;
        }

        .book-title {
            font-size: 1.2em;
            font-weight: bold;
            color: #111;
        }

        .book-meta {
            font-size: 0.85em;
            color: #666;
            margin: 4px 0 8px 0;
        }

        .badge {
            display: inline-block;
            padding: 2px 6px;
            border-radius: 3px;
            font-size: 0.75em;
            background: #6c757d;
            color: white;
        }

        /* バックアップエリア */
        .backup-section {
            background: #e9f7ef;
            border: 1px solid #c3e6cb;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>📚 読書感想文記録システム</h1>

    <!-- 1. 年別集計エリア -->
    <div class="card">
        <h2>📊 年間読破冊数</h2>
        <div id="yearlyStats" class="stats-grid">
            <!-- JavaScriptで動的生成 -->
        </div>
    </div>

    <!-- 2. 新規登録フォーム -->
    <div class="card">
        <h2>📝 新しい読書記録を追加</h2>
        <form id="bookForm" onsubmit="handleFormSubmit(event)">
            <div class="form-group">
                <label for="title">作品名 *</label>
                <input type="text" id="title" required placeholder="例: 走れメロス">
            </div>
            <div style="display: flex; gap: 10px; flex-wrap: wrap;">
                <div class="form-group" style="flex: 1; min-width: 200px;">
                    <label for="author">作者名 *</label>
                    <input type="text" id="author" required placeholder="例: 太宰治">
                </div>
                <div class="form-group" style="flex: 1; min-width: 200px;">
                    <label for="kana">作者名かな（検索・頭文字用）*</label>
                    <input type="text" id="kana" required placeholder="例: だざい おさむ">
                </div>
            </div>
            <div style="display: flex; gap: 10px; flex-wrap: wrap;">
                <div class="form-group" style="flex: 1; min-width: 150px;">
                    <label for="startDate">読み始めた日</label>
                    <input type="date" id="startDate">
                </div>
                <div class="form-group" style="flex: 1; min-width: 150px;">
                    <label for="endDate">読了日</label>
                    <input type="date" id="endDate">
                </div>
            </div>
            <div class="form-group">
                <label for="impression">読書感想文 / 感想メモ *</label>
                <textarea id="impression" required placeholder="心に残った場面や自分の考えを書きましょう"></textarea>
            </div>
            <button type="submit" class="btn btn-success">記録を保存する</button>
        </form>
    </div>

    <!-- 3. 検索・フィルター機能 -->
    <div class="card">
        <h2>🔍 登録済みの読書記録</h2>
        <div class="filter-group">
            <input type="text" id="searchInput" oninput="renderBooks()" placeholder="作品名・作者名でフリーワード検索..." style="flex: 1;">
        </div>
        
        <div>
            <label>五十音フィルター（作者名の頭文字）:</label>
            <div class="kana-buttons" id="kanaButtons">
                <!-- JavaScriptで五十音ボタンを生成 -->
            </div>
        </div>
    </div>

    <!-- 4. 読書記録一覧表示 -->
    <div class="card">
        <div id="bookList">
            <!-- JavaScriptで動的生成 -->
        </div>
    </div>

    <!-- 5. データバックアップ / 復元 (端末間の引き継ぎ用) -->
    <div class="card backup-section">
        <h3>💾 データの引き継ぎ・バックアップ</h3>
        <p style="font-size: 0.85em; color: #555; margin-bottom: 10px;">
            別のPCやスマホへデータを移す場合は、ここでバックアップファイルを保存・読み込みしてください。
        </p>
        <div style="display: flex; gap: 10px; flex-wrap: wrap; align-items: center;">
            <button onclick="exportData()" class="btn">データ出力（JSON）</button>
            <div style="display: flex; align-items: center; gap: 5px;">
                <input type="file" id="importFile" accept=".json" style="font-size: 0.85em; width: auto;">
                <button onclick="importData()" class="btn" style="background-color: #17a2b8;">データインポート</button>
            </div>
        </div>
    </div>
</div>

<script>
    /* ローカルストレージキー */
    const STORAGE_KEY = 'school_reading_log_data';

    /* グローバルデータ管理配列 */
    let booksData = [];
    let activeKanaFilter = '';

    /* 五十音定義 */
    const KANA_GROUPS = [
        { label: 'すべて', val: '' },
        { label: 'ア行', val: 'ア' }, { label: 'カ行', val: 'カ' },
        { label: 'サ行', val: 'サ' }, { label: 'タ行', val: 'タ' },
        { label: 'ナ行', val: 'ナ' }, { label: 'ハ行', val: 'ハ' },
        { label: 'マ行', val: 'マ' }, { label: 'ヤ行', val: 'ヤ' },
        { label: 'ラ行', val: 'ラ' }, { label: 'ワ行', val: 'ワ' }
    ];

    /* 初期化処理 */
    window.onload = function() {
        loadData();
        renderKanaButtons();
        renderYearlyStats();
        renderBooks();
    };

    /* LocalStorageからの読み込み */
    function loadData() {
        const stored = localStorage.getItem(STORAGE_KEY);
        if (stored) {
            try {
                booksData = JSON.parse(stored);
            } catch (e) {
                booksData = [];
            }
        }
    }

    /* LocalStorageへの保存 */
    function saveData() {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(booksData));
    }

    /* 新規フォーム送信処理 */
    function handleFormSubmit(e) {
        e.preventDefault();

        const newBook = {
            id: Date.now(),
            title: document.getElementById('title').value.trim(),
            author: document.getElementById('author').value.trim(),
            kana: document.getElementById('kana').value.trim(),
            startDate: document.getElementById('startDate').value || '未設定',
            endDate: document.getElementById('endDate').value || '未設定',
            impression: document.getElementById('impression').value.trim(),
            createdAt: new Date().toISOString()
        };

        booksData.unshift(newBook);
        saveData();

        document.getElementById('bookForm').reset();
        renderYearlyStats();
        renderBooks();
        alert('読書記録を保存しました！');
    }

    /* データの削除 */
    function deleteBook(id) {
        if (confirm('この読書記録を削除してもよろしいですか？')) {
            booksData = booksData.filter(b => b.id !== id);
            saveData();
            renderYearlyStats();
            renderBooks();
        }
    }

    /* 五十音フィルターボタン作成 */
    function renderKanaButtons() {
        const container = document.getElementById('kanaButtons');
        container.innerHTML = '';

        KANA_GROUPS.forEach(g => {
            const btn = document.createElement('button');
            btn.className = 'kana-btn' + (activeKanaFilter === g.val ? ' active' : '');
            btn.textContent = g.label;
            btn.onclick = () => {
                activeKanaFilter = g.val;
                renderKanaButtons();
                renderBooks();
            };
            container.appendChild(btn);
        });
    }

    /* 作者かなの頭文字判定（カタカナ行変換） */
    function getKanaGroup(kanaStr) {
        if (!kanaStr) return '';
        const firstChar = kanaStr.trim().charAt(0);
        const code = firstChar.charCodeAt(0);

        // ひらがな・カタカナ変換用簡易判定ロジック
        if (/[あ-おア-オアカサタナハマイウエオ]/.test(firstChar)) return 'ア';
        if (/[か-ごカ-ゴキクケコ]/.test(firstChar)) return 'カ';
        if (/[さ-ぞサ-ゾシスセソ]/.test(firstChar)) return 'サ';
        if (/[た-どタ-ドチツテト]/.test(firstChar)) return 'タ';
        if (/[な-のナ-ノニヌネノ]/.test(firstChar)) return 'ナ';
        if (/[は-ぼパ-ポハ-ホヒフヘホ]/.test(firstChar)) return 'ハ';
        if (/[ま-もマ-モミムメモ]/.test(firstChar)) return 'マ';
        if (/[や-よヤ-ヨユヨ]/.test(firstChar)) return 'ヤ';
        if (/[ら-ろラ-ロリルレロ]/.test(firstChar)) return 'ラ';
        if (/[わ-んワ-ンヲン]/.test(firstChar)) return 'ワ';

        return '';
    }

    /* 年間読破冊数の動的集計（直近3年＋折りたたみ対応） */
    function renderYearlyStats() {
        const container = document.getElementById('yearlyStats');
        const yearCounts = {};

        booksData.forEach(b => {
            if (b.endDate && b.endDate !== "未設定") {
                const year = b.endDate.split('-')[0];
                yearCounts[year] = (yearCounts[year] || 0) + 1;
            }
        });

        const years = Object.keys(yearCounts).sort((a, b) => b - a);

        if (years.length === 0) {
            container.innerHTML = `<p style="font-size:0.9em; color:#666; margin:0; grid-column:1/-1;">読了日を入力して登録すると、ここに年別の読破冊数が集計されます。（総登録数: ${booksData.length}冊）</p>`;
            return;
        }

        let html = `<div style="grid-column: 1/-1; text-align:left; font-size:0.9em; margin-bottom:5px;"><strong>累計読書冊数: ${booksData.length} 冊</strong></div>`;
        
        const recentYears = years.slice(0, 3);
        const pastYears = years.slice(3);

        recentYears.forEach(yr => {
            html += `
                <div class="stat-item">
                    <div class="stat-year">${yr}年</div>
                    <div class="stat-count">${yearCounts[yr]} <span style="font-size:0.6em;">冊</span></div>
                </div>
            `;
        });

        if (pastYears.length > 0) {
            html += `
                <details style="grid-column: 1/-1; margin-top: 10px; font-size: 0.9em; color: #555;">
                    <summary style="cursor: pointer; font-weight: bold; color: #007bff;">▼ 過去の読書データ（${pastYears.length}年分）を見る</summary>
                    <div class="stats-grid" style="margin-top: 8px;">
            `;
            
            pastYears.forEach(yr => {
                html += `
                    <div class="stat-item">
                        <div class="stat-year">${yr}年</div>
                        <div class="stat-count">${yearCounts[yr]} <span style="font-size:0.6em;">冊</span></div>
                    </div>
                `;
            });

            html += `
                    </div>
                </details>
            `;
        }

        container.innerHTML = html;
    }

    /* 読書記録の描画（検索・五十音フィルター適用） */
    function renderBooks() {
        const container = document.getElementById('bookList');
        const query = document.getElementById('searchInput').value.toLowerCase().trim();

        const filtered = booksData.filter(b => {
            const matchesQuery = b.title.toLowerCase().includes(query) || 
                                 b.author.toLowerCase().includes(query) ||
                                 b.kana.toLowerCase().includes(query);
            
            let matchesKana = true;
            if (activeKanaFilter !== '') {
                const group = getKanaGroup(b.kana);
                matchesKana = (group === activeKanaFilter);
            }

            return matchesQuery && matchesKana;
        });

        if (filtered.length === 0) {
            container.innerHTML = '<p style="color:#666; text-align:center; padding: 20px 0;">条件に一致する読書記録が見つかりません。</p>';
            return;
        }

        let html = '';
        filtered.forEach(b => {
            html += `
                <div class="book-item">
                    <div style="display:flex; justify-between; align-items:flex-start;">
                        <div class="book-title">${escapeHtml(b.title)}</div>
                        <button onclick="deleteBook(${b.id})" class="btn btn-danger" style="margin-left:auto;">削除</button>
                    </div>
                    <div class="book-meta">
                        作者: <strong>${escapeHtml(b.author)}</strong> (${escapeHtml(b.kana)}) | 
                        期間: ${b.startDate} ～ ${b.endDate}
                    </div>
                    <div style="white-space: pre-wrap; font-size: 0.95em; background: #f8f9fa; padding: 10px; border-radius: 4px; border-left: 3px solid #007bff;">${escapeHtml(b.impression)}</div>
                </div>
            `;
        });

        container.innerHTML = html;
    }

    /* HTMLエスケープ処理（XSS対策） */
    function escapeHtml(str) {
        if (!str) return '';
        return str.replace(/&/g, '&amp;')
                  .replace(/</g, '&lt;')
                  .replace(/>/g, '&gt;')
                  .replace(/"/g, '&quot;')
                  .replace(/'/g, '&#039;');
    }

    /* データ出力（JSONファイルとして保存） */
    function exportData() {
        if (booksData.length === 0) {
            alert("出力する読書データがありません。");
            return;
        }
        const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(booksData, null, 2));
        const downloadAnchor = document.createElement('a');
        downloadAnchor.setAttribute("href", dataStr);
        downloadAnchor.setAttribute("download", "reading_data.json");
        document.body.appendChild(downloadAnchor);
        downloadAnchor.click();
        downloadAnchor.remove();
    }

    /* データインポート（JSONファイルから復元） */
    function importData() {
        const fileInput = document.getElementById('importFile');
        const file = fileInput.files[0];

        if (!file) {
            alert("インポートするJSONファイル（reading_data.json）を選択してください。");
            return;
        }

        const reader = new FileReader();
        reader.onload = function(e) {
            try {
                const importedData = JSON.parse(e.target.result);
                if (Array.isArray(importedData)) {
                    if (confirm("現在の読書データが置き換わります。インポートを実行しますか？")) {
                        booksData = importedData;
                        saveData();
                        renderYearlyStats();
                        renderBooks();
                        alert("データの読み込みが完了しました！");
                    }
                } else {
                    alert("ファイル形式が正しくありません。");
                }
            } catch (err) {
                alert("ファイルの読み込みに失敗しました。正しく出力されたJSONファイルを選択してください。");
            }
        };
        reader.readAsText(file);
    }
</script>

</body>
</html>
