<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>教材貸出アプリ</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 font-sans min-h-screen text-gray-800">
    
    <div class="max-w-4xl mx-auto p-4 sm:p-6 lg:p-8">
        <h1 class="text-3xl font-bold text-center mb-8 text-blue-600">教材貸出システム</h1>
        
        <div class="bg-white p-5 rounded-xl shadow-md mb-8 flex flex-col sm:flex-row gap-4 items-center">
            <label for="userName" class="font-bold text-gray-700 whitespace-nowrap">利用者名:</label>
            <input type="text" id="userName" placeholder="名前を入力してください（例: 山田太郎）" 
                class="border-2 border-gray-200 p-3 rounded-lg flex-1 w-full focus:outline-none focus:border-blue-500 transition">
        </div>

        <div id="itemsContainer" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
            <!-- Items will be dynamically injected here via JavaScript -->
        </div>
    </div>

    <div id="messageModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center hidden z-50 p-4 transition-opacity">
        <div class="bg-white p-6 rounded-xl shadow-2xl max-w-sm w-full text-center">
            <div class="text-yellow-500 mb-4">
                <svg class="w-12 h-12 mx-auto" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"></path>
                </svg>
            </div>
            <p id="messageText" class="text-lg font-medium text-gray-800 mb-6"></p>
            <button onclick="closeModal()" class="bg-blue-600 text-white px-8 py-2 rounded-lg hover:bg-blue-700 font-bold transition">OK</button>
        </div>
    </div>

    <script>
        // 教材の初期データ
        const materials = [
            { id: 1, name: "HTML/CSS 実践テキスト", isBorrowed: false, borrower: "" },
            { id: 2, name: "JavaScript 基礎ガイド", isBorrowed: true, borrower: "田中一郎" },
            { id: 3, name: "React 開発入門", isBorrowed: false, borrower: "" },
            { id: 4, name: "デザイン原則ハンドブック", isBorrowed: false, borrower: "" },
            { id: 5, name: "テスト用貸出タブレット A", isBorrowed: false, borrower: "" },
            { id: 6, name: "テスト用貸出タブレット B", isBorrowed: true, borrower: "鈴木花子" }
        ];

        // 教材リストを描画する関数
        function renderItems() {
            const container = document.getElementById('itemsContainer');
            container.innerHTML = ''; // コンテナをクリア

            materials.forEach(item => {
                const card = document.createElement('div');
                card.className = `bg-white p-6 rounded-xl shadow-sm border-t-4 hover:shadow-md transition-shadow ${item.isBorrowed ? "border-red-500" : "border-green-500"}`;
                
                const statusBadge = item.isBorrowed 
                    ? `<span class="bg-red-100 text-red-700 text-xs font-bold px-3 py-1 rounded-full">貸出中</span>`
                    : `<span class="bg-green-100 text-green-700 text-xs font-bold px-3 py-1 rounded-full">貸出可能</span>`;
                
                const borrowerInfo = item.isBorrowed 
                    ? `<p class="text-sm text-gray-600 mt-3 bg-gray-50 p-2 rounded">利用者: <span class="font-bold text-gray-800">${item.borrower}</span></p>`
                    : `<p class="text-sm text-gray-400 mt-3 bg-gray-50 p-2 rounded">利用者: なし</p>`;

                const actionButton = item.isBorrowed
                    ? `<button onclick="returnItem(${item.id})" class="mt-5 w-full bg-gray-800 text-white font-bold py-2 rounded-lg hover:bg-gray-700 transition transform hover:scale-[1.02]">返却する</button>`
                    : `<button onclick="borrowItem(${item.id})" class="mt-5 w-full bg-blue-600 text-white font-bold py-2 rounded-lg hover:bg-blue-700 transition transform hover:scale-[1.02]">借りる</button>`;

                card.innerHTML = `
                    <div class="flex justify-between items-start mb-2 gap-2">
                        <h2 class="text-lg font-bold text-gray-800 leading-tight">${item.name}</h2>
                    </div>
                    <div class="mb-2">
                        ${statusBadge}
                    </div>
                    ${borrowerInfo}
                    ${actionButton}
                `;
                container.appendChild(card);
            });
        }

        // 教材を借りる処理
        function borrowItem(id) {
            const userNameInput = document.getElementById('userName').value.trim();
            
            if (!userNameInput) {
                showModal("利用者名を入力してから「借りる」ボタンを押してください。");
                return;
            }

            const item = materials.find(m => m.id === id);
            if (item && !item.isBorrowed) {
                item.isBorrowed = true;
                item.borrower = userNameInput;
                renderItems(); // 画面を更新
            }
        }

        // 教材を返却する処理
        function returnItem(id) {
            const item = materials.find(m => m.id === id);
            if (item && item.isBorrowed) {
                item.isBorrowed = false;
                item.borrower = "";
                renderItems(); // 画面を更新
            }
        }

        // カスタムモーダルを表示（alertの代替）
        function showModal(msg) {
            document.getElementById('messageText').innerText = msg;
            document.getElementById('messageModal').classList.remove('hidden');
        }

        // モーダルを閉じる
        function closeModal() {
            document.getElementById('messageModal').classList.add('hidden');
        }

        // ページ読み込み時に初期描画を実行
        window.onload = function() {
            renderItems();
        };
    </script>
</body>
</html>
