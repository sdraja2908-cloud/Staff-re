<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Staff Compliment Portal</title>
    <style>
        :root { --gold: #ffcc00; --primary: #2563eb; --bg: #f3f4f6; }
        body { font-family: 'Inter', sans-serif; background: var(--bg); padding: 20px; display: flex; flex-direction: column; align-items: center; }
        
        /* Main Form */
        .card { background: white; padding: 30px; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.1); width: 100%; max-width: 500px; }
        h2 { text-align: center; color: #1f2937; margin-bottom: 25px; }
        
        .field { margin-bottom: 20px; }
        label { display: block; font-weight: 600; margin-bottom: 8px; color: #4b5563; }
        input[type="text"] { width: 100%; padding: 12px; border: 1px solid #d1d5db; border-radius: 8px; box-sizing: border-box; }

        /* Star Rating Styling */
        .star-container { display: flex; flex-direction: row-reverse; justify-content: flex-end; gap: 5px; }
        .star-container input { display: none; }
        .star-container label { font-size: 32px; color: #d1d5db; cursor: pointer; }
        .star-container input:checked ~ label,
        .star-container label:hover,
        .star-container label:hover ~ label { color: var(--gold); }

        button { width: 100%; padding: 14px; background: var(--primary); color: white; border: none; border-radius: 8px; font-weight: bold; cursor: pointer; transition: 0.3s; margin-top: 10px; }
        button:hover { background: #1d4ed8; }
        .btn-secondary { background: #64748b; margin-top: 5px; }

        /* Admin Dashboard (Hidden) */
        #adminSection { display: none; width: 100%; max-width: 900px; margin-top: 40px; animation: fadeIn 0.5s; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        
        table { width: 100%; background: white; border-collapse: collapse; border-radius: 10px; overflow: hidden; box-shadow: 0 4px 12px rgba(0,0,0,0.05); }
        th, td { padding: 15px; text-align: left; border-bottom: 1px solid #edf2f7; }
        th { background: #1f2937; color: white; }
        .stars { color: var(--gold); letter-spacing: 2px; }
        .admin-trigger { margin-top: 60px; color: #9ca3af; font-size: 11px; cursor: pointer; text-decoration: none; border: none; background: none; }
    </style>
</head>
<body>

<div class="card">
    <h2>Staff Peer Review</h2>
    
    <div class="field">
        <label>Your Name (From)</label>
        <input type="text" id="fromStaff" placeholder="Type your name...">
    </div>

    <div class="field">
        <label>Colleague's Name (To Whom)</label>
        <input type="text" id="toStaff" placeholder="Who are you rating?">
    </div>

    <div class="field">
        <label>Behavior Rating</label>
        <div class="star-container">
            <input type="radio" name="b-star" id="b5" value="5"><label for="b5">★</label>
            <input type="radio" name="b-star" id="b4" value="4"><label for="b4">★</label>
            <input type="radio" name="b-star" id="b3" value="3"><label for="b3">★</label>
            <input type="radio" name="b-star" id="b2" value="2"><label for="b2">★</label>
            <input type="radio" name="b-star" id="b1" value="1"><label for="b1">★</label>
        </div>
    </div>

    <div class="field">
        <label>Working Efficiency</label>
        <div class="star-container">
            <input type="radio" name="w-star" id="w5" value="5"><label for="w5">★</label>
            <input type="radio" name="w-star" id="w4" value="4"><label for="w4">★</label>
            <input type="radio" name="w-star" id="w3" value="3"><label for="w3">★</label>
            <input type="radio" name="w-star" id="w2" value="2"><label for="w2">★</label>
            <input type="radio" name="w-star" id="w1" value="1"><label for="w1">★</label>
        </div>
    </div>

    <button onclick="submitReport()">Submit Review</button>
</div>

<button class="admin-trigger" onclick="unlockAdmin()">Developer Console Access</button>

<div id="adminSection">
    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px;">
        <h2 style="margin: 0;">Performance Records</h2>
        <div>
            <button class="btn-secondary" style="width: auto; padding: 8px 15px;" onclick="window.print()">Print as PDF</button>
            <button style="width: auto; background: #dc2626; padding: 8px 15px;" onclick="clearData()">Clear All</button>
        </div>
    </div>
    <table>
        <thead>
            <tr>
                <th>Date</th>
                <th>From</th>
                <th>To</th>
                <th>Behavior</th>
                <th>Efficiency</th>
            </tr>
        </thead>
        <tbody id="adminTableBody"></tbody>
    </table>
</div>

<script>
    // Updated Password as requested
    const DEV_PASSWORD = "raja1234@";

    function submitReport() {
        const from = document.getElementById('fromStaff').value;
        const to = document.getElementById('toStaff').value;
        const bRating = document.querySelector('input[name="b-star"]:checked')?.value;
        const wRating = document.querySelector('input[name="w-star"]:checked')?.value;
        const date = new Date().toLocaleDateString();

        if (!from || !to || !bRating || !wRating) {
            alert("All fields and star ratings are required.");
            return;
        }

        const report = { from, to, bRating, wRating, date };
        let allReports = JSON.parse(localStorage.getItem('secureReports')) || [];
        allReports.push(report);
        localStorage.setItem('secureReports', JSON.stringify(allReports));

        alert("Thank you! Your feedback has been submitted.");
        location.reload(); 
    }

    function unlockAdmin() {
        const pass = prompt("Developer Authentication Required:");
        if (pass === DEV_PASSWORD) {
            document.getElementById('adminSection').style.display = 'block';
            loadAdminData();
            window.scrollTo(0, document.body.scrollHeight);
        } else {
            alert("Incorrect Password. Access Denied.");
        }
    }

    function loadAdminData() {
        const reports = JSON.parse(localStorage.getItem('secureReports')) || [];
        const tbody = document.getElementById('adminTableBody');
        tbody.innerHTML = "";

        reports.forEach(r => {
            tbody.innerHTML += `
                <tr>
                    <td>${r.date}</td>
                    <td>${r.from}</td>
                    <td><strong>${r.to}</strong></td>
                    <td class="stars">${"★".repeat(r.bRating)}</td>
                    <td class="stars">${"★".repeat(r.wRating)}</td>
                </tr>
            `;
        });
    }

    function clearData() {
        if(confirm("Permanently delete all staff records?")) {
            localStorage.removeItem('secureReports');
            loadAdminData();
        }
    }
</script>

</body>
</html>
