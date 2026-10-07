<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>SRES Staff Fund & Loan Tracker (2026)</title>
  <style>
    :root {
      --primary: #1a73e8;
      --bg: #f4f6f9;
      --card-bg: #ffffff;
      --text: #202124;
      --border: #dadce0;
      --success: #34a853;
      --warning: #f9ab00;
      --danger: #ea4335;
    }
    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
      background-color: var(--bg);
      color: var(--text);
      margin: 0;
      padding: 16px;
    }
    .container {
      max-width: 1000px;
      margin: 0 auto;
    }
    h1, h3 { margin-top: 0; }
    
    /* Summary Dashboard Grid */
    .dashboard-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
      gap: 12px;
      margin-bottom: 20px;
    }
    .metric-card {
      background: var(--card-bg);
      border-radius: 8px;
      padding: 16px;
      border: 1px solid var(--border);
      box-shadow: 0 1px 3px rgba(0,0,0,0.05);
    }
    .metric-card .label { font-size: 0.8rem; color: #5f6368; font-weight: bold; }
    .metric-card .val { font-size: 1.3rem; font-weight: bold; margin-top: 6px; }
    .metric-card.highlight { background: #e8f0fe; border-color: var(--primary); color: var(--primary); }
    .metric-card.award { background: #fef7e0; border-color: var(--warning); color: #b06000; }

    /* Action Forms */
    .grid-2 {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 16px;
      margin-bottom: 20px;
    }
    @media (max-width: 768px) {
      .grid-2 { grid-template-columns: 1fr; }
    }
    .card {
      background: var(--card-bg);
      border-radius: 8px;
      padding: 16px;
      border: 1px solid var(--border);
      box-shadow: 0 1px 3px rgba(0,0,0,0.05);
    }
    .form-group {
      display: flex;
      flex-direction: column;
      gap: 6px;
      margin-bottom: 12px;
    }
    input, select, button {
      padding: 10px;
      border-radius: 6px;
      border: 1px solid var(--border);
      font-size: 0.95rem;
    }
    button {
      background: var(--primary);
      color: white;
      border: none;
      font-weight: bold;
      cursor: pointer;
    }
    button:hover { opacity: 0.9; }

    /* Tables */
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
      font-size: 0.85rem;
    }
    th, td {
      border: 1px solid var(--border);
      padding: 8px 10px;
      text-align: left;
    }
    th {
      background: #f1f3f4;
      font-weight: bold;
    }
    .badge {
      display: inline-block;
      padding: 2px 6px;
      border-radius: 4px;
      font-size: 0.75rem;
      font-weight: bold;
    }
    .badge-success { background: #e6f4ea; color: var(--success); }
    .badge-danger { background: #fce8e6; color: var(--danger); }
  </style>
</head>
<body>

<div class="container">
  <h1>SRES Staff Fund Tracker (2026)</h1>
  <p>12 Staff Members • ₱500/month contribution • 3% monthly loan interest • 3% late penalty (₱15)</p>

  <!-- Summary Dashboard -->
  <div class="dashboard-grid">
    <div class="metric-card">
      <div class="label">TOTAL CONTRIBUTED</div>
      <div class="val" id="totalContributed">₱0.00</div>
    </div>
    <div class="metric-card">
      <div class="label">LOAN INTEREST PROFIT</div>
      <div class="val" id="totalInterest">₱0.00</div>
    </div>
    <div class="metric-card">
      <div class="label">PENALTIES COLLECTED</div>
      <div class="val" id="totalPenalties">₱0.00</div>
    </div>
    <div class="metric-card highlight">
      <div class="label">CASH ON HAND</div>
      <div class="val" id="cashOnHand">₱0.00</div>
    </div>
    <div class="metric-card award">
      <div class="label">PATRONAGE AWARD LEADER</div>
      <div class="val" id="patronageLeader" style="font-size:1rem;">None</div>
    </div>
  </div>

  <!-- Action Forms -->
  <div class="grid-2">
    <!-- Log Contribution & Late Penalty Form -->
    <div class="card">
      <h3>1. Record Monthly Contribution</h3>
      <form id="contribForm">
        <div class="form-group">
          <label for="contribStaff">Staff Member:</label>
          <select id="contribStaff" required></select>
        </div>
        <div class="form-group">
          <label for="contribMonth">Month (2026):</label>
          <select id="contribMonth" required>
            <option value="Jan">January</option><option value="Feb">February</option>
            <option value="Mar">March</option><option value="Apr">April</option>
            <option value="May">May</option><option value="Jun">June</option>
            <option value="Jul">July</option><option value="Aug">August</option>
            <option value="Sep">September</option><option value="Oct">October</option>
            <option value="Nov">November</option><option value="Dec">December</option>
          </select>
        </div>
        <div class="form-group">
          <label for="isLate">Payment Status:</label>
          <select id="isLate">
            <option value="no">On-Time (₱500.00)</option>
            <option value="yes">Late / Missed Deadline (+3% Penalty = ₱515.00)</option>
          </select>
        </div>
        <button type="submit">Log Contribution</button>
      </form>
    </div>

    <!-- Log Loan Issue or Repayment -->
    <div class="card">
      <h3>2. Issue Loan or Record Payment</h3>
      <form id="loanForm">
        <div class="form-group">
          <label for="loanStaff">Staff Member:</label>
          <select id="loanStaff" required></select>
        </div>
        <div class="form-group">
          <label for="actionType">Action Type:</label>
          <select id="actionType" onchange="toggleLoanFields()">
            <option value="borrow">Borrow Money (Issue Loan)</option>
            <option value="repay">Repay Loan & 3% Interest</option>
          </select>
        </div>
        <div class="form-group" id="borrowAmountGroup">
          <label for="loanAmount">Loan Principal Amount (₱):</label>
          <input type="number" id="loanAmount" min="1" step="0.01" placeholder="e.g. 2000" />
        </div>
        <div class="form-group" id="repayAmountGroup" style="display:none;">
          <label for="principalRepaid">Principal Amount Being Paid (₱):</label>
          <input type="number" id="principalRepaid" min="1" step="0.01" placeholder="e.g. 2000" />
        </div>
        <button type="submit">Process Loan Transaction</button>
      </form>
    </div>
  </div>

  <!-- Staff Directory & Financial Summary Table -->
  <div class="card">
    <h3>Staff Financial Ledger</h3>
    <table id="staffTable">
      <thead>
        <tr>
          <th>Staff Member</th>
          <th>Months Paid</th>
          <th>Base Contributed</th>
          <th>Penalties Paid</th>
          <th>Total Borrowed</th>
          <th>Outstanding Balance</th>
          <th>Interest Profit Paid</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>
</div>

<script>
  const MONTHLY_BASE = 500;
  const PENALTY_RATE = 0.03; 
  const INTEREST_RATE = 0.03; 

  const staffNames = [
    "Tapio", "Allanic", "Orbeta", "Sios-e", 
    "Del Rosario", "Salva", "Pendang", "Ucat", 
    "Adolfo", "Estaño", "Armanoche", "Orlasan"
  ];

  let staffList = JSON.parse(localStorage.getItem('sres_staff_data_named')) || staffNames.map((name, i) => ({
    id: i + 1,
    name: name,
    paidMonths: [],
    baseContributed: 0,
    penaltiesPaid: 0,
    totalBorrowed: 0,
    outstandingPrincipal: 0,
    interestPaid: 0
  }));

  function saveData() {
    localStorage.setItem('sres_staff_data_named', JSON.stringify(staffList));
    render();
  }

  function populateDropdowns() {
    const contribSelect = document.getElementById('contribStaff');
    const loanSelect = document.getElementById('loanStaff');
    contribSelect.innerHTML = '';
    loanSelect.innerHTML = '';

    staffList.forEach(s => {
      contribSelect.innerHTML += `<option value="${s.id}">${s.name}</option>`;
      loanSelect.innerHTML += `<option value="${s.id}">${s.name}</option>`;
    });
  }

  function toggleLoanFields() {
    const type = document.getElementById('actionType').value;
    document.getElementById('borrowAmountGroup').style.display = type === 'borrow' ? 'flex' : 'none';
    document.getElementById('repayAmountGroup').style.display = type === 'repay' ? 'flex' : 'none';
  }

  function render() {
    const tbody = document.querySelector('#staffTable tbody');
    tbody.innerHTML = '';

    let grandContributed = 0;
    let grandPenalties = 0;
    let grandBorrowed = 0;
    let grandOutstanding = 0;
    let grandInterest = 0;

    let topBorrower = null;
    let maxBorrowed = 0;

    staffList.forEach(s => {
      grandContributed += s.baseContributed;
      grandPenalties += s.penaltiesPaid;
      grandBorrowed += s.totalBorrowed;
      grandOutstanding += s.outstandingPrincipal;
      grandInterest += s.interestPaid;

      if (s.totalBorrowed > maxBorrowed) {
        maxBorrowed = s.totalBorrowed;
        topBorrower = s.name;
      }

      tbody.innerHTML += `
        <tr>
          <td><strong>Engr. ${s.name}</strong></td>
          <td>${s.paidMonths.length} / 12</td>
          <td>₱${s.baseContributed.toLocaleString('en-US', {minimumFractionDigits: 2})}</td>
          <td>₱${s.penaltiesPaid.toLocaleString('en-US', {minimumFractionDigits: 2})}</td>
          <td>₱${s.totalBorrowed.toLocaleString('en-US', {minimumFractionDigits: 2})}</td>
          <td>
            ${s.outstandingPrincipal > 0 
              ? `<span class="badge badge-danger">₱${s.outstandingPrincipal.toLocaleString('en-US', {minimumFractionDigits: 2})}</span>` 
              : `<span class="badge badge-success">₱0.00</span>`}
          </td>
          <td>₱${s.interestPaid.toLocaleString('en-US', {minimumFractionDigits: 2})}</td>
        </tr>
      `;
    });

    const cashOnHand = grandContributed + grandPenalties + grandInterest - grandOutstanding;

    document.getElementById('totalContributed').innerText = `₱${grandContributed.toLocaleString('en-US', {minimumFractionDigits: 2})}`;
    document.getElementById('totalPenalties').innerText = `₱${grandPenalties.toLocaleString('en-US', {minimumFractionDigits: 2})}`;
    document.getElementById('totalInterest').innerText = `₱${grandInterest.toLocaleString('en-US', {minimumFractionDigits: 2})}`;
    document.getElementById('cashOnHand').innerText = `₱${cashOnHand.toLocaleString('en-US', {minimumFractionDigits: 2})}`;
    document.getElementById('patronageLeader').innerText = topBorrower ? `${topBorrower} (₱${maxBorrowed.toLocaleString()})` : "No borrows yet";
  }

  document.getElementById('contribForm').addEventListener('submit', (e) => {
    e.preventDefault();
    const staffId = parseInt(document.getElementById('contribStaff').value);
    const month = document.getElementById('contribMonth').value;
    const isLate = document.getElementById('isLate').value === 'yes';

    const staff = staffList.find(s => s.id === staffId);
    if (staff.paidMonths.includes(month)) {
      alert(`${staff.name} has already logged a contribution for ${month}.`);
      return;
    }

    staff.paidMonths.push(month);
    staff.baseContributed += MONTHLY_BASE;
    if (isLate) {
      staff.penaltiesPaid += (MONTHLY_BASE * PENALTY_RATE);
    }

    saveData();
    alert(`Contribution logged for ${staff.name} (${month}).`);
  });

  document.getElementById('loanForm').addEventListener('submit', (e) => {
    e.preventDefault();
    const staffId = parseInt(document.getElementById('loanStaff').value);
    const action = document.getElementById('actionType').value;
    const staff = staffList.find(s => s.id === staffId);

    if (action === 'borrow') {
      const amount = parseFloat(document.getElementById('loanAmount').value);
      if (!amount || amount <= 0) return;

      staff.totalBorrowed += amount;
      staff.outstandingPrincipal += amount;
      alert(`Loan of ₱${amount.toFixed(2)} issued to ${staff.name}.`);
    } else {
      const principalPaid = parseFloat(document.getElementById('principalRepaid').value);
      if (!principalPaid || principalPaid <= 0) return;

      if (principalPaid > staff.outstandingPrincipal) {
        alert(`Amount exceeds outstanding principal balance of ₱${staff.outstandingPrincipal.toFixed(2)}.`);
        return;
      }

      const interestDue = principalPaid * INTEREST_RATE;
      staff.outstandingPrincipal -= principalPaid;
      staff.interestPaid += interestDue;

      alert(`Payment recorded for ${staff.name}:\nPrincipal Paid: ₱${principalPaid.toFixed(2)}\n3% Interest Earned: ₱${interestDue.toFixed(2)}`);
    }

    document.getElementById('loanAmount').value = '';
    document.getElementById('principalRepaid').value = '';
    saveData();
  });

  populateDropdowns();
  render();
</script>
</body>
</html>
