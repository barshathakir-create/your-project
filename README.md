<!DOCTYPE html>
<html lang="ne">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>परियोजना लागत र अर्डर ड्यासबोर्ड (NPR)</title>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background-color: #f0f2f5; margin: 20px; color: #333; }
        .container { max-width: 1100px; margin: 0 auto; display: grid; grid-template-columns: 1fr 1.5fr; gap: 25px; }
        .card { background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.08); }
        h2 { border-bottom: 2px solid #0056b3; padding-bottom: 10px; margin-top: 0; color: #0056b3; display: flex; justify-content: space-between; align-items: center; }
        table { width: 100%; border-collapse: collapse; margin-top: 15px; }
        th, td { border: 1px solid #dee2e6; padding: 12px; text-align: left; }
        th { background-color: #f8f9fa; color: #495057; }
        .kpi-container { display: flex; gap: 15px; margin-bottom: 20px; }
        .kpi { flex: 1; padding: 15px; background: #e9ecef; border-radius: 8px; text-align: center; }
        .kpi h3 { margin: 0; font-size: 14px; color: #495057; }
        .kpi p { margin: 8px 0 0 0; font-size: 24px; font-weight: bold; color: #0056b3; }
        .add-btn { background: #007bff; color: white; border: none; width: 40px; height: 40px; border-radius: 50%; cursor: pointer; font-size: 24px; font-weight: bold; display: flex; align-items: center; justify-content: center; transition: 0.2s; }
        .add-btn:hover { background: #0056b3; }
        .add-btn:disabled { background: #6c757d; cursor: not-allowed; }
        
        /* फाराम मोडाल (Form Popup) */
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); justify-content: center; align-items: center; z-index: 1000; }
        .modal-content { background: white; padding: 30px; border-radius: 10px; width: 400px; box-shadow: 0 5px 15px rgba(0,0,0,0.3); position: relative; }
        .close-btn { position: absolute; top: 15px; right: 20px; font-size: 24px; cursor: pointer; color: #6c757d; }
        .form-group { margin-bottom: 15px; }
        .form-group label { display: block; margin-bottom: 5px; font-weight: bold; }
        .form-group input, .form-group select { width: 100%; padding: 10px; border: 1px solid #ced4da; border-radius: 4px; box-sizing: border-box; }
        .submit-btn { background: #28a745; color: white; border: none; padding: 12px; width: 100%; border-radius: 4px; font-size: 16px; cursor: pointer; font-weight: bold; }
        
        .status-alert { padding: 12px; border-radius: 6px; font-weight: bold; text-align: center; margin-bottom: 20px; }
        .alert-ok { background: #d4edda; color: #155724; border: 1px solid #c3e6cb; }
        .alert-limit { background: #f8d7da; color: #721c24; border: 1px solid #f5c6cb; }
    </style>
</head>
<body>

<div class="container">
    
    <!-- १. लागत तालिका (NPR मा) -->
    <div class="card">
        <h2>लागत सूची (Cost Table)</h2>
        <table>
            <thead>
                <tr><th>शीर्षक (Item)</th><th>लागत (NPR)</th></tr>
            </thead>
            <tbody>
                <tr><td>Webpages</td><td>NPR 300</td></tr>
                <tr><td>Website</td><td>NPR 350</td></tr>
                <tr><td>PowerPoint Slide</td><td>NPR 150</td></tr>
                <tr><td>Excel File</td><td>NPR 100</td></tr>
                <tr><td>Word</td><td>NPR 80</td></tr>
                <tr style="font-weight: bold; background: #e9ecef;">
                    <td>Total Cost</td><td>NPR 980</td>
                </tr>
            </tbody>
        </table>
    </div>

    <!-- २. ड्यासबोर्ड र प्लस बटन -->
    <div class="card">
        <h2>
            <span>अर्डर ड्यासबोर्ड</span>
            <button id="openFormBtn" class="add-btn" onclick="toggleModal(true)">+</button>
        </h2>
        
        <div id="statusAlert" class="status-alert alert-ok">नयाँ अर्डर लिन खुल्ला छ</div>

        <div class="kpi-container">
            <div class="kpi">
                <h3>कुल अर्डर सीमा</h3>
                <p id="orderCount">0 / 10</p>
            </div>
            <div class="kpi">
                <h3>बाँकी समय</h3>
                <p id="timeLeft">४ दिन ००:००:००</p>
            </div>
        </div>

        <h3>ग्राहक अर्डर इतिहास</h3>
        <table id="orderTable">
            <thead>
                <tr>
                    <th>अर्डर नं.</th>
                    <th>ग्राहकको नाम</th>
                    <th>सेवा</th>
                    <th>मिति/समय</th>
                </tr>
            </thead>
            <tbody>
                <!-- यहाँ डेटा थपिन्छ -->
            </tbody>
        </table>
    </div>
</div>

<!-- अर्डर आवेदन फाराम (Popup Application Form) -->
<div id="orderModal" class="modal">
    <div class="modal-content">
        <span class="close-btn" onclick="toggleModal(false)">&times;</span>
        <h3 style="margin-top:0; border-bottom: 2px solid #28a745; padding-bottom: 10px;">अर्डर आवेदन फाराम</h3>
        <form id="orderForm" onsubmit="submitForm(event)">
            <div class="form-group">
                <label>ग्राहकको पूरा नाम:</label>
                <input type="text" id="custName" required placeholder="उदा: राम श्रेष्ठ">
            </div>
            <div class="form-group">
                <label>आवश्यक सेवा छनोट गर्नुहोस्:</label>
                <select id="serviceType" required>
                    <option value="Website">Website (NPR 350)</option>
                    <option value="Webpages">Webpages (NPR 300)</option>
                    <option value="PowerPoint Slide">PowerPoint Slide (NPR 150)</option>
                    <option value="Excel File">Excel File (NPR 100)</option>
                    <option value="Word Document">Word Document (NPR 80)</option>
                </select>
            </div>
            <button type="submit" class="submit-btn">अर्डर सुरक्षित गर्नुहोस्</button>
        </form>
    </div>
</div>

<script>
    let orders = [];
    const maxOrders = 10;
    let totalSeconds = 4 * 24 * 60 * 60; // ४ दिनको समय

    function toggleModal(show) {
        document.getElementById('orderModal').style.display = show ? 'flex' : 'none';
    }

    function updateDashboard() {
        document.getElementById('orderCount').innerText = `${orders.length} / ${maxOrders}`;
        const alertBox = document.getElementById('statusAlert');
        const openBtn = document.getElementById('openFormBtn');

        if (orders.length >= maxOrders) {
            alertBox.innerText = "अर्डर सीमा समाप्त भयो (१०/१० पुरा)";
            alertBox.className = "status-alert alert-limit";
            openBtn.disabled = true;
        } else if (totalSeconds <= 0) {
            alertBox.innerText = "समय सीमा समाप्त (४ दिन पुरा)";
            alertBox.className = "status-alert alert-limit";
            openBtn.disabled = true;
        } else {
            alertBox.innerText = "नयाँ अर्डर लिन खुल्ला छ (सक्रिय)";
            alertBox.className = "status-alert alert-ok";
            openBtn.disabled = false;
        }
    }

    function submitForm(event) {
        event.preventDefault();
        if (orders.length >= maxOrders || totalSeconds <= 0) return;

        const name = document.getElementById('custName').value;
        const service = document.getElementById('serviceType').value;
        const now = new Date();
        const timeStr = now.toLocaleTimeString() + " (" + now.toLocaleDateString() + ")";

        orders.push({ name, service, timeStr });

        const tableBody = document.getElementById('orderTable').getElementsByTagName('tbody');
        const row = tableBody.insertRow();
        row.insertCell(0).innerText = `ORD-${orders.length.toString().padStart(3, '0')}`;
        row.insertCell(1).innerText = name;
        row.insertCell(2).innerText = service;
        row.insertCell(3).innerText = timeStr;

        document.getElementById('orderForm').reset();
        toggleModal(false);
        updateDashboard();
    }

    // समय गणना (Countdown)
    setInterval(() => {
        if (totalSeconds > 0) {
            totalSeconds--;
            const days = Math.floor(totalSeconds / (24 * 3600));
            const hours = Math.floor((totalSeconds % (24 * 3600)) / 3600);
            const minutes = Math.floor((totalSeconds % 3600) / 60);
            const seconds = totalSeconds % 60;

            document.getElementById('timeLeft').innerText = 
                `${days} दिन ${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
        } else {
            updateDashboard();
        }
    }, 1000);

    updateDashboard();
</script>

</body>
</html>
