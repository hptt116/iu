# iu
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Biển Tình Yêu 3D</title>
    <script src="https://cdn.jsdelivr.net/npm/qrcode@1.5.1/build/qrcode.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden;
            background: #000;
            font-family: 'Arial', sans-serif;
        }
        
        #canvas-container {
            position: absolute;
            width: 100%;
            height: 100%;
            z-index: 1;
        }
        
        .download-qr {
            position: absolute;
            bottom: 20px;
            right: 20px;
            background-color: rgba(255, 255, 255, 0.9);
            padding: 15px;
            border-radius: 15px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
            z-index: 100;
            text-align: center;
            backdrop-filter: blur(5px);
            cursor: pointer;
        }
        
        .download-qr:hover {
            background-color: rgba(255, 255, 255, 0.95);
        }
        
        .qr-code {
            width: 100px;
            height: 100px;
            margin: 0 auto;
        }
        
        .download-text {
            color: #e91e63;
            margin-top: 10px;
            font-size: 14px;
            font-weight: bold;
        }
        
        .love-word {
            position: absolute;
            pointer-events: none;
            font-weight: bold;
            animation: float 8s linear forwards;
            opacity: 0;
            text-shadow: 0 0 10px rgba(255, 255, 255, 0.7);
            z-index: 2;
        }
        
        @keyframes float {
            0% {
                transform: translate(0, 0) rotate(0deg) scale(0.8);
                opacity: 0;
            }
            10% {
                opacity: 1;
            }
            90% {
                opacity: 1;
            }
            100% {
                transform: translate(var(--random-x), var(--random-y)) rotate(var(--random-rotate)) scale(1.2);
                opacity: 0;
            }
        }
    </style>
