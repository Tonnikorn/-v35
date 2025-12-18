<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Fuel Splitter</title>

<style>
:root{
  --orange1:#ff9a3c;
  --orange2:#ff6a00;
  --bg:#fff7f1;
  --card:#ffffff;
  --text:#333;
}

*{ box-sizing:border-box; }

body {
  margin: 0;
  font-family: system-ui, -apple-system, sans-serif;
  background: linear-gradient(135deg, var(--orange1), var(--orange2));
  color: var(--text);
}

.container {
  max-width: 420px;
  margin: auto;
  padding: 18px 14px 28px;
}

.card {
  background: var(--card);
  border-radius: 20px;
  padding: 18px;
  margin-bottom: 16px;
  box-shadow: 0 10px 25px rgba(0,0,0,.15);
}

h2 {
  text-align: center;
  margin: 0 0 14px;
  font-size: 20px;
}

label {
  font-weight: 600;
  margin-top: 14px;
  display: block;
  font-size: 14px;
}

input, select {
  width: 100%;
  padding: 14px;
  margin-top: 6px;
  border-radius: 16px;
  border: 1px solid #ddd;
  font-size: 16px;
  background: #fafafa;
}

button {
  width: 100%;
  padding: 15px;
  margin-top: 14px;
  border-radius: 18px;
  border: none;
  font-size: 16px;
  font-weight: bold;
  color: #fff;
  background: linear-gradient(135deg, var(--orange1), var(--orange2));
}

button.secondary{
  background:#999;
}

.result {
  margin-top: 16px;
  padding: 16px;
  border-radius: 18px;
  background: var(--bg);
  font-size: 18px;
  font-weight: bold;
  text-align: center;
}

.history {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.history-item {
  background: var(--bg);
  padding: 12px 14px;
  border-radius: 14px;
  font-size: 14px;
  display: flex;
  justify-content: space-between;
}
</style>
</head>

<body>
<div class="container">

  <!-- ตั้งค่ารถ -->
  <div class="card">
    <h2>⛽ ตั้งค่ารถ</h2>

    <label>ราคาน้ำมัน (บาท/ลิตร)</label>
    <input id="fuelPrice" type="number" value="31.85">

    <label>อัตราสิ้นเปลือง (กม./ลิตร)</label>
    <input id="kmPerLiter" type="number" value="18">
  </div>

  <!-- บันทึก -->
  <div class="card">
    <h2>🚗 บันทึกการขับ</h2>

    <label>ระยะทาง (กม.)</label>
    <input id="distance" type="number">

    <label>ประเภท</label>
    <select id="type">
      <option value="งาน">งาน</option>
      <option value="ชีวิตคู่">ชีวิตคู่ (หาร)</option>
    </select>

    <button onclick="calculate()">บันทึกค่าใช้จ่าย</button>
    <div id="output"></div>
  </div>

  <!-- สรุป -->
  <div class="card">
    <h2>📊 สรุปยอดรวม</h2>
    <button onclick="summary()">ดูสรุปยอด</button>
    <div id="summaryResult"></div>
  </div>

  <!-- ประวัติ -->
  <div class="card">
    <h2>📜 ประวัติย้อนหลัง</h2>
    <div id="history" class="history"></div>
    <button class="secondary" onclick="resetHistory()">รีเซ็ตประวัติทั้งหมด</button>
  </div>

</div>

<script>
function getHistory(){
  return JSON.parse(localStorage.getItem("fuelHistory") || "[]");
}

function saveLocal(data){
  const list = getHistory();
  list.unshift(data);
  localStorage.setItem("fuelHistory", JSON.stringify(list));
}

function loadHistory(){
  const list = getHistory();
  document.getElementById("history").innerHTML =
    list.length === 0
    ? "<div class='result'>ยังไม่มีข้อมูล</div>"
    : list.map(i=>`
      <div class="history-item">
        <span>${i.type} • ${i.distance} กม.</span>
        <strong>${i.totalCost.toFixed(2)} ฿</strong>
      </div>
    `).join("");
}

function calculate(){
  const fuel = +fuelPrice.value;
  const kmL = +kmPerLiter.value;
  const d = +distance.value;
  const type = document.getElementById("type").value;
  if(!d) return;

  const total = d * (fuel/kmL);

  document.getElementById("output").innerHTML =
    `<div class="result">💰 ${total.toFixed(2)} บาท</div>`;

  saveLocal({distance:d,type,totalCost:total});
  loadHistory();
  distance.value="";
}

function summary(){
  const list = getHistory();
  let work = 0, life = 0;

  list.forEach(i=>{
    if(i.type==="งาน") work += i.totalCost;
    if(i.type==="ชีวิตคู่") life += i.totalCost;
  });

  document.getElementById("summaryResult").innerHTML = `
    <div class="result">งาน: ${work.toFixed(2)} บาท</div>
    <div class="result">ชีวิตคู่: ${life.toFixed(2)} บาท</div>
  `;
}

function resetHistory(){
  if(confirm("ล้างประวัติทั้งหมดใช่ไหม?")){
    localStorage.removeItem("fuelHistory");
    loadHistory();
    document.getElementById("summaryResult").innerHTML="";
  }
}

loadHistory();
</script>
</body>
</html>
