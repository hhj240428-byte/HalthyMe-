<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8">
  <title>حاسبة الوزن الذكية</title>
  <style>
    body {
      font-family: 'Cairo', sans-serif;
      background: linear-gradient(135deg, #6a11cb, #2575fc);
      color: #fff;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
      transition: 0.5s;
    }
    body.light {
      background: linear-gradient(135deg, #f5f7fa, #c3cfe2);
      color: #000;
    }
    .card {
      background: rgba(255, 255, 255, 0.1);
      padding: 25px;
      border-radius: 20px;
      box-shadow: 0 8px 25px rgba(0,0,0,0.2);
      width: 420px;
      text-align: center;
      backdrop-filter: blur(10px);
      transition: 0.5s;
      position: relative;
    }
    h1 { margin-bottom: 20px; font-size: 22px; }
    input, select {
      width: 90%;
      padding: 10px;
      margin: 8px 0;
      border: none;
      border-radius: 10px;
      text-align: center;
      font-size: 15px;
    }
    button {
      background: #ff9800;
      color: #fff;
      padding: 10px 18px;
      border: none;
      border-radius: 12px;
      cursor: pointer;
      font-size: 14px;
      margin: 5px;
      transition: 0.3s;
    }
    button:hover { background: #e68900; }
    .toggle-mode {
      position: absolute;
      top: 15px;
      right: 15px;
      background: #ff9800;
      color: #fff;
      border: none;
      border-radius: 20px;
      padding: 6px 12px;
      cursor: pointer;
      font-size: 13px;
      font-weight: bold;
      box-shadow: 0 4px 12px rgba(0,0,0,0.3);
      transition: 0.3s;
    }
    .toggle-mode:hover { background: #e68900; }
    body.light .toggle-mode {
      background: #333;
      color: #fff;
    }
    .result, .advice, .ideal, .calories, .extra, .water, .tip, .meals {
      margin-top: 10px;
      font-size: 15px;
      font-weight: bold;
    }
    .bar-container {
      background: #ddd;
      border-radius: 20px;
      overflow: hidden;
      margin-top: 12px;
      height: 20px;
    }
    .bar {
      height: 100%;
      text-align: right;
      padding-right: 5px;
      line-height: 20px;
      color: #fff;
    }
    .bar.green { background: #4CAF50; }
    .bar.yellow { background: #FFC107; }
    .bar.red { background: #F44336; }
    .bar.blue { background: #2196F3; }
    .history {
      margin-top: 15px;
      text-align: right;
      font-size: 13px;
      background: rgba(255,255,255,0.2);
      padding: 10px;
      border-radius: 10px;
      max-height: 120px;
      overflow-y: auto;
    }
  </style>
</head>
<body>
  <div class="card">
    <button class="toggle-mode" onclick="toggleMode()">🌙</button>
    <h1>حاسبة مؤشر كتلة الجسم</h1>

    <select id="gender">
      <option value="male">👦 ولد</option>
      <option value="female">👧 بنت</option>
    </select>

    <input type="number" id="age" placeholder="العمر (بالسنوات)">
    <input type="number" id="height" placeholder="الطول (سم)">
    <input type="number" id="weight" placeholder="الوزن (كغ)">

    <select id="goal">
      <option value="maintain">🔹 تثبيت الوزن</option>
      <option value="lose">⬇️ خسارة الوزن</option>
      <option value="gain">⬆️ زيادة الوزن</option>
    </select>

    <select id="activity">
      <option value="1.2">💺 خامل (بدون حركة)</option>
      <option value="1.4">🚶 خفيف (مشي أحياناً)</option>
      <option value="1.6">🏃 متوسط (3-4 مرات/أسبوع)</option>
      <option value="1.8">💪 عالي (تمارين يومية)</option>
    </select>

    <button onclick="calculateBMI()">احسب</button>
    <button onclick="resetFields()">إعادة المحاولة</button>
    
    <div class="result" id="result"></div>
    <div class="bar-container"><div class="bar" id="progressBar"></div></div>
    <div class="advice" id="advice"></div>
    <div class="ideal" id="ideal"></div>
    <div class="calories" id="calories"></div>
    <div class="water" id="water"></div>
    <div class="extra" id="extra"></div>
    <div class="tip" id="tip"></div>
    <div class="meals" id="meals"></div>

    <div class="history" id="history">📜 السجل:</div>
  </div>

  <script>
    function calculateBMI() {
      let gender = document.getElementById("gender").value;
      let age = document.getElementById("age").value;
      let height = document.getElementById("height").value / 100;
      let weight = document.getElementById("weight").value;
      let goal = document.getElementById("goal").value;
      let activity = parseFloat(document.getElementById("activity").value);

      let result = document.getElementById("result");
      let advice = document.getElementById("advice");
      let ideal = document.getElementById("ideal");
      let calories = document.getElementById("calories");
      let extra = document.getElementById("extra");
      let water = document.getElementById("water");
      let tipBox = document.getElementById("tip");
      let bar = document.getElementById("progressBar");
      let history = document.getElementById("history");
      let mealsBox = document.getElementById("meals");

      if (height <= 0 || weight <= 0 || age <= 0) {
        result.textContent = "⚠️ الرجاء إدخال بيانات صحيحة.";
        return;
      }

      let bmi = (weight / (height * height)).toFixed(1);
      let status = "";
      let tip = "";
      let barColor = "blue";

      if (bmi < 18.5) { status = "نحيف"; tip = "زد وجباتك الصحية."; barColor = "blue"; }
      else if (bmi < 24.9) { status = "طبيعي"; tip = "حافظ على نمطك الصحي."; barColor = "green"; }
      else if (bmi < 29.9) { status = "وزن زائد"; tip = "قلل السكريات وأكثر من الرياضة."; barColor = "yellow"; }
      else { status = "سمنة"; tip = "ابدأ برنامج صحي ورياضي جاد."; barColor = "red"; }

      // الوزن المثالي
      let minIdeal = (18.5 * height * height).toFixed(1);
      let maxIdeal = (24.9 * height * height).toFixed(1);

      // BMR
      let bmr = (gender === "male") 
        ? (10 * weight) + (6.25 * (height * 100)) - (5 * age) + 5
        : (10 * weight) + (6.25 * (height * 100)) - (5 * age) - 161;

      let dailyCalories = Math.round(bmr * activity);

      // تعديل حسب الهدف
      if (goal === "lose") dailyCalories -= 500;
      if (goal === "gain") dailyCalories += 400;

      // شريط التقدم
      let progressPercent = Math.min(100, Math.max(0, ((bmi / 40) * 100)));
      bar.style.width = progressPercent + "%";
      bar.className = "bar " + barColor;

      // مؤشر شرب الماء
      let waterNeed = (weight * 0.035).toFixed(2);

      // نصائح عشوائية
      let tips = [
        "💧 اشرب كوب ماء قبل كل وجبة.",
        "🚶 مارس 30 دقيقة مشي يومياً.",
        "🥦 اجعل الخضار نصف وجبتك.",
        "😴 نم 7-8 ساعات يومياً.",
        "🍬 قلل السكريات المصنعة."
      ];
      let randomTip = tips[Math.floor(Math.random() * tips.length)];

      // اقتراح وجبات
      let meals = {
        "نحيف": {
          "إفطار": "🥐 بيضتين + خبز أسمر + كوب حليب.",
          "غداء": "🍲 أرز + دجاج مشوي + خضار.",
          "عشاء": "🥗 سلطة + سمك + بطاطا.",
          "وجبة خفيفة": "🍌 موز + حفنة مكسرات."
        },
        "طبيعي": {
          "إفطار": "🥣 شوفان + موز + حليب.",
          "غداء": "🥩 لحم + خضار + أرز قليل.",
          "عشاء": "🥪 توست أسمر + جبن + خضار.",
          "وجبة خفيفة": "🍎 تفاحة أو برتقال."
        },
        "وزن زائد": {
          "إفطار": "🍳 بيضة + توست أسمر + خيار.",
          "غداء": "🥗 سلطة + صدر دجاج + شوربة.",
          "عشاء": "🍲 خضار مطهوة بالبخار + تونة.",
          "وجبة خفيفة": "🥒 خيار + جزر."
        },
        "سمنة": {
          "إفطار": "🥗 سلطة خفيفة + شاي بدون سكر.",
          "غداء": "🥬 خضار + بروتين (سمك/دجاج) بدون زيت.",
          "عشاء": "🍲 شوربة خضار + سلطة.",
          "وجبة خفيفة": "🍊 برتقال أو خيار."
        }
      };

      let suggestedMeals = meals[status];
      let mealsText = `🍽️ اقتراح يوم غذائي:<br>
      🥐 إفطار: ${suggestedMeals["إفطار"]}<br>
      🍲 غداء: ${suggestedMeals["غداء"]}<br>
      🥗 عشاء: ${suggestedMeals["عشاء"]}<br>
      🍎 وجبة خفيفة: ${suggestedMeals["وجبة خفيفة"]}`;

      // مخرجات
      result.textContent = `📊 مؤشر كتلة جسمك: ${bmi} (${status})`;
      advice.textContent = tip;
      ideal.textContent = `⚖️ وزنك المثالي: ${minIdeal} - ${maxIdeal} كغ`;
      calories.textContent = `🔥 سعراتك اليومية: ${dailyCalories} سعرة`;
      water.textContent = `💧 احتياجك من الماء: ${waterNeed} لتر يومياً`;
      extra.textContent = `🎯 هدفك: ${goal === "lose" ? "خسارة" : goal === "gain" ? "زيادة" : "تثبيت"} الوزن`;
      tipBox.textContent = `💡 نصيحة اليوم: ${randomTip}`;
      mealsBox.innerHTML = mealsText;

      // حفظ في السجل
      let today = new Date().toLocaleString();
      let record = `📅 ${today} → BMI: ${bmi} (${status}) | سعرات: ${dailyCalories}`;
      let oldHistory = JSON.parse(localStorage.getItem("bmiHistory")) || [];
      oldHistory.unshift(record);
      localStorage.setItem("bmiHistory", JSON.stringify(oldHistory));

      displayHistory();
    }

    function resetFields() {
      ["age","height","weight"].forEach(id => document.getElementById(id).value = "");
      ["result","advice","ideal","calories","extra","water","tip","meals"].forEach(id => document.getElementById(id).textContent = "");
      document.getElementById("progressBar").style.width = "0%";
    }

    function displayHistory() {
      let history = JSON.parse(localStorage.getItem("bmiHistory")) || [];
      let historyBox = document.getElementById("history");
      historyBox.innerHTML = "📜 السجل:<br>" + history.slice(0,5).join("<br>");
    }

    function toggleMode() {
      document.body.classList.toggle("light");
      let btn = document.querySelector(".toggle-mode");
      btn.textContent = document.body.classList.contains("light") ? "☀️" : "🌙";
    }

    window.onload = displayHistory;
  </script>
</body>
</html>