</head>
<body>
    <div id="canvas-container"></div>
    
    <div class="download-qr" id="download-qr">
        <div id="qr-code" class="qr-code"></div>
        <div class="download-text">Lưu QR Code</div>
    </div>
    
    <script>
        // ========== CẤU HÌNH 3D ==========
        let scene, camera, renderer, hearts = [];
        const heartColors = [0xff3366, 0xff6699, 0xff0066, 0xcc0066, 0xff0033];
        
        function init3D() {
            // Tạo scene
            scene = new THREE.Scene();
            
            // Tạo camera
            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.z = 30;
            
            // Tạo renderer
            renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            document.getElementById('canvas-container').appendChild(renderer.domElement);
            
            // Thêm ánh sáng
            const ambientLight = new THREE.AmbientLight(0x404040);
            scene.add(ambientLight);
            
            const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
            directionalLight.position.set(1, 1, 1);
            scene.add(directionalLight);
            
            // Tạo trái tim 3D ban đầu
            for (let i = 0; i < 50; i++) {
                create3DHeart();
            }
            
            // Xử lý resize
            window.addEventListener('resize', () => {
                camera.aspect = window.innerWidth / window.innerHeight;
                camera.updateProjectionMatrix();
                renderer.setSize(window.innerWidth, window.innerHeight);
            });
            
            // Animation loop
            animate();
        }
        
        function create3DHeart() {
            const shape = new THREE.Shape();
            shape.moveTo(0, 0);
            shape.bezierCurveTo(0, 0, 0.5, 0.8, 0, 1.5);
            shape.bezierCurveTo(-0.5, 0.8, 0, 0, 0, 0);
            
            const extrudeSettings = {
                steps: 1,
                depth: 0.5,
                bevelEnabled: true,
                bevelThickness: 0.1,
                bevelSize: 0.1,
                bevelSegments: 3
            };
            
            const geometry = new THREE.ExtrudeGeometry(shape, extrudeSettings);
            const material = new THREE.MeshPhongMaterial({
                color: heartColors[Math.floor(Math.random() * heartColors.length)],
                shininess: 100,
                specular: 0xffffff
            });
            
            const heart = new THREE.Mesh(geometry, material);
            
            // Random position
            heart.position.x = (Math.random() - 0.5) * 50;
            heart.position.y = (Math.random() - 0.5) * 50;
            heart.position.z = (Math.random() - 0.5) * 50;
            
            // Random rotation
            heart.rotation.x = Math.random() * Math.PI;
            heart.rotation.y = Math.random() * Math.PI;
            
            // Random scale
            const scale = Math.random() * 2 + 1;
            heart.scale.set(scale, scale, scale);
            
            // Random speed
            heart.userData = {
                speedX: (Math.random() - 0.5) * 0.02,
                speedY: (Math.random() - 0.5) * 0.02,
                speedZ: (Math.random() - 0.5) * 0.02,
                rotateX: (Math.random() - 0.5) * 0.02,
                rotateY: (Math.random() - 0.5) * 0.02
            };
            
            scene.add(heart);
            hearts.push(heart);
        }
        
        function animate() {
            requestAnimationFrame(animate);
            
            // Di chuyển các trái tim 3D
            hearts.forEach(heart => {
                heart.position.x += heart.userData.speedX;
                heart.position.y += heart.userData.speedY;
                heart.position.z += heart.userData.speedZ;
                
                heart.rotation.x += heart.userData.rotateX;
                heart.rotation.y += heart.userData.rotateY;
                
                // Giới hạn không gian di chuyển
                if (Math.abs(heart.position.x) > 30) heart.userData.speedX *= -1;
                if (Math.abs(heart.position.y) > 30) heart.userData.speedY *= -1;
                if (Math.abs(heart.position.z) > 30) heart.userData.speedZ *= -1;
            });
            
            renderer.render(scene, camera);
        }
        
        // ========== TỪ NGỮ YÊU THƯƠNG ==========
        const loveWords = [
            "Yêu em nhiuuuuu", "Nhớ em qaa di thoiiiii", "Thương em", "Mãi bên nhau", "Đời anh có em",
            "Hạnh phúc", "Ngọt ngào", "Lãng mạn", "Đắm say", "Bên nhau trọn đời",
            "Em là tất cả đối với anhhh ", "Anh yêu em", "Trái tim anh", "Thuộc về em", "Mãi không xa",
            "Tình yêu đẹp", "Nguyện yêu em suốt cuộc đời ", "Trọn đời bên em", "Mãi mãi", "Yêu em nhất",
            "Em là số 1", "Tình đầu", "Tình cuối cua anhhhh", "Duy nhất", "Yêu em hơn cả bản thân",
            "Nhớ em nhiều", "Thương em lắm", "Yêu từ cái nhìn đầu", "Triệu like",
            "Like mạnh", "Yêu không hối hận", "Yêu điên cuồng", "Yêu say đắm", "Yêu chân thành",
            "Yêu vô điều kiện", "Yêu hết mình", "Yêu bằng cả trái tim", "Yêu bất chấp", "Yêu không lối thoát"
        ];
        
        function createLoveWord() {
            const word = document.createElement('div');
            word.className = 'love-word';
            word.textContent = loveWords[Math.floor(Math.random() * loveWords.length)];
            
            // Random màu (hồng/đỏ)
            const hue = 330 + Math.random() * 30;
            word.style.color = `hsl(${hue}, 100%, 70%)`;
            
            // Random kích thước
            const size = Math.random() * 20 + 15;
            word.style.fontSize = `${size}px`;
            
            // Vị trí ban đầu
            const x = Math.random() * window.innerWidth;
            const y = window.innerHeight + 50;
            word.style.left = `${x}px`;
            word.style.top = `${y}px`;
            
            // Random hướng bay
            const randomX = (Math.random() - 0.5) * 300;
            const randomY = -Math.random() * window.innerHeight - 200;
            const randomRotate = (Math.random() - 0.5) * 60;
            word.style.setProperty('--random-x', `${randomX}px`);
            word.style.setProperty('--random-y', `${randomY}px`);
            word.style.setProperty('--random-rotate', `${randomRotate}deg`);
            
            // Thời gian animation
            const duration = Math.random() * 5 + 5;
            word.style.animationDuration = `${duration}s`;
            
            document.body.appendChild(word);
            
            // Xóa sau khi animation kết thúc
            setTimeout(() => {
                word.remove();
            }, duration * 1000);
        }
        
        // ========== TẠO QR CODE DẪN VỀ TRANG NÀY ==========
        function createQRCode() {
            // Lấy URL hiện tại của trang
            const currentUrl = window.location.href;
            
            QRCode.toCanvas(document.getElementById('qr-code'), currentUrl, {
                width: 100,
                color: {
                    dark: '#e91e63',
                    light: '#ffffff00'
                },
                errorCorrectionLevel: 'H',
                margin: 1,
                shape: 'heart'
            }, function(error) {
                if (error) console.error(error);
            });
        }
        
        // ========== TẢI QR CODE ==========
        function setupDownloadQR() {
            document.getElementById('download-qr').addEventListener('click', function() {
                const canvas = document.querySelector('#qr-code canvas');
                const link = document.createElement('a');
                link.download = 'tinh-yeu-3d.png';
                link.href = canvas.toDataURL('image/png');
                link.click();
            });
        }
        
        // ========== KHỞI TẠO ==========
        window.onload = function() {
            init3D();
            createQRCode();
            setupDownloadQR();
            
            // Tạo từ ngữ yêu thương liên tục
            setInterval(createLoveWord, 200);
            
            // Tạo thêm trái tim 3D định kỳ
            setInterval(create3DHeart, 1000);
            
            // Click để tạo từ ngữ
            document.addEventListener('click', (e) => {
                for (let i = 0; i < 5; i++) {
                    setTimeout(() => {
                        createLoveWord();
                        create3DHeart();
                    }, i * 200);
                }
            });
            
            // Tạo 100 từ ngữ ban đầu
            for (let i = 0; i < 100; i++) {
                setTimeout(createLoveWord, i * 50);
            }
        };
    </script>
</body>
</html>
