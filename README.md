# My-App
محاسبه قیمت سیمان نی ریز


<html lang="fa">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>محاسبه‌گر سیمان</title>
  <link href="https://cdn.fontcdn.ir/Font/Persian/Vazirmatn/Vazirmatn.css" rel="stylesheet">

<!DOCTYPE html>
<html lang="fa">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My App</title>
  <style>
    body {
      font-family: sans-serif;
      margin: 0;
      padding: 20px;
      text-align: center;
    }
    .box {
      max-width: 600px;
      margin: auto;
      padding: 20px;
      border: 1px solid #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <div class="box">
    <h1>اپ من</h1>
    <p>سلام! این یه اپ تستی روی GitHub Pages هست.</p>
  </div>
</body>
</html>
  
  <style>
    body {
      font-family: 'Vazirmatn', sans-serif;
      direction: rtl;
      background-color: #001f3f;
      color: white;
      margin: 0;
      padding: 0;
    }

    .container {
      max-width: 500px;
      margin: 0 auto;
      padding: 20px;
    }

    h1, h2 {
      text-align: center;
      margin-bottom: 5px;
    }

    label {
      margin-top: 15px;
      display: block;
    }

    input, select, button {
      width: 100%;
      padding: 10px;
      margin-top: 5px;
      font-size: 1em;
      border-radius: 6px;
      border: 1px solid #ccc;
      color: #000;
      box-sizing: border-box;
    }

    input {
      background: #fff;
    }

    button {
      background-color: #0074D9;
      color: white;
      border: none;
      margin-top: 20px;
      cursor: pointer;
      font-weight: bold;
    }

    .result {
      margin-top: 25px;
      background: #003366;
      padding: 15px;
      border-radius: 10px;
    }

    .result p {
      margin: 8px 0;
      font-size: 0.95em;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>فروش عمده سیمان خاکستری نی‌ریز</h1>
    <h2>پاکنیت ۰۹۱۷۱۳۳۶۵۶۱</h2>

    <label>قیمت هر تن سیمان (تومان):</label>
    <input type="text" id="pricePerTon" placeholder=" قیمت سیمان اینجا وارد شود">

    <label>تناژ:</label>
    <select id="tonnage">
      <option value="10">10</option>
      <option value="17">17</option>
      <option value="27">27</option>
    </select>

    <label>نام شهر:</label>
    <input type="text" id="citySearch" placeholder="جستجوی شهر">
    <select id="citySelect" size="4"></select>

    <button onclick="calculate()">محاسبه</button>

    <div class="result" id="resultBox" style="display:none;">
      <p>تناژ: <span id="resTonnage">--</span> تن</p>
      <p>مبلغ کرایه تریلی (هر تن): <span id="resFreight">--</span> تومان</p>
      <p>مبلغ کرایه ده‌چرخ (هر تن): <span id="resFreightTax">--</span> تومان</p>
      <p>مبلغ خرید سیمان: <span id="resCement">--</span> تومان</p>
      <p id="resFinalFreightRow">مبلغ کرایه نهایی: <span id="resFinalFreight">--</span> تومان</p>
      <p><strong>مبلغ کل پرداختی: <span id="resTotal">--</span> تومان</strong></p>
      <p><strong>قیمت پاکت تحویل کار: <span id="resBagPrice">--</span> تومان</strong></p>
    </div>
  </div>

  <script>
    const cities = [
      { name: "سیرجان", freight: 330000 },
      { name: "اصفهان", freight: 1200000 },
      { name: "شیراز", freight: 1400000 },
      { name: "تبریز", freight: 1600000 },
      { name: "مشهد", freight: 1350000 }
    ];

    const citySelect = document.getElementById("citySelect");
    const citySearch = document.getElementById("citySearch");
    const priceInput = document.getElementById("pricePerTon");
    const tonnageSelect = document.getElementById("tonnage");

    const resTonnage = document.getElementById("resTonnage");
    const resFreight = document.getElementById("resFreight");
    const resFreightTax = document.getElementById("resFreightTax");
    const resCement = document.getElementById("resCement");
    const resFinalFreight = document.getElementById("resFinalFreight");
    const resFinalFreightRow = document.getElementById("resFinalFreightRow");
    const resTotal = document.getElementById("resTotal");
    const resBagPrice = document.getElementById("resBagPrice");
    const resultBox = document.getElementById("resultBox");

    let selectedCity = null;

    function toEnglishDigits(str) {
      return str.replace(/[۰-۹]/g, d => "۰۱۲۳۴۵۶۷۸۹".indexOf(d));
    }

    citySearch.addEventListener("input", () => {
      const filter = citySearch.value.trim();
      citySelect.innerHTML = "";
      cities.filter(c => c.name.includes(filter)).forEach(c => {
        const option = document.createElement("option");
        option.textContent = c.name;
        citySelect.appendChild(option);
      });
    });

    citySelect.addEventListener("change", () => {
      selectedCity = cities.find(c => c.name === citySelect.value);
    });

    priceInput.addEventListener("input", function () {
      const raw = toEnglishDigits(this.value).replace(/\D/g, "");
      this.value = parseInt(raw || 0).toLocaleString();
    });

    function calculate() {
      if (!selectedCity) return;

      const price = parseInt(toEnglishDigits(priceInput.value).replace(/,/g, '')) || 0;
      const tonnage = parseInt(tonnageSelect.value);
      const freight = selectedCity.freight;
      const freightTax = Math.round(freight * 1.09);

      const cementTotal = price * tonnage;
      let totalFreight, totalAmount;

      if (tonnage === 27) {
        totalFreight = freight * tonnage;
        totalAmount = cementTotal + totalFreight;
        resFinalFreightRow.style.display = "block";
        resFinalFreight.textContent = totalFreight.toLocaleString();
        resFreightTax.parentElement.style.display = "none";
      } else {
        totalFreight = freightTax * tonnage;
        totalAmount = cementTotal + totalFreight;
        resFinalFreightRow.style.display = "block";
        resFinalFreight.textContent = totalFreight.toLocaleString();
        resFreightTax.parentElement.style.display = "block";
      }

      // نمایش نتایج
      resTonnage.textContent = tonnage;
      resFreight.textContent = freight.toLocaleString();
      resFreightTax.textContent = freightTax.toLocaleString();
      resCement.textContent = cementTotal.toLocaleString();
      resTotal.textContent = totalAmount.toLocaleString();

      // قیمت پاکت تحویل کار
      const bagPrice = Math.round(totalAmount / (tonnage * 20));
      resBagPrice.textContent = bagPrice.toLocaleString();

      resultBox.style.display = "block";
    }

    // بارگذاری اولیه
    citySearch.dispatchEvent(new Event("input"));
  </script>
</body>
</html>
