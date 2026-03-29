<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>💰 الادخار العائلي</title>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import { getFirestore, collection, addDoc, onSnapshot, doc, setDoc, updateDoc } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

// 🔴 ضع بياناتك هنا
const firebaseConfig = {
  apiKey: "PUT_YOURS",
  authDomain: "PUT_YOURS",
  projectId: "PUT_YOURS",
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

// إضافة عضو
window.addMember = async function() {
    let name = document.getElementById("name").value;
    if (!name) return;

    await setDoc(doc(db, "members", name), {
        paid: false
    });

    document.getElementById("name").value = "";
};

// تغيير حالة الدفع
window.toggle = async function(name, status) {
    await updateDoc(doc(db, "members", name), {
        paid: status
    });
};

// عرض مباشر
onSnapshot(collection(db, "members"), (snapshot) => {
    let html = "";
    let total = 0;
    let paid = 0;

    let amount = parseInt(document.getElementById("amount").value || 2000);

    snapshot.forEach(docSnap => {
        let data = docSnap.data();
        total += amount;

        if (data.paid) paid += amount;

        html += `
        <div class="member">
            <span>${docSnap.id}</span>
            <input type="checkbox" ${data.paid ? "checked" : ""}
            onchange="toggle('${docSnap.id}', this.checked)">
        </div>`;
    });

    document.getElementById("membersList").innerHTML = html;

    document.getElementById("result").innerHTML =
        "💰 المفروض: " + total + " دج<br>" +
        "✅ المدفوع: " + paid + " دج<br>" +
        "❌ المتبقي: " + (total - paid);
});
</script>

<style>
body {
    font-family: Arial;
    background: #eef2f7;
    direction: rtl;
    text-align: center;
}

.container {
    max-width: 500px;
    margin: auto;
    padding: 15px;
}

.card {
    background: white;
    padding: 15px;
    border-radius: 15px;
    margin: 10px 0;
}

.member {
    display: flex;
    justify-content: space-between;
    background: #f1f1f1;
    padding: 10px;
    margin: 5px;
    border-radius: 10px;
}

input, button {
    width: 90%;
    padding: 10px;
    margin: 5px;
}
</style>

</head>

<body>

<div class="container">

<div class="card">
<h3>👥 إضافة أخ</h3>
<input type="text" id="name" placeholder="اسم الأخ">
<button onclick="addMember()">➕ إضافة</button>
</div>

<div class="card">
<h3>💵 المبلغ</h3>
<input type="number" id="amount" value="2000">
</div>

<div class="card">
<h3>📋 الإخوة</h3>
<div id="membersList"></div>
</div>

<div class="card">
<h3>📊 النتائج</h3>
<div id="result"></div>
</div>

</div>

</body>
</html>
