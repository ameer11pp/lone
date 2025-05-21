<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>منشئ الألوان - RGBA</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            padding: 20px;
            background-color: #f5f5f5;
        }
        .color-picker {
            background: white;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
            max-width: 600px;
            margin: 0 auto;
        }
        .sliders {
            margin: 20px 0;
        }
        .slider-container {
            margin: 15px 0;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }
        .slider-container label {
            width: 60px;
            text-align: right;
        }
        input[type="range"] {
            flex-grow: 1;
            margin: 0 10px;
        }
        .number-input {
            width: 50px;
            text-align: center;
        }
        .color-display {
            width: 200px;
            height: 200px;
            margin: 20px auto;
            border-radius: 10px;
            border: 1px solid #ddd;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            transition: background-color 0.3s;
        }
        .rgba-value {
            font-size: 18px;
            font-weight: bold;
            margin: 10px 0;
            padding: 10px;
            background: #eee;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.2s;
        }
        .rgba-value:hover {
            background: #ddd;
        }
        .favorites {
            margin-top: 20px;
        }
        .favorite-colors {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 10px;
            margin-top: 10px;
        }
        .favorite-color {
            width: 40px;
            height: 40px;
            border-radius: 5px;
            cursor: pointer;
            border: 1px solid #ddd;
        }
        button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 10px;
        }
        button:hover {
            background-color: #45a049;
        }
        h1 {
            color: #333;
        }
        @media (max-width: 600px) {
            .slider-container {
                flex-direction: column;
                align-items: flex-start;
            }
            .slider-container label {
                margin-bottom: 5px;
            }
            input[type="range"] {
                width: 100%;
                margin: 5px 0;
            }
        }
    </style>
