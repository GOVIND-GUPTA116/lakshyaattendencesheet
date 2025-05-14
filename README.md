# lakshyaattendencesheet
DIGITA WORK
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>LIA ATTENDANCE SHEET</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      background: linear-gradient(to right, #89f7fe, #66a6ff);
    }
    body.dark {
      background: linear-gradient(to right, #0f2027, #203a43, #2c5364);
      color: #f1f1f1;
    }
    body.dark .clock {
      color: #f1f1f1;
    }
    h2 {
      text-align: center;
      font-size: 28px;
      color: #003366;
    }
    table {
      border-collapse: collapse;
      width: 100%;
      background: #fff;
    }
    table, th, td {
      border: 1px solid #444;
    }
    th, td {
      padding: 6px;
      text-align: center;
    }
    input, select, button {
      margin: 5px;
      padding: 6px;
      border-radius: 6px;
    }
    .card {
      background: #fff;
      border-radius: 16px;
      padding: 20px;
      margin: 15px 0;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
    }
    .clock {
      font-size: 18px;
      text-align: right;
    }
    @media print {
      .no-print, #loginScreen {
        display: none !important;
      }
    }
  </style>
</head>
<body>
<div class="clock" id="liveClock"></div>

<!-- LOGIN SCREEN -->
<div id="loginScreen" style="position: fixed; top: 0; left: 0; right: 0; bottom: 0;
  background: linear-gradient(to right, #ffecd2, #fcb69f); display: flex; flex-direction: column; align-items: center;
  justify-content: center; z-index: 9999;">
  <h2>Login to LIA ATTENDANCE SHEET</h2>
  <input type="text" id="loginUser" placeholder="Username">
  <input type="password" id="loginPass" placeholder="Password">
  <input type="checkbox" onclick="togglePassword()"> Show Password
  <br>
  <button onclick="checkLogin()">Login</button>
  <p id="loginMsg" style="color:red;"></p>
</div>

<h2>LIA ATTENDANCE SHEET</h2>

<!-- FILTER -->
<div class="card">
  <label for="filterClass">Individual Class:</label>
  <select id="filterClass">
    <option value="">All</option>
    <option value="1">Class 1</option>
    <option value="2">Class 2</option>
    <option value="3">Class 3</option>
    <option value="4">Class 4</option>
    <option value="5">Class 5</option>
    <option value="6">Class 6</option>
    <option value="7">Class 7</option>
    <option value="8">Class 8</option>
    <option value="9">Class 9</option>
    <option value="10">Class 10</option>
  </select>
  <label for="filterSection">Section:</label>
  <select id="filterSection">
    <option value="">All</option>
    <option value="A">A</option>
    <option value="B">B</option>
  </select>
  <button onclick="loadFilteredStudents()">📥 Load</button>
</div>

<!-- ADD STUDENT -->
<div class="card">
  <h3>Add Student</h3>
  <input type="text" id="rollNo" placeholder="Roll No">
  <input type="text" id="studentName" placeholder="Name">
  <input type="text" id="admissionNo" placeholder="Admission No">
  <input type="text" id="fatherName" placeholder="Father's Name">
  <select id="studentClass">
    <option value="">Select Class</option>
    <option value="1">Class 1</option>
    <option value="2">Class 2</option>
    <option value="3">Class 3</option>
    <option value="4">Class 4</option>
    <option value="5">Class 5</option>
    <option value="6">Class 6</option>
    <option value="7">Class 7</option>
    <option value="8">Class 8</option>
    <option value="9">Class 9</option>
    <option value="10">Class 10</option>
  </select>
  <select id="studentSection">
    <option value="">Section</option>
    <option value="A">A</option>
    <option value="B">B</option>
  </select>
  <button onclick="addStudent()">Add</button>
</div>

<!-- SEARCH STUDENT -->
<div class="card">
  <h3>Search Students</h3>
  <input type="text" id="searchBox" placeholder="Enter name...">
  <button onclick="filterStudents()">Search</button>
  <div id="searchResults"></div>
</div>

<!-- MARK ATTENDANCE -->
<div class="card">
  <h3>Mark Attendance</h3>
  <label>Select Date:</label>
  <input type="date" id="attendanceDate">

  <table id="attendanceTable">
    <thead>
      <tr>
        <th>Roll No</th>
        <th>Class</th>
        <th>Section</th>
        <th>Name</th>
        <th>Admission No</th>
        <th>Father's Name</th>
        <th>Status</th>
        <th>Action</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>

  <button onclick="saveAttendance()">💾 Save</button>
  <button onclick="exportToPDF()">📄 Export to PDF</button>
  <button onclick="window.print()">🖨️ Print</button>
  <button onclick="clearAttendance()">🧹 Clear All</button>
  <p id="message"></p>
</div>

<!-- VIEW ATTENDANCE -->
<div class="card">
  <h3>📅 View Attendance</h3>
  
  <label for="viewClass">Class:</label>
  <select id="viewClass" onchange="viewAttendanceByDate()">
    <option value="">All</option>
    <option value="1">Class 1</option>
    <option value="2">Class 2</option>
    <option value="3">Class 3</option>
    <option value="4">Class 4</option>
    <option value="5">Class 5</option>
    <option value="6">Class 6</option>
    <option value="7">Class 7</option>
    <option value="8">Class 8</option>
    <option value="9">Class 9</option>
    <option value="10">Class 10</option>
  </select>

  <label for="viewSection">Section:</label>
  <select id="viewSection" onchange="viewAttendanceByDate()">
    <option value="">All</option>
    <option value="A">A</option>
    <option value="B">B</option>
  </select>

  <label for="viewDateDropdown">Date:</label>
  <select id="viewDateDropdown" onchange="viewAttendanceByDate()">
    <option value="">-- Select Date --</option>
  </select>

  <div id="viewAttendanceTable"></div>
</div>

<!-- SCRIPTS -->
<script>
let students = JSON.parse(localStorage.getItem("students") || "[]");

function checkLogin() {
  const user = document.getElementById("loginUser").value.trim();
  const pass = document.getElementById("loginPass").value.trim();
  if (user === "Lakshya" && pass === "Lakshya") {
    document.getElementById("loginScreen").style.display = "none";
  } else {
    document.getElementById("loginMsg").innerText = "❌ Incorrect username or password.";
  }
}

function togglePassword() {
  const pass = document.getElementById("loginPass");
  pass.type = pass.type === "password" ? "text" : "password";
}

function addStudent() {
  const roll = document.getElementById("rollNo").value.trim();
  const name = document.getElementById("studentName").value.trim();
  const admission = document.getElementById("admissionNo").value.trim();
  const father = document.getElementById("fatherName").value.trim();
  const cls = document.getElementById("studentClass").value;
  const sec = document.getElementById("studentSection").value;
  if (!roll || !name || !admission || !father || !cls || !sec) return alert("Please fill all details");
  students.push({ id: Date.now(), roll, name, admission, father, cls, sec, present: 0, total: 0 });
  students.sort((a, b) => parseInt(a.roll) - parseInt(b.roll));
  localStorage.setItem("students", JSON.stringify(students));
  document.querySelectorAll("#rollNo, #studentName, #admissionNo, #fatherName").forEach(el => el.value = "");
  document.getElementById("studentClass").value = "";
  document.getElementById("studentSection").value = "";
  renderStudentTable();
}

function renderStudentTable(filtered = students) {
  const tbody = document.querySelector("#attendanceTable tbody");
  tbody.innerHTML = "";
  filtered.sort((a, b) => parseInt(a.roll) - parseInt(b.roll));
  filtered.forEach(s => {
    const tr = document.createElement("tr");
    tr.innerHTML = `
      <td>${s.roll}</td>
      <td>${s.cls}</td>
      <td>${s.sec}</td>
      <td>${s.name}</td>
      <td>${s.admission}</td>
      <td>${s.father}</td>
      <td><input type="checkbox" data-id="${s.id}" onchange="markAttendance(this)" ${s.marked ? "checked" : ""}></td>
      <td><button onclick="editStudent(${s.id})">✏️</button> <button onclick="deleteStudent(${s.id})">🗑️</button></td>
    `;
    tbody.appendChild(tr);
  });
}

function markAttendance(cb) {
  const id = +cb.getAttribute("data-id");
  const student = students.find(s => s.id === id);
  if (student) student.marked = cb.checked;
}

function saveAttendance() {
  const date = document.getElementById("attendanceDate").value;
  if (!date) return alert("Select a date");
  const formattedDate = new Date(date).toLocaleDateString('en-GB');
  let attendance = JSON.parse(localStorage.getItem("attendance") || "{}");
  attendance[formattedDate] = students.map(s => {
    const status = s.marked ? "Present" : "Absent";
    s.total = (s.total || 0) + 1;
    if (s.marked) s.present = (s.present || 0) + 1;
    return { ...s, status };
  });
  localStorage.setItem("attendance", JSON.stringify(attendance));
  localStorage.setItem("students", JSON.stringify(students));
  document.getElementById("message").innerText = `✅ Attendance saved for ${formattedDate}`;
  updateDateDropdown();
}

function updateDateDropdown() {
  const attendance = JSON.parse(localStorage.getItem("attendance") || "{}");
  const dropdown = document.getElementById("viewDateDropdown");
  dropdown.innerHTML = '<option value="">-- Select Date --</option>';
  Object.keys(attendance).forEach(date => {
    dropdown.innerHTML += `<option value="${date}">${date}</option>`;
  });
}

function viewAttendanceByDate() {
  const date = document.getElementById("viewDateDropdown").value;
  const filterClass = document.getElementById("viewClass").value;
  const filterSection = document.getElementById("viewSection").value;

  const data = JSON.parse(localStorage.getItem("attendance") || "{}")[date] || [];

  const filteredData = data.filter(s =>
    (!filterClass || s.cls === filterClass) &&
    (!filterSection || s.sec === filterSection)
  );

  if (!filteredData.length) {
    document.getElementById("viewAttendanceTable").innerHTML = "<p>No data available for selected filters.</p>";
    return;
  }

  let html = `<h4>Attendance for ${date}</h4>
    <button onclick="deleteAttendance('${date}')">🗑️ Delete This Attendance</button>
    <table>
      <tr><th>Class</th><th>Section</th><th>Roll No</th><th>Name</th><th>Status</th></tr>`;
  filteredData.forEach(r => {
    html += `<tr>
      <td>${r.cls}</td>
      <td>${r.sec}</td>
      <td>${r.roll}</td>
      <td>${r.name}</td>
      <td>${r.status}</td>
    </tr>`;
  });
  html += `</table>`;
  document.getElementById("viewAttendanceTable").innerHTML = html;
}

function deleteAttendance(date) {
  if (!confirm(`Are you sure you want to delete attendance for ${date}?`)) return;
  let attendance = JSON.parse(localStorage.getItem("attendance") || "{}");
  delete attendance[date];
  localStorage.setItem("attendance", JSON.stringify(attendance));
  document.getElementById("viewAttendanceTable").innerHTML = "<p>Deleted.</p>";
  updateDateDropdown();
}

function deleteStudent(id) {
  students = students.filter(s => s.id !== id);
  localStorage.setItem("students", JSON.stringify(students));
  renderStudentTable();
}

function editStudent(id) {
  const s = students.find(st => st.id === id);
  if (!s) return;
  document.getElementById("rollNo").value = s.roll;
  document.getElementById("studentName").value = s.name;
  document.getElementById("admissionNo").value = s.admission;
  document.getElementById("fatherName").value = s.father;
  document.getElementById("studentClass").value = s.cls;
  document.getElementById("studentSection").value = s.sec;
  students = students.filter(st => st.id !== id);
}

function clearAttendance() {
  document.querySelectorAll('#attendanceTable input[type="checkbox"]').forEach(cb => cb.checked = false);
  students.forEach(s => s.marked = false);
  document.getElementById("message").innerText = "🧹 Attendance cleared.";
}

function filterStudents() {
  const q = document.getElementById("searchBox").value.toLowerCase();
  const results = students.filter(s => s.name.toLowerCase().includes(q));
  document.getElementById("searchResults").innerHTML = results.map(s => `<div>${s.name} (${s.cls}${s.sec})</div>`).join('');
}

function loadFilteredStudents() {
  const cls = document.getElementById("filterClass").value;
  const sec = document.getElementById("filterSection").value;
  const filtered = students.filter(s => (!cls || s.cls === cls) && (!sec || s.sec === sec));
  renderStudentTable(filtered);
}

async function exportToPDF() {
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF();
  doc.setFontSize(12);
  doc.text("Attendance Sheet", 10, 10);
  doc.setFontSize(10);
  doc.text("Roll | Class | Sec | Name | Admission | Father | Status", 10, 18);
  let y = 24;
  students.forEach(s => {
    const status = s.marked ? "Present" : "Absent";
    const line = `${s.roll} | ${s.cls} | ${s.sec} | ${s.name} | ${s.admission} | ${s.father} | ${status}`;
    doc.text(line, 10, y);
    y += 6;
    if (y > 280) { doc.addPage(); y = 10; }
  });
  doc.save("attendance.pdf");
}

function updateClock() {
  document.getElementById("liveClock").innerText = new Date().toLocaleString();
}
setInterval(updateClock, 1000);

window.onload = () => {
  renderStudentTable();
  updateDateDropdown();
  updateClock();
};
</script>
</body>
</html>
