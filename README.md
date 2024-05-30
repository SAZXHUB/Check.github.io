<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>เช็คสถานะเว็บไซต์</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background-color: #333;
            color: #fff;
        }
        .container {
            text-align: center;
        }
        input[type="text"] {
            padding: 10px;
            width: 300px;
            margin-bottom: 10px;
            border: none;
            border-radius: 5px;
        }
        button {
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .result {
            margin-top: 20px;
            font-weight: bold;
        }
        .visit-count {
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>เช็คสถานะเว็บไซต์</h1>
        <input type="text" id="urlInput" placeholder="กรอกลิ้งเว็บไซต์">
        <button onclick="checkWebsiteStatus()">เช็คสถานะ</button>
        <div id="result" class="result"></div>
        <div id="visitCount" class="visit-count"></div>
    </div>

    <script>
        function updateVisitCount() {
            let visitCount = localStorage.getItem('visitCount');
            if (!visitCount) {
                visitCount = 0;
            }
            visitCount = parseInt(visitCount) + 1;
            localStorage.setItem('visitCount', visitCount);
            document.getElementById('visitCount').textContent = `จำนวนการเข้าชม: ${visitCount}`;
        }

        async function checkWebsiteStatus() {
            const urlInput = document.getElementById('urlInput').value;
            const resultDiv = document.getElementById('result');
            let url = urlInput;
            
            if (!url.startsWith("http://") && !url.startsWith("https://")) {
                url = "https://" + url;
            }

            const apiUrl = `https://api.allorigins.win/get?url=${encodeURIComponent(url)}`;

            try {
                const response = await fetch(apiUrl);
                if (response.ok) {
                    const data = await response.json();
                    if (data.status.http_code === 200) {
                        resultDiv.textContent = "เว็บไซต์ปกติ";
                        resultDiv.style.color = "green";
                    } else {
                        resultDiv.textContent = `เว็บไซต์มีปัญหา โค้ดสถานะ: ${data.status.http_code}`;
                        resultDiv.style.color = "red";
                    }
                } else {
                    resultDiv.textContent = `เว็บไซต์มีปัญหา โค้ดสถานะ: ${response.status}`;
                    resultDiv.style.color = "red";
                }
            } catch (error) {
                resultDiv.textContent = "ไม่สามารถเชื่อมต่อ เว็บไซต์ได้";
                resultDiv.style.color = "red";
            }
        }

        // Update visit count on page load
        updateVisitCount();
    </script>
</body>
</html>