</head>
<body>
    <div class="color-picker">
        <h1>منشئ الألوان RGBA</h1>
        
        <div class="color-display" id="colorDisplay">لون العينة</div>
        
        <div class="rgba-value" id="rgbaValue">rgba(255, 255, 255, 1)</div>
        
        <div class="sliders">
            <div class="slider-container">
                <label for="red">الأحمر:</label>
                <input type="range" id="red" min="0" max="255" value="255">
                <input type="number" id="redInput" min="0" max="255" value="255" class="number-input">
            </div>
            
            <div class="slider-container">
                <label for="green">الأخضر:</label>
                <input type="range" id="green" min="0" max="255" value="255">
                <input type="number" id="greenInput" min="0" max="255" value="255" class="number-input">
            </div>
            
            <div class="slider-container">
                <label for="blue">الأزرق:</label>
                <input type="range" id="blue" min="0" max="255" value="255">
                <input type="number" id="blueInput" min="0" max="255" value="255" class="number-input">
            </div>
            
            <div class="slider-container">
                <label for="alpha">الشفافية:</label>
                <input type="range" id="alpha" min="0" max="1" step="0.01" value="1">
                <input type="number" id="alphaInput" min="0" max="1" step="0.01" value="1" class="number-input">
            </div>
        </div>
        
        <button id="saveFavorite">حفظ اللون المفضل</button>
        
        <div class="favorites">
            <h3>الألوان المفضلة</h3>
            <div class="favorite-colors" id="favoriteColors">
                <!-- سيتم إضافة الألوان المفضلة هنا -->
            </div>
        </div>
    </div>

    <script>
        // الحصول على عناصر DOM
        const redSlider = document.getElementById('red');
        const greenSlider = document.getElementById('green');
        const blueSlider = document.getElementById('blue');
        const alphaSlider = document.getElementById('alpha');
        
        const redInput = document.getElementById('redInput');
        const greenInput = document.getElementById('greenInput');
        const blueInput = document.getElementById('blueInput');
        const alphaInput = document.getElementById('alphaInput');
        
        const colorDisplay = document.getElementById('colorDisplay');
        const rgbaValue = document.getElementById('rgbaValue');
        const saveFavoriteBtn = document.getElementById('saveFavorite');
        const favoriteColorsContainer = document.getElementById('favoriteColors');
        
        // تحميل الألوان المفضلة من localStorage
        let favoriteColors = JSON.parse(localStorage.getItem('favoriteColors')) || [];
        
        // عرض الألوان المفضلة
        function displayFavoriteColors() {
            favoriteColorsContainer.innerHTML = '';
            favoriteColors.forEach((color, index) => {
                const colorBox = document.createElement('div');
                colorBox.className = 'favorite-color';
                colorBox.style.backgroundColor = color;
                colorBox.title = color;
                
                colorBox.addEventListener('click', () => {
                    applyFavoriteColor(color);
                });
                
                // إضافة زر لحذف اللون المفضل
                colorBox.addEventListener('contextmenu', (e) => {
                    e.preventDefault();
                    favoriteColors.splice(index, 1);
                    localStorage.setItem('favoriteColors', JSON.stringify(favoriteColors));
                    displayFavoriteColors();
                });
                
                favoriteColorsContainer.appendChild(colorBox);
            });
        }
        
        // تطبيق لون مفضل
        function applyFavoriteColor(color) {
            const rgbaMatch = color.match(/rgba?\((\d+),\s*(\d+),\s*(\d+)(?:,\s*([\d.]+))?\)/);
            if (rgbaMatch) {
                redSlider.value = rgbaMatch[1];
                redInput.value = rgbaMatch[1];
                greenSlider.value = rgbaMatch[2];
                greenInput.value = rgbaMatch[2];
                blueSlider.value = rgbaMatch[3];
                blueInput.value = rgbaMatch[3];
                
                if (rgbaMatch[4]) {
                    alphaSlider.value = rgbaMatch[4];
                    alphaInput.value = rgbaMatch[4];
                } else {
                    alphaSlider.value = 1;
                    alphaInput.value = 1;
                }
                
                updateColor();
            }
        }
        
        // تحديث اللون عند تغيير أي من المنزلقات أو حقول الإدخال
        function updateColor() {
            const red = redSlider.value;
            const green = greenSlider.value;
            const blue = blueSlider.value;
            const alpha = alphaSlider.value;
            
            // تحديث حقول الإدخال
            redInput.value = red;
            greenInput.value = green;
            blueInput.value = blue;
            alphaInput.value = alpha;
            
            // إنشاء لون RGBA
            const color = `rgba(${red}, ${green}, ${blue}, ${alpha})`;
            
            // تطبيق اللون على العرض
            colorDisplay.style.backgroundColor = color;
            
            // تحديث نص RGBA
            rgbaValue.textContent = color;
            
            // تغيير لون النص إذا كان اللون داكنًا
            const brightness = (red * 299 + green * 587 + blue * 114) / 1000;
            colorDisplay.style.color = brightness > 128 ? 'black' : 'white';
        }
        
        // إضافة مستمعات الأحداث للمنزلقات
        redSlider.addEventListener('input', () => {
            redInput.value = redSlider.value;
            updateColor();
        });
        
        greenSlider.addEventListener('input', () => {
            greenInput.value = greenSlider.value;
            updateColor();
        });
        
        blueSlider.addEventListener('input', () => {
            blueInput.value = blueSlider.value;
            updateColor();
        });
        
        alphaSlider.addEventListener('input', () => {
            alphaInput.value = alphaSlider.value;
            updateColor();
        });
        
        // إضافة مستمعات الأحداث لحقول الإدخال
        redInput.addEventListener('input', () => {
            let value = Math.min(255, Math.max(0, redInput.value));
            redSlider.value = value;
            redInput.value = value;
            updateColor();
        });
        
        greenInput.addEventListener('input', () => {
            let value = Math.min(255, Math.max(0, greenInput.value));
            greenSlider.value = value;
            greenInput.value = value;
            updateColor();
        });
        
        blueInput.addEventListener('input', () => {
            let value = Math.min(255, Math.max(0, blueInput.value));
            blueSlider.value = value;
            blueInput.value = value;
            updateColor();
        });
        
        alphaInput.addEventListener('input', () => {
            let value = Math.min(1, Math.max(0, alphaInput.value));
            alphaSlider.value = value;
            alphaInput.value = value;
            updateColor();
        });
        
        // نسخ قيمة RGBA إلى الحافظة
        rgbaValue.addEventListener('click', function() {
            navigator.clipboard.writeText(rgbaValue.textContent)
                .then(() => {
                    const originalText = rgbaValue.textContent;
                    rgbaValue.textContent = 'تم النسخ!';
                    setTimeout(() => {
                        rgbaValue.textContent = originalText;
                    }, 2000);
                })
                .catch(err => {
                    console.error('Failed to copy: ', err);
                    alert('تعذر النسخ، يرجى المحاولة مرة أخرى');
                });
        });
        
        // حفظ اللون المفضل
        saveFavoriteBtn.addEventListener('click', function() {
            const currentColor = rgbaValue.textContent;
            
            if (!favoriteColors.includes(currentColor)) {
                favoriteColors.push(currentColor);
                localStorage.setItem('favoriteColors', JSON.stringify(favoriteColors));
                displayFavoriteColors();
                
                // إشعار مؤقت
                const originalText = saveFavoriteBtn.textContent;
                saveFavoriteBtn.textContent = 'تم الحفظ!';
                setTimeout(() => {
                    saveFavoriteBtn.textContent = originalText;
                }, 2000);
            }
        });
        
        // تهيئة اللون الأولي وعرض الألوان المفضلة
        updateColor();
        displayFavoriteColors();
    </script>
</body>
</html>
