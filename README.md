<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FocusFlow - Odak & Görev Asistanı</title>
    <style>
        :root {
            --bg-color: #0f172a;
            --card-bg: #1e293b;
            --text-color: #f8fafc;
            --accent-color: #6366f1;
            --accent-hover: #4f46e5;
            --border-color: #334155;
            --danger: #ef4444;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .container {
            width: 100%;
            max-width: 480px;
            background: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.3);
        }

        header {
            text-align: center;
            margin-bottom: 25px;
        }

        header h1 {
            font-size: 1.5rem;
            font-weight: 700;
            letter-spacing: 0.5px;
            color: #818cf8;
        }

        .timer-display {
            text-align: center;
            font-size: 4rem;
            font-weight: 800;
            margin: 20px 0;
            letter-spacing: 2px;
            color: var(--text-color);
        }

        .controls {
            display: flex;
            gap: 12px;
            justify-content: center;
            margin-bottom: 30px;
        }

        button {
            background: var(--accent-color);
            color: white;
            border: none;
            padding: 12px 24px;
            font-size: 1rem;
            font-weight: 600;
            border-radius: 10px;
            cursor: pointer;
            transition: background 0.2s, transform 0.1s;
        }

        button:hover {
            background: var(--accent-hover);
        }

        button:active {
            transform: scale(0.97);
        }

        button.secondary {
            background: transparent;
            border: 1px solid var(--border-color);
            color: var(--text-color);
        }

        button.secondary:hover {
            background: var(--border-color);
        }

        .todo-section h2 {
            font-size: 1.1rem;
            margin-bottom: 15px;
            color: #94a3b8;
        }

        .todo-input-group {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }

        input[type="text"] {
            flex: 1;
            background: var(--bg-color);
            border: 1px solid var(--border-color);
            padding: 12px;
            border-radius: 10px;
            color: var(--text-color);
            font-size: 0.95rem;
        }

        input[type="text"]:focus {
            outline: none;
            border-color: var(--accent-color);
        }

        ul {
            list-style: none;
            max-height: 180px;
            overflow-y: auto;
        }

        li {
            background: var(--bg-color);
            padding: 10px 14px;
            border-radius: 8px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 8px;
            border: 1px solid var(--border-color);
            font-size: 0.95rem;
        }

        li.completed span {
            text-decoration: line-through;
            color: #64748b;
        }

        .actions {
            display: flex;
            gap: 8px;
        }

        .delete-btn {
            background: transparent;
            color: var(--danger);
            padding: 4px 8px;
            font-size: 0.85rem;
        }
        
        .delete-btn:hover {
            background: rgba(239, 68, 68, 0.1);
        }
    </style>
</head>
<body>

    <div class="container">
        <header>
            <h1>⚡ FocusFlow</h1>
        </header>

        <div class="timer-display" id="timer">25:00</div>

        <div class="controls">
            <button id="startBtn" onclick="toggleTimer()">Başlat</button>
            <button class="secondary" onclick="resetTimer()">Sıfırla</button>
        </div>

        <div class="todo-section">
            <h2>Günün Hedefleri</h2>
            <div class="todo-input-group">
                <input type="text" id="taskInput" placeholder="Yeni bir odak hedefi ekle...">
                <button onclick="addTask()">Ekle</button>
            </div>
            <ul id="taskList"></ul>
        </div>
    </div>

    <script>
        let timeLeft = 1500;
        let timerId = null;
        let isRunning = false;

        const timerDisplay = document.getElementById('timer');
        const startBtn = document.getElementById('startBtn');
        const taskInput = document.getElementById('taskInput');
        const taskList = document.getElementById('taskList');

        function updateDisplay() {
            const minutes = Math.floor(timeLeft / 60);
            const seconds = timeLeft % 60;
            timerDisplay.textContent = `${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
        }

        function toggleTimer() {
            if (isRunning) {
                clearInterval(timerId);
                startBtn.textContent = 'Başlat';
                isRunning = false;
            } else {
                startBtn.textContent = 'Duraklat';
                isRunning = true;
                timerId = setInterval(() => {
                    if (timeLeft > 0) {
                        timeLeft--;
                        updateDisplay();
                    } else {
                        clearInterval(timerId);
                        alert('Süre bitti! Harika odaklandın, şimdi kısa bir mola verme vakti.');
                        resetTimer();
                    }
                }, 1000);
            }
        }

        function resetTimer() {
            clearInterval(timerId);
            isRunning = false;
            timeLeft = 1500;
            startBtn.textContent = 'Başlat';
            updateDisplay();
        }

        function addTask() {
            const text = taskInput.value.trim();
            if (!text) return;

            const li = document.createElement('li');
            li.innerHTML = `
                <span onclick="toggleTask(this)" style="cursor: pointer; flex: 1;">${text}</span>
                <div class="actions">
                    <button class="delete-btn" onclick="this.parentElement.parentElement.remove()">Sil</button>
                </div>
            `;
            taskList.appendChild(li);
            taskInput.value = '';
        }

        function toggleTask(element) {
            element.parentElement.classList.toggle('completed');
        }

        taskInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') addTask();
        });
    </script>
</body>
</html>
