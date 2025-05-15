<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Alcohol Inventory Sheet</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background-color: #f8f8f8;
      margin: 0;
      padding: 10px;
    }
    .container {
      max-width: 1000px;
      margin: auto;
      background-color: #fff;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 8px rgba(0,0,0,0.1);
    }
    h1 {
      text-align: center;
      margin-bottom: 20px;
      font-size: 1.8rem;
    }
    .form-group {
      margin: 15px 0;
      display: flex;
      flex-direction: column;
    }
    .form-group label {
      margin-bottom: 5px;
      font-weight: bold;
    }
    input[type="date"], input[type="text"] {
      padding: 10px;
      font-size: 1rem;
      border: 1px solid #ccc;
      border-radius: 6px;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
      font-size: 0.95rem;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 10px;
    }
    th {
      background-color: #f0f0f0;
    }
    .category {
      background-color: #e0e0e0;
      font-weight: bold;
      text-align: left;
    }
    input[type="number"] {
      width: 100%;
      padding: 6px;
      box-sizing: border-box;
    }
    tfoot td {
      font-weight: bold;
      background-color: #f9f9f9;
    }
    .buttons {
      display: flex;
      flex-direction: column;
      align-items: center;
      margin: 20px 0;
    }
    .buttons button {
      padding: 10px 20px;
      margin: 5px;
      font-size: 1rem;
      border: none;
      border-radius: 5px;
      background-color: #007BFF;
      color: white;
      cursor: pointer;
    }
    .buttons button:hover {
      background-color: #0056b3;
    }

    @media (min-width: 600px) {
      .form-group {
        flex-direction: row;
        justify-content: space-between;
        align-items: center;
      }
      .form-group label, .form-group input {
        width: 48%;
      }
      .buttons {
        flex-direction: row;
        justify-content: center;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Alcohol Inventory Sheet</h1>

    <div class="form-group">
      <label for="inventoryDate">Inventory Date</label>
      <input type="date" id="inventoryDate" />
    </div>

    <div class="buttons">
      <button onclick="printInventory()">🖨️ Print</button>
      <button onclick="exportToCSV()">📄 Export to Excel</button>
    </div>

    <table id="inventoryTable">
      <thead>
        <tr>
          <th>Alcohol Name</th>
          <th>Quantity</th>
        </tr>
      </thead>
      <tbody id="tableBody"></tbody>
      <tfoot>
        <tr>
          <td>Total</td>
          <td id="totalQuantity">0</td>
        </tr>
      </tfoot>
    </table>

    <div class="form-group">
      <label for="signature">Signature</label>
      <input type="text" id="signature" placeholder="Enter your name or sign here" />
    </div>
  </div>

  <script>
    const alcoholData = {
      "Whisky": [
        "Chivas Regal 12yrs", "Chivas Regal 18yrs", "Chivas Regal 21yrs",
        "Glenlivet 12yrs", "Glenlivet 15yrs", "Glenlivet 18yrs",
        "Glenfiddich 12yrs", "Glenfiddich 15yrs", "Glenfiddich 18yrs", "Glenfiddich 21yrs"
      ],
      "Vodka": ["Absolut Vodka", "Grey Goose Vodka", "Russian Vodka"],
      "Gin": ["Bombay Sapphire", "Hendrik's Gin", "Ruco Gin"],
      "Wine": ["Red Wine", "White Wine"],
      "Rum": ["Old Monk", "Bacardi"],
      "Beer": ["Kingfisher Ultra", "Corona Beer"],
      "Others": ["Champagne", "Tequila"]
    };

    const tableBody = document.getElementById("tableBody");

    let index = 0;
    for (const [category, items] of Object.entries(alcoholData)) {
      const catRow = document.createElement("tr");
      catRow.classList.add("category");
      const catCell = document.createElement("td");
      catCell.colSpan = 2;
      catCell.textContent = category;
      catRow.appendChild(catCell);
      tableBody.appendChild(catRow);

      items.forEach(name => {
        const row = document.createElement("tr");
        const nameCell = document.createElement("td");
        nameCell.textContent = name;

        const qtyCell = document.createElement("td");
        const input = document.createElement("input");
        input.type = "number";
        input.min = 0;
        input.value = 0;
        input.name = `qty_${index++}`;
        input.addEventListener("input", updateTotal);

        qtyCell.appendChild(input);
        row.appendChild(nameCell);
        row.appendChild(qtyCell);
        tableBody.appendChild(row);
      });
    }

    function updateTotal() {
      const inputs = document.querySelectorAll('input[type="number"]');
      let total = 0;
      inputs.forEach(input => {
        total += parseInt(input.value) || 0;
      });
      document.getElementById("totalQuantity").textContent = total;
    }

    function printInventory() {
      window.print();
    }

    function exportToCSV() {
      const date = document.getElementById("inventoryDate").value || "Not Specified";
      const signature = document.getElementById("signature").value || "Not Signed";

      let csv = `Date,${date}\nAlcohol Name,Quantity\n`;

      document.querySelectorAll("#tableBody tr").forEach(row => {
        if (row.classList.contains("category")) return;
        const name = row.cells[0].textContent;
        const qty = row.querySelector("input").value || 0;
        csv += `"${name}",${qty}\n`;
      });

      csv += `Total,${document.getElementById("totalQuantity").textContent}\n`;
      csv += `Signature,${signature}\n`;

      const blob = new Blob([csv], { type: 'text/csv' });
      const link = document.createElement("a");
      link.href = URL.createObjectURL(blob);
      link.download = "alcohol_inventory.csv";
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }
  </script>
</body>
</html>
