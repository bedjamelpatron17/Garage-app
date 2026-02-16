<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="theme-color" content="#e74c3c">
    <link rel="manifest" href="manifest.json">
    <title>Bedjam's Garage</title>
    <style>
        body { font-family: -apple-system, system-ui; background: #1a1a1a; color: white; padding: 15px; margin: 0; }
        .car-card { background: #2d2d2d; padding: 15px; border-radius: 12px; margin-bottom: 20px; border-top: 4px solid #e74c3c; box-shadow: 0 4px 10px rgba(0,0,0,0.5); }
        h1 { font-size: 1.5rem; text-align: center; color: #ecf0f1; }
        h2 { margin: 0 0 10px 0; font-size: 1.1rem; color: #e74c3c; }
        label { font-size: 0.75rem; color: #bdc3c7; text-transform: uppercase; font-weight: bold; }
        input, select, textarea { width: 100%; background: #444; border: 1px solid #555; color: white; padding: 10px; border-radius: 6px; margin: 5px 0 15px 0; box-sizing: border-box; }
        .btn { display: block; text-align: center; background: #e74c3c; color: white; padding: 12px; text-decoration: none; border-radius: 8px; font-weight: bold; border: none; width: 100%; }
        table { width: 100%; border-collapse: collapse; font-size: 0.85rem; margin-top: 10px; }
        th { text-align: left; border-bottom: 2px solid #555; padding-bottom: 5px; color: #bdc3c7; }
        td { padding: 8px 0; border-bottom: 1px solid #3d3d3d; }
    </style>
</head>
<body>
    <h1>🔧 Bedjam's Garage</h1>

    <div class="car-card">
        <h2>Mazda 3 Torque Specs</h2>
        <label>Camshaft (ft-lb)</label>
        <input type="number" id="m3-cam" placeholder="Enter spec..." oninput="saveData('m3-cam')">
        <label>Flywheel (ft-lb)</label>
        <input type="number" id="m3-flywheel" placeholder="Enter spec..." oninput="saveData('m3-flywheel')">
        <a href="https://www.densoproducts.com" target="_blank" class="btn">Search Denso Parts</a>
    </div>

    <div class="car-card">
        <h2>🛠 Maintenance Log</h2>
        <select id="log-vehicle">
            <option>2002 Mazda Protege</option>
            <option>2011 Mazda 3</option>
            <option>2007 Toyota RAV4</option>
            <option>2019 Hyundai Ioniq</option>
        </select>
        <input type="text" id="log-task" placeholder="What did you fix?">
        <button onclick="addLog()" class="btn">Add Entry</button>
        
        <table>
            <thead><tr><th>Vehicle</th><th>Task</th><th>Date</th></tr></thead>
            <tbody id="log-body"></tbody>
        </table>
    </div>

    <script>
        // PWA Service Worker Registration
        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('sw.js');
        }

        // Local Storage Logic
        function saveData(id) {
            localStorage.setItem(id, document.getElementById(id).value);
        }

        function addLog() {
            const vehicle = document.getElementById('log-vehicle').value;
            const task = document.getElementById('log-task').value;
            if(!task) return;
            const logs = JSON.parse(localStorage.getItem('garage-logs') || '[]');
            logs.unshift({ vehicle, task, date: new Date().toLocaleDateString() });
            localStorage.setItem('garage-logs', JSON.stringify(logs));
            document.getElementById('log-task').value = "";
            renderLogs();
        }

        function renderLogs() {
            const logs = JSON.parse(localStorage.getItem('garage-logs') || '[]');
            document.getElementById('log-body').innerHTML = logs.map(l => 
                `<tr><td>${l.vehicle}</td><td>${l.task}</td><td>${l.date}</td></tr>`
            ).join('');
            
            // Reload simple inputs
            ['m3-cam', 'm3-flywheel'].forEach(id => {
                if(localStorage.getItem(id)) document.getElementById(id).value = localStorage.getItem(id);
            });
        }

        window.onload = renderLogs;
    </script>
</body>
</html>
# Garage-app