
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" type="text/css" href="styles.css">
    <title>Генератор QR-кода для видео</title>
    <script src="script.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcode-generator/1.4.4/qrcode.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 0;
        }
        input[type="text"], button {
            padding: 10px;
            margin: 5px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }
        input[type="text"] {
            width: 300px;
        }
        button {
            background-color: #007bff;
            color: #fff;
        }
        button:hover {
            background-color: #0056b3;
        }
        label {
            margin-left: 5px;
        }
        #qrcode {
            margin-top: 20px;
        }
        .modal {
            display: none;
            position: fixed;
            z-index: 1;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0,0,0,0.8);
        }
        .modal-content {
            margin: 10% auto;
            width: 80%;
            max-width: 600px;
            background-color: #fff;
            padding: 20px;
            position: relative;
            border-radius: 10px;
        }
        .close {
            position: absolute;
            top: 10px;
            right: 10px;
            font-size: 20px;
            cursor: pointer;
        }
        footer {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            background-color: #333;
            color: #fff;
            padding: 10px;
            text-align: center;
        }
        code {
            font-family: 'Courier New', Courier, monospace;
            font-size: 14px;
            background-color: #f4f4f4;
            padding: 2px 5px;
            border-radius: 4px;
        }
        nav ul li a {
            color: #333;
            text-decoration: none;
            padding: 5px 10px;
            border-radius: 5px;
            transition: background-color 0.3s ease;
        }

        nav ul li a:hover {
            background-color: #ccc;
        }

        section {
            margin: 20px 0;
        }

        h2 {
            color: #007bff;
        }

        p {
            line-height: 1.6;
        }

        footer p {
            margin: 0;
        }

        footer a {
            color: #fff;
            text-decoration: none;
        }

        footer a:hover {
            text-decoration: underline;
        }
    </style>
</head>
<body>
    <input id="videoLink" type="text" placeholder="Введите ссылку на видео">
    <button onclick="generateVideoQR()">Создать QR-код</button>
    <button onclick="pasteFromClipboard()">Вставить</button>
    <button onclick="clearQRCode()">Очистить</button>
    <button onclick="copyToClipboard()">Копировать в буфер</button>
    <button onclick="pasteVideoLinkFromClipboard()">Вставить ссылку</button>
    <label for="qrSize">Размер QR-кода:</label>
    <input id="qrSize" type="number" min="100" max="500" value="200">
    
    <div id="qrcode"></div>

    <div id="videoModal" class="modal">
        <div class="modal-content">
            <span class="close" onclick="closeVideoModal()">&times;</span>
            <iframe id="videoFrame" width="560" height="315" frameborder="0" allowfullscreen></iframe>
        </div>
    </div>

    <script>
            function generateVideoQR() {
    var videoLink = document.getElementById('videoLink').value;
    var qrSize = document.getElementById('qrSize').value;
    var qr = qrcode(0, 'M');
    qr.addData(videoLink);
    qr.make();
    var qrCanvas = document.createElement('canvas');
    qrCanvas.width = qrSize;
    qrCanvas.height = qrSize;
    var qrContext = qrCanvas.getContext('2d');
    var moduleCount = qr.getModuleCount();
    var moduleSize = qrSize / moduleCount;
    for (var row = 0; row < moduleCount; row++) {
        for (var col = 0; col < moduleCount; col++) {
            if (qr.isDark(row, col)) {
                qrContext.fillStyle = "#000"; // Установка цвета для заменяющего пикселя
            } else {
                qrContext.fillStyle = "#fff"; // Установка цвета для фона
            }
            qrContext.fillRect(col * moduleSize, row * moduleSize, moduleSize, moduleSize);
        }
    }
    var qrImage = document.createElement('img');
    qrImage.src = qrCanvas.toDataURL('image/png');
    var qrContainer = document.getElementById('qrcode');
    qrContainer.innerHTML = '';
    qrContainer.appendChild(qrImage);
}

             }
            var qrImage = document.createElement('img');
            qrImage.src = qrCanvas.toDataURL('image/png');
            var qrContainer = document.getElementById('qrcode');
            qrContainer.innerHTML = '';
            qrContainer.appendChild(qrImage);
        }

        function clearQRCode() {
            document.getElementById('videoLink').value = '';
            document.getElementById('qrcode').innerHTML = '';
        }

        function copyToClipboard() {
            var qrContainer = document.getElementById('qrcode');
            var qrImage = qrContainer.querySelector('img');
            
            var tempInput = document.createElement('input');
            tempInput.setAttribute('value', qrImage.src);
            document.body.appendChild(tempInput);
            tempInput.select();
            document.execCommand('copy');
            document.body.removeChild(tempInput);

            alert('QR-код скопирован в буфер обмена!');
        }

        function pasteFromClipboard() {
            navigator.clipboard.readText().then(function(text) {
                document.getElementById('videoLink').value = text;
                openVideoModal(text);
            });
        }

        function pasteVideoLinkFromClipboard() {
            navigator.clipboard.readText().then(function(text) {
                document.getElementById('videoLink').value = text;
                generateVideoQR();
            });
        }

        function openVideoModal(videoLink) {
            var modal = document.getElementById('videoModal');
            var videoFrame = document.getElementById('videoFrame');
            videoFrame.src = videoLink;
            modal.style.display = 'block';
        }

        function closeVideoModal() {
            var modal = document.getElementById('videoModal');
            var videoFrame = document.getElementById('videoFrame');
            videoFrame.src = '';
            modal.style.display = 'none';
        }

        window.onclick = function(event) {
            var modal = document.getElementById('videoModal');
            if (event.target == modal) {
                closeVideoModal();
            }
        };
    </script>

    <footer>
        <p>&copy; 2024 Разработчик Dylan933. Все права защищены. | </p>
    </footer>
