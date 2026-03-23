# daily-solver-
An all-in-one daily life toolkit to stay organized and make quick decisions.
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Daily Problem Solver</title>
    <style>
        :root {
            --primary: #4F46E5;
            --bg: #F3F4F6;
            --card: #FFFFFF;
            --text: #1F2937;
            --border: #E5E7EB;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
        }

        .container {
            max-width: 800px;
            width: 100%;
            display: grid;
            gap: 20px;
            grid-template-columns: 1fr;
        }

        @media (min-width: 768px) {
            .container {
                grid-template-columns: 1fr 1fr;
            }
            .full-width {
                grid-column: 1 / -1;
            }
        }

        .card {
            background-color: var(--card);
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            border: 1px solid var(--border);
        }

        h1 {
            text-align: center;
            color: var(--primary);
            margin-bottom: 5px;
        }
        
        .header-desc {
            text-align: center;
            color: #6B7280;
            margin-bottom: 20px;
            font-size: 0.9em;
        }

        h2 {
            font-size: 1.25rem;
            margin-top: 0;
            border-bottom: 2px solid var(--border);
            padding-bottom: 10px;
        }

        input[type="text"], textarea {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
            border: 1px solid var(--border);
            border-radius: 6px;
            box-sizing: border-box;
            font-family: inherit;
        }

        button {
            background-color: var(--primary);
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 6px;
            cursor: pointer;
            width: 100%;
            font-weight: bold;
            transition: background-color 0.2s;
        }

        button:hover {
            background-color: #4338CA;
        }

        /* Task List Styles */
        ul {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }

        li {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px;
            background-color: var(--bg);
            margin-bottom: 8px;
            border-radius: 6px;
        }

        .delete-btn {
            background-color: #EF4444;
            width: auto;
            padding: 5px 10px;
            font-size: 0.8rem;
        }

        .delete-btn:hover {
            background-color: #DC2626;
        }

        /* Decision Maker Styles */
        #decision-result {
            margin-top: 15px;
            padding: 15px;
            background-color: #EEF2FF;
            border: 1px dashed var(--primary);
            border-radius: 6px;
            text-align: center;
            font-weight: bold;
            min-height: 20px;
        }
    </style>
</head>
<body>

    <div class="container">
        <div class="full-width">
            <h1>Daily Problem Solver</h1>
            <div class="header-desc">A toolkit to organize your day, clear your mind, and make decisions.</div>
        </div>

        <div class="card">
            <h2>📝 Task Manager</h2>
            <p style="font-size: 0.85em; color: #6B7280;">Problem: Forgetting what needs to be done.</p>
            <div style="display: flex; gap: 10px;">
                <input type="text" id="task-input" placeholder="Enter a new task...">
                <button onclick="addTask()" style="width: auto;">Add</button>
            </div>
            <ul id="task-list"></ul>

</div>

        <div class="card">
            <h2>⚖️ Decision Maker</h2>
            <p style="font-size: 0.85em; color: #6B7280;">Problem: Wasting time deciding what to do/eat/watch.</p>
            <textarea id="options-input" rows="3" placeholder="Enter options separated by commas (e.g., Pizza, Burgers, Tacos)"></textarea>
            <button onclick="makeDecision()">Decide For Me</button>
            <div id="decision-result">Awaiting your options...</div>
        </div>

        <div class="card full-width">
            <h2>🧠 Quick Scratchpad</h2>
            <p style="font-size: 0.85em; color: #6B7280;">Problem: Losing track of a sudden idea or phone number.</p>
            <textarea id="scratchpad" rows="6" placeholder="Type your notes, ideas, or reminders here. They save automatically..."></textarea>
        </div>
    </div>

    <script>
        // --- 1. Task Manager Logic ---
        const taskInput = document.getElementById('task-input');
        const taskList = document.getElementById('task-list');
        let tasks = JSON.parse(localStorage.getItem('dailyTasks')) || [];

        function renderTasks() {
            taskList.innerHTML = '';
            tasks.forEach((task, index) => {
                const li = document.createElement('li');
                li.textContent = task;
                
                const deleteBtn = document.createElement('button');
                deleteBtn.textContent = 'Done';
                deleteBtn.className = 'delete-btn';
                deleteBtn.onclick = () => removeTask(index);
                
                li.appendChild(deleteBtn);
                taskList.appendChild(li);
            });
        }

        function addTask() {
            const text = taskInput.value.trim();
            if (text !== '') {
                tasks.push(text);
                localStorage.setItem('dailyTasks', JSON.stringify(tasks));
                taskInput.value = '';
                renderTasks();
            }
        }

        function removeTask(index) {
            tasks.splice(index, 1);
            localStorage.setItem('dailyTasks', JSON.stringify(tasks));
            renderTasks();
        }

        // Allow pressing "Enter" to add a task
        taskInput.addEventListener('keypress', function (e) {
            if (e.key === 'Enter') addTask();
        });

        // --- 2. Decision Maker Logic ---
        function makeDecision() {
            const input = document.getElementById('options-input').value;
            const resultDiv = document.getElementById('decision-result');
            
            if (!input.trim()) {
                resultDiv.textContent = "Please enter some options first!";
                resultDiv.style.color = "#EF4444";
                return;
            }

            // Split by comma, remove empty spaces
            const options = input.split(',').map(opt => opt.trim()).filter(opt => opt !== '');
            
            if (options.length === 0) {
                resultDiv.textContent = "Please enter valid options separated by commas.";
                return;
            }

            // Pick a random option
            const randomIndex = Math.floor(Math.random() * options.length);
            resultDiv.style.color = "inherit";
            resultDiv.innerHTML = I have decided: <br><span style="font-size: 1.5em; color: var(--primary);">${options[randomIndex]}</span>;
        }

        // --- 3. Scratchpad Logic (Auto-save) ---
        const scratchpad = document.getElementById('scratchpad');
        
        // Load saved notes on startup
        scratchpad.value = localStorage.getItem('dailyNotes') || '';

        // Save notes every time the user types
        scratchpad.addEventListener('input', function() {
            localStorage.setItem('dailyNotes', this.value);
        });

        // Initialize the app
        renderTasks();
    </script>
</body>
</html>