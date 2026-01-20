<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>أقرب طبيب</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>

<style>
body{font-family:Arial;background:#f4f6f8;margin:0;direction:rtl}
header{background:#0d6efd;color:white;padding:15px;text-align:center}
.container{padding:15px}
select,button{width:100%;padding:12px;margin-top:10px;font-size:16px}
button{background:#0d6efd;color:white;border:none;border-radius:6px}
#map{height:300px;margin-top:15px;border-radius:10px}
#result{margin-top:10px;font-weight:bold}
</style>
</head>

<body>

<header>🩺 أوجد أقرب طبيب</header>

<div class="container">

<select id="symptom">
  <option value="">-- اختاري العرض --</option>
  <option value="باطنة">صداع / تعب عام</option>
  <option value="صدر">كحة / ضيق تنفس</option>
  <option value="جلدية">حساسية / جلد</option>
  <option value="مخ وأعصاب">دوخة / صداع شديد</option>
</select>

<button onclick="findDoctor()">بحث</button>

<div id="result"></div>
<div id="map"></div>

</div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<script>
// ================= قاعدة بيانات الأطباء =================
const doctors = [
  {name:"د. أحمد علي", specialty:"باطنة", lat:26.155, lng:32.716},
  {name:"د. محمد حسن", specialty:"صدر", lat:26.160, lng:32.720},
  {name:"د. ريم حسين", specialty:"جلدية", lat:26.158, lng:32.718},
  {name:"د. سارة محمود", specialty:"مخ وأعصاب", lat:26.150, lng:32.710}
];

let map;

// ============== حساب المسافة =================
function distance(lat1,lng1,lat2,lng2){
  return Math.sqrt(Math.pow(lat1-lat2,2)+Math.pow(lng1-lng2,2));
}

// ============== البحث =================
function findDoctor(){
  const spec = document.getElementById("symptom").value;
  if(!spec){alert("اختاري العرض");return;}

  navigator.geolocation.getCurrentPosition(pos=>{
    const userLat = pos.coords.latitude;
    const userLng = pos.coords.longitude;

    const filtered = doctors.filter(d=>d.specialty===spec);
    if(filtered.length===0){alert("لا يوجد دكتور متاح");return;}

    let nearest = filtered[0];
    let minDist = distance(userLat,userLng,nearest.lat,nearest.lng);

    filtered.forEach(d=>{
      const dist = distance(userLat,userLng,d.lat,d.lng);
      if(dist < minDist){minDist=dist; nearest=d;}
    });

    document.getElementById("result").innerHTML =
      `✔️ أقرب طبيب: <br> ${nearest.name} <br> التخصص: ${nearest.specialty}`;

    if(map) map.remove();
    map = L.map('map').setView([userLat,userLng],14);

    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);

    L.marker([userLat,userLng]).addTo(map).bindPopup("موقعك").openPopup();
    L.marker([nearest.lat,nearest.lng]).addTo(map)
      .bindPopup(nearest.name + "<br>" + nearest.specialty);
  });
}
</script>

</body>
</html>
