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
      max-width: 100%;
      margin: auto;
      background-color: #fff;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 8px rgba(0,0,0,0.1);
      overflow-x: auto;
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
      font-size: 0.9rem;
      min-width: 1200px;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 8px;
      text-align: center;
    }
    th {
      background-color: #f0f0f0;
    }
    .category {
      background-color: #e0e0e0;
      font-weight: bold;
      text-align: left;
    }
    input[type="number"], input[type="text"] {
      width: 100%;
      box-sizing: border-box;
      padding: 6px;
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
          <th>Opening</th>
          <th>Received</th>
          <th>Total Bottles</th>
          <th>Consumed</th>
          <th>Return</th>
          <th>Sealed Return</th>
          <th>Breakage</th>
          <th>Loss/Theft</th>
          <th>Closing</th>
          <th>Comments</th>
        </tr>
      </thead>
      <tbody id="tableBody"></tbody>
      <tfoot>
        <tr>
          <td>Total</td>
          <td id="totalOpening">0</td>
          <td id="totalReceived">0</td>
          <td id="totalTotal">0</td>
          <td id="totalConsumed">0</td>
          <td id="totalReturn">0</td>
          <td id="totalSealed">0</td>
          <td id="totalBreakage">0</td>
          <td id="totalLoss">0</td>
          <td id="totalClosing">0</td>
          <td></td>
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
    const fields = ["opening", "received", "total", "consumed", "return", "sealed", "breakage", "loss", "closing", "comments"];

    for (const [category, items] of Object.entries(alcoholData)) {
      const catRow = document.createElement("tr");
      catRow.classList.add("category");
      const catCell = document.createElement("td");
      catCell.colSpan = fields.length + 1;
      catCell.textContent = category;
      catRow.appendChild(catCell);
      tableBody.appendChild(catRow);

      items.forEach(name => {
        const row = document.createElement("tr");
        const nameCell = document.createElement("td");
        nameCell.textContent = name;
        row.appendChild(nameCell);

        fields.forEach(field => {
          const td = document.createElement("td");
          const input = document.createElement("input");
          input.name = `${field}_${index}`;
          input.type = field === "comments" ? "text" : "number";
          input.value = field === "comments" ? "" : "0";
          input.min = 0;
          if (field !== "comments") input.addEventListener("input", updateTotal);
          td.appendChild(input);
          row.appendChild(td);
        });

        tableBody.appendChild(row);
        index++;
      });
    }

    function updateTotal() {
      const totals = {
        opening: 0, received: 0, total: 0, consumed: 0,
        return: 0, sealed: 0, breakage: 0, loss: 0, closing: 0
      };

      document.querySelectorAll("#tableBody tr:not(.category)").forEach(row => {
        const inputs = row.querySelectorAll("input");
        fields.forEach((field, i) => {
          if (field !== "comments") {
            totals[field] += parseInt(inputs[i].value) || 0;
          }
        });
      });

      Object.keys(totals).forEach(field => {
        const id = `total${field.charAt(0).toUpperCase() + field.slice(1)}`;
        const element = document.getElementById(id);
        if (element) element.textContent = totals[field];
      });
    }

    function printInventory() {
      window.print();
    }

    function exportToCSV() {
      const date = document.getElementById("inventoryDate").value || "Not Specified";
      const signature = document.getElementById("signature").value || "Not Signed";

      let csv = `Date,${date}\nAlcohol Name,${fields.map(f => f.charAt(0).toUpperCase() + f.slice(1)).join(",")}\n`;

      document.querySelectorAll("#tableBody tr").forEach(row => {
        if (row.classList.contains("category")) return;
        const name = row.cells[0].textContent;
        const values = Array.from(row.querySelectorAll("input")).map(input => input.value || "");
        csv += `"${name}",${values.join(",")}\n`;
      });

      const totalRow = fields.map(f => {
        if (f === "comments") return "";
        return document.getElementById(`total${f.charAt(0).toUpperCase() + f.slice(1)}`).textContent;
      });

      csv += `Total,${totalRow.join(",")}\n`;
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
