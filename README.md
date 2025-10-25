<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบติดตามอุณหภูมิ</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            max-width: 500px;
            margin: 0 auto;
        }
        
        .header {
            text-align: center;
            margin-bottom: 20px;
            padding: 20px 0;
        }
        
        .header h1 {
            font-size: 28px;
            margin-bottom: 10px;
            font-weight: 700;
        }
        
        .project-info {
            background: rgba(255, 255, 255, 0.1);
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            text-align: center;
            font-size: 14px;
            line-height: 1.5;
        }
        
        .card {
            background: rgba(255, 255, 255, 0.15);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
        }
        
        .card-title {
            font-size: 20px;
            font-weight: 600;
            margin-bottom: 20px;
            text-align: center;
        }
        
        .temp-display {
            display: flex;
            justify-content: space-around;
            text-align: center;
            gap: 20px;
        }
        
        .temp-item {
            flex: 1;
        }
        
        .temp-value {
            font-size: 42px;
            font-weight: 700;
            margin-bottom: 5px;
        }
        
        .temp-label {
            font-size: 14px;
            opacity: 0.9;
        }
        
        .fan-status {
            text-align: center;
        }
        
        .fan-value {
            font-size: 48px;
            font-weight: 700;
            margin-bottom: 10px;
        }
        
        .fan-label {
            font-size: 16px;
            opacity: 0.9;
            margin-bottom: 15px;
        }
        
        .indicators {
            display: flex;
            justify-content: center;
            gap: 10px;
        }
        
        .indicator {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            background-color: #e74c3c;
        }
        
        .chart-container {
            height: 200px;
            margin-top: 10px;
        }
        
        .update-time {
            text-align: center;
            margin-top: 20px;
            font-size: 12px;
            opacity: 0.7;
        }

        @media (max-width: 480px) {
            .temp-value {
                font-size: 36px;
            }
            
            .fan-value {
                font-size: 40px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>ระบบติดตามอุณหภูมิ</h1>
        </div>
        
        <div class="project-info">
            การพัฒนาระบบควบคุมพัดลมระบายความร้อนอัตโนมัติสำหรับมอเตอร์โดยใช้ไมโครคอนโทรลเลอร์และเซนเซอร์วัดอุณหภูมิ
        </div>
        
        <div class="card">
            <div class="card-title">อุณหภูมิปัจจุบัน</div>
            <div class="temp-display">
                <div class="temp-item">
                    <div class="temp-value" id="motorTemp">42</div>
                    <div class="temp-label">Motor Temp. (°C)</div>
                </div>
                <div class="temp-item">
                    <div class="temp-value" id="ambientTemp">26</div>
                    <div class="temp-label">Ambient Temp. (°C)</div>
                </div>
            </div>
        </div>
        
        <div class="card">
            <div class="card-title">สถานะพัดลม</div>
            <div class="fan-status">
                <div class="fan-value" id="fanStatus">0%</div>
                <div class="fan-label">Fan Status</div>
                <div class="indicators">
                    <div class="indicator" id="indicator1"></div>
                    <div class="indicator" id="indicator2"></div>
                    <div class="indicator" id="indicator3"></div>
                </div>
            </div>
        </div>
        
        <div class="card">
            <div class="card-title">กราฟอุณหภูมิ</div>
            <div class="chart-container">
                <canvas id="tempChart"></canvas>
            </div>
        </div>
        
        <div class="update-time" id="updateTime">
            อัพเดตล่าสุด: กำลังโหลด...
        </div>
    </div>

    <script>
        // ตั้งค่ากราฟ
        const ctx = document.getElementById('tempChart').getContext('2d');
        const tempChart = new Chart(ctx, {
            type: 'line',
            data: {
                labels: ['10:00', '10:05', '10:10', '10:15', '10:20', '10:25', '10:30'],
                datasets: [
                    {
                        label: 'Motor Temp (°C)',
                        data: [38, 40, 41, 42, 41, 40, 42],
                        borderColor: '#ff6b6b',
                        backgroundColor: 'rgba(255, 107, 107, 0.1)',
                        tension: 0.4,
                        fill: true,
                        borderWidth: 2
                    },
                    {
                        label: 'Ambient Temp (°C)',
                        data: [24, 25, 25, 26, 25, 25, 26],
                        borderColor: '#4ecdc4',
                        backgroundColor: 'rgba(78, 205, 196, 0.1)',
                        tension: 0.4,
                        fill: true,
                        borderWidth: 2
                    }
                ]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: {
                    legend: {
                        labels: {
                            color: 'white',
                            font: {
                                size: 12
                            }
                        }
                    }
                },
                scales: {
                    y: {
                        beginAtZero: false,
                        min: 20,
                        max: 45,
                        grid: {
                            color: 'rgba(255, 255, 255, 0.1)'
                        },
                        ticks: {
                            color: 'white',
                            stepSize: 5
                        }
                    },
                    x: {
                        grid: {
                            color: 'rgba(255, 255, 255, 0.1)'
                        },
                        ticks: {
                            color: 'white'
                        }
                    }
                }
            }
        });

        // ฟังก์ชันอัพเดตข้อมูล
        function updateData() {
            const motorTemp = Math.floor(Math.random() * 5) + 40;
            const ambientTemp = Math.floor(Math.random() * 3) + 24;
            const fanStatus = Math.random() > 0.8 ? 100 : 0;
            
            // อัพเดตอุณหภูมิ
            document.getElementById('motorTemp').textContent = motorTemp;
            document.getElementById('ambientTemp').textContent = ambientTemp;
            document.getElementById('fanStatus').textContent = fanStatus + '%';
            
            // อัพเดตอินดิเคเตอร์พัดลม
            const indicators = document.querySelectorAll('.indicator');
            if (fanStatus === 0) {
                indicators.forEach(ind => ind.style.backgroundColor = '#e74c3c');
            } else {
                indicators.forEach(ind => ind.style.backgroundColor = '#2ecc71');
            }
            
            // อัพเดตกราฟ
            const now = new Date();
            const timeLabel = `${now.getHours()}:${now.getMinutes().toString().padStart(2, '0')}`;
            
            tempChart.data.labels.push(timeLabel);
            tempChart.data.datasets[0].data.push(motorTemp);
            tempChart.data.datasets[1].data.push(ambientTemp);
            
            // จำกัดจำนวนข้อมูลในกราฟ
            if (tempChart.data.labels.length > 8) {
                tempChart.data.labels.shift();
                tempChart.data.datasets[0].data.shift();
                tempChart.data.datasets[1].data.shift();
            }
            
            tempChart.update();
            
            // อัพเดตเวลา
            document.getElementById('updateTime').textContent = 
                `อัพเดตล่าสุด: ${now.toLocaleDateString('th-TH')} - ${now.toLocaleTimeString('th-TH')}`;
        }

        // อัพเดตทุก 3 วินาที
        setInterval(updateData, 3000);
        updateData();
    </script>
</body>
</html><!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบติดตามอุณหภูมิ</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            max-width: 500px;
            margin: 0 auto;
        }
        
        .header {
            text-align: center;
            margin-bottom: 20px;
            padding: 20px 0;
        }
        
        .header h1 {
            font-size: 28px;
            margin-bottom: 10px;
            font-weight: 700;
        }
        
        .project-info {
            background: rgba(255, 255, 255, 0.1);
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            text-align: center;
            font-size: 14px;
            line-height: 1.5;
        }
        
        .card {
            background: rgba(255, 255, 255, 0.15);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
        }
        
        .card-title {
            font-size: 20px;
            font-weight: 600;
            margin-bottom: 20px;
            text-align: center;
        }
        
        .temp-display {
            display: flex;
            justify-content: space-around;
            text-align: center;
            gap: 20px;
        }
        
        .temp-item {
            flex: 1;
        }
        
        .temp-value {
            font-size: 42px;
            font-weight: 700;
            margin-bottom: 5px;
        }
        
        .temp-label {
            font-size: 14px;
            opacity: 0.9;
        }
        
        .fan-status {
            text-align: center;
        }
        
        .fan-value {
            font-size: 48px;
            font-weight: 700;
            margin-bottom: 10px;
        }
        
        .fan-label {
            font-size: 16px;
            opacity: 0.9;
            margin-bottom: 15px;
        }
        
        .indicators {
            display: flex;
            justify-content: center;
            gap: 10px;
        }
        
        .indicator {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            background-color: #e74c3c;
        }
        
        .chart-container {
            height: 200px;
            margin-top: 10px;
        }
        
        .update-time {
            text-align: center;
            margin-top: 20px;
            font-size: 12px;
            opacity: 0.7;
        }

        @media (max-width: 480px) {
            .temp-value {
                font-size: 36px;
            }
            
            .fan-value {
                font-size: 40px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>ระบบติดตามอุณหภูมิ</h1>
        </div>
        
        <div class="project-info">
            การพัฒนาระบบควบคุมพัดลมระบายความร้อนอัตโนมัติสำหรับมอเตอร์โดยใช้ไมโครคอนโทรลเลอร์และเซนเซอร์วัดอุณหภูมิ
        </div>
        
        <div class="card">
            <div class="card-title">อุณหภูมิปัจจุบัน</div>
            <div class="temp-display">
                <div class="temp-item">
                    <div class="temp-value" id="motorTemp">42</div>
                    <div class="temp-label">Motor Temp. (°C)</div>
                </div>
                <div class="temp-item">
                    <div class="temp-value" id="ambientTemp">26</div>
                    <div class="temp-label">Ambient Temp. (°C)</div>
                </div>
            </div>
        </div>
        
        <div class="card">
            <div class="card-title">สถานะพัดลม</div>
            <div class="fan-status">
                <div class="fan-value" id="fanStatus">0%</div>
                <div class="fan-label">Fan Status</div>
                <div class="indicators">
                    <div class="indicator" id="indicator1"></div>
                    <div class="indicator" id="indicator2"></div>
                    <div class="indicator" id="indicator3"></div>
                </div>
            </div>
        </div>
        
        <div class="card">
            <div class="card-title">กราฟอุณหภูมิ</div>
            <div class="chart-container">
                <canvas id="tempChart"></canvas>
            </div>
        </div>
        
        <div class="update-time" id="updateTime">
            อัพเดตล่าสุด: กำลังโหลด...
        </div>
    </div>

    <script>
        // ตั้งค่ากราฟ
        const ctx = document.getElementById('tempChart').getContext('2d');
        const tempChart = new Chart(ctx, {
            type: 'line',
            data: {
                labels: ['10:00', '10:05', '10:10', '10:15', '10:20', '10:25', '10:30'],
                datasets: [
                    {
                        label: 'Motor Temp (°C)',
                        data: [38, 40, 41, 42, 41, 40, 42],
                        borderColor: '#ff6b6b',
                        backgroundColor: 'rgba(255, 107, 107, 0.1)',
                        tension: 0.4,
                        fill: true,
                        borderWidth: 2
                    },
                    {
                        label: 'Ambient Temp (°C)',
                        data: [24, 25, 25, 26, 25, 25, 26],
                        borderColor: '#4ecdc4',
                        backgroundColor: 'rgba(78, 205, 196, 0.1)',
                        tension: 0.4,
                        fill: true,
                        borderWidth: 2
                    }
                ]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: {
                    legend: {
                        labels: {
                            color: 'white',
                            font: {
                                size: 12
                            }
                        }
                    }
                },
                scales: {
                    y: {
                        beginAtZero: false,
                        min: 20,
                        max: 45,
                        grid: {
                            color: 'rgba(255, 255, 255, 0.1)'
                        },
                        ticks: {
                            color: 'white',
                            stepSize: 5
                        }
                    },
                    x: {
                        grid: {
                            color: 'rgba(255, 255, 255, 0.1)'
                        },
                        ticks: {
                            color: 'white'
                        }
                    }
                }
            }
        });

        // ฟังก์ชันอัพเดตข้อมูล
        function updateData() {
            const motorTemp = Math.floor(Math.random() * 5) + 40;
            const ambientTemp = Math.floor(Math.random() * 3) + 24;
            const fanStatus = Math.random() > 0.8 ? 100 : 0;
            
            // อัพเดตอุณหภูมิ
            document.getElementById('motorTemp').textContent = motorTemp;
            document.getElementById('ambientTemp').textContent = ambientTemp;
            document.getElementById('fanStatus').textContent = fanStatus + '%';
            
            // อัพเดตอินดิเคเตอร์พัดลม
            const indicators = document.querySelectorAll('.indicator');
            if (fanStatus === 0) {
                indicators.forEach(ind => ind.style.backgroundColor = '#e74c3c');
            } else {
                indicators.forEach(ind => ind.style.backgroundColor = '#2ecc71');
            }
            
            // อัพเดตกราฟ
            const now = new Date();
            const timeLabel = `${now.getHours()}:${now.getMinutes().toString().padStart(2, '0')}`;
            
            tempChart.data.labels.push(timeLabel);
            tempChart.data.datasets[0].data.push(motorTemp);
            tempChart.data.datasets[1].data.push(ambientTemp);
            
            // จำกัดจำนวนข้อมูลในกราฟ
            if (tempChart.data.labels.length > 8) {
                tempChart.data.labels.shift();
                tempChart.data.datasets[0].data.shift();
                tempChart.data.datasets[1].data.shift();
            }
            
            tempChart.update();
            
            // อัพเดตเวลา
            document.getElementById('updateTime').textContent = 
                `อัพเดตล่าสุด: ${now.toLocaleDateString('th-TH')} - ${now.toLocaleTimeString('th-TH')}`;
        }

        // อัพเดตทุก 3 วินาที
        setInterval(updateData, 3000);
        updateData();
    </script>
</body>
</html>
