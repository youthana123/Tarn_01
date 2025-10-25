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
            background-color: #f5f5f5;
            color: #333;
            padding: 20px;
            max-width: 500px;
            margin: 0 auto;
        }
        
        .header {
            text-align: center;
            margin-bottom: 30px;
        }
        
        .header h1 {
            font-size: 24px;
            color: #2c3e50;
            margin-bottom: 5px;
        }
        
        .header p {
            color: #7f8c8d;
            font-size: 14px;
        }
        
        .card {
            background-color: white;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
            padding: 20px;
            margin-bottom: 20px;
        }
        
        .card-title {
            font-size: 18px;
            font-weight: 600;
            margin-bottom: 15px;
            color: #2c3e50;
        }
        
        .temp-display {
            display: flex;
            justify-content: space-between;
            margin-bottom: 20px;
        }
        
        .temp-item {
            text-align: center;
            flex: 1;
        }
        
        .temp-value {
            font-size: 36px;
            font-weight: 700;
            margin-bottom: 5px;
        }
        
        .temp-label {
            font-size: 14px;
            color: #7f8c8d;
        }
        
        .motor-temp {
            color: #e74c3c;
        }
        
        .ambient-temp {
            color: #3498db;
        }
        
        .fan-status {
            text-align: center;
            margin-top: 10px;
        }
        
        .fan-value {
            font-size: 32px;
            font-weight: 700;
            margin-bottom: 5px;
        }
        
        .fan-label {
            font-size: 14px;
            color: #7f8c8d;
        }
        
        .fan-on {
            color: #2ecc71;
        }
        
        .fan-off {
            color: #e74c3c;
        }
        
        .chart-container {
            height: 250px;
            margin-top: 10px;
        }
        
        .status-indicator {
            display: flex;
            justify-content: center;
            margin-top: 15px;
        }
        
        .indicator {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            margin: 0 5px;
        }
        
        .indicator.active {
            background-color: #2ecc71;
        }
        
        .indicator.inactive {
            background-color: #e74c3c;
        }
        
        .update-time {
            text-align: center;
            font-size: 12px;
            color: #95a5a6;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>ระบบติดตามอุณหภูมิ</h1>
        <p>การพัฒนาระบบควบคุมพัดลมระบายความร้อนอัตโนมัติสำหรับมอเตอร์โดยใช้ไมโครคอนโทรลเลอร์และเซนเซอร์วัดอุณหภูมิ</p>
    </div>
    
    <div class="card">
        <div class="card-title">อุณหภูมิปัจจุบัน</div>
        <div class="temp-display">
            <div class="temp-item">
                <div class="temp-value motor-temp" id="motorTemp">42</div>
                <div class="temp-label">Motor Temp. °C</div>
            </div>
            <div class="temp-item">
                <div class="temp-value ambient-temp" id="ambientTemp">26</div>
                <div class="temp-label">Ambient Temp. °C</div>
            </div>
        </div>
    </div>
    
    <div class="card">
        <div class="card-title">สถานะพัดลม</div>
        <div class="fan-status">
            <div class="fan-value fan-off" id="fanStatus">0%</div>
            <div class="fan-label">Fan Status</div>
        </div>
        <div class="status-indicator">
            <div class="indicator inactive" id="indicator1"></div>
            <div class="indicator inactive" id="indicator2"></div>
            <div class="indicator inactive" id="indicator3"></div>
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

    <script>
        // ข้อมูลตัวอย่างสำหรับกราฟ
        const initialData = {
            labels: ['10:00', '10:05', '10:10', '10:15', '10:20', '10:25', '10:30'],
            datasets: [
                {
                    label: 'Motor Temp (°C)',
                    data: [38, 40, 41, 42, 41, 40, 42],
                    borderColor: '#e74c3c',
                    backgroundColor: 'rgba(231, 76, 60, 0.1)',
                    tension: 0.4,
                    fill: true
                },
                {
                    label: 'Ambient Temp (°C)',
                    data: [24, 25, 25, 26, 25, 25, 26],
                    borderColor: '#3498db',
                    backgroundColor: 'rgba(52, 152, 219, 0.1)',
                    tension: 0.4,
                    fill: true
                }
            ]
        };

        // ตั้งค่ากราฟ
        const ctx = document.getElementById('tempChart').getContext('2d');
        const tempChart = new Chart(ctx, {
            type: 'line',
            data: initialData,
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: {
                    legend: {
                        position: 'top',
                    }
                },
                scales: {
                    y: {
                        beginAtZero: false,
                        suggestedMin: 20,
                        suggestedMax: 45
                    }
                }
            }
        });

        // ฟังก์ชันจำลองการอัพเดตข้อมูล
        function updateData() {
            // สุ่มข้อมูลใหม่เพื่อจำลองการอัพเดต
            const newMotorTemp = Math.floor(Math.random() * 5) + 40;
            const newAmbientTemp = Math.floor(Math.random() * 3) + 24;
            const newFanStatus = Math.random() > 0.8 ? 100 : 0;
            
            // อัพเดตข้อมูลบนหน้าเว็บ
            document.getElementById('motorTemp').textContent = newMotorTemp;
            document.getElementById('ambientTemp').textContent = newAmbientTemp;
            document.getElementById('fanStatus').textContent = newFanStatus + '%';
            
            // อัพเดตสีสถานะพัดลม
            const fanStatusElement = document.getElementById('fanStatus');
            if (newFanStatus === 0) {
                fanStatusElement.classList.remove('fan-on');
                fanStatusElement.classList.add('fan-off');
            } else {
                fanStatusElement.classList.remove('fan-off');
                fanStatusElement.classList.add('fan-on');
            }
            
            // อัพเดตอินดิเคเตอร์
            const indicators = document.querySelectorAll('.indicator');
            if (newFanStatus === 0) {
                indicators.forEach(indicator => {
                    indicator.classList.remove('active');
                    indicator.classList.add('inactive');
                });
            } else {
                indicators.forEach(indicator => {
                    indicator.classList.remove('inactive');
                    indicator.classList.add('active');
                });
            }
            
            // อัพเดตกราฟ
            const currentTime = new Date();
            const timeLabel = `${currentTime.getHours()}:${currentTime.getMinutes().toString().padStart(2, '0')}`;
            
            tempChart.data.labels.push(timeLabel);
            tempChart.data.datasets[0].data.push(newMotorTemp);
            tempChart.data.datasets[1].data.push(newAmbientTemp);
            
            // จำกัดจำนวนจุดข้อมูลในกราฟ
            if (tempChart.data.labels.length > 10) {
                tempChart.data.labels.shift();
                tempChart.data.datasets[0].data.shift();
                tempChart.data.datasets[1].data.shift();
            }
            
            tempChart.update();
            
            // อัพเดตเวลาล่าสุด
            document.getElementById('updateTime').textContent = 
                `อัพเดตล่าสุด: ${currentTime.toLocaleTimeString('th-TH')}`;
        }

        // จำลองการอัพเดตข้อมูลทุก 5 วินาที
        setInterval(updateData, 5000);
        
        // อัพเดตข้อมูลทันทีเมื่อโหลดหน้าเว็บ
        updateData();
    </script>
</body>
</html>
