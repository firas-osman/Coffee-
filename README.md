# Coffee-
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Supply Box Calculator</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 20px; background: #f4f4f4; }
    h2 { color: #333; }
    label { display: block; margin-top: 15px; }
    input { padding: 8px; width: 100%; max-width: 300px; margin-bottom: 10px; }
    table { width: 100%; max-width: 1200px; border-collapse: collapse; margin-top: 20px; background: #fff; }
    th, td { padding: 10px; border: 1px solid #ccc; text-align: center; font-size: 14px; }
    th { background: #eee; }
  </style>
</head>
<body>
  <h2>Supply Box Calculator</h2>

  <label>Expected Number of People (minimum):</label>
  <input type="number" id="minPeople" placeholder="e.g. 5805" />

  <h3>Stock and Pack Sizes</h3>

  <label>Coffee Packs in Stock:</label>
  <input type="number" id="coffeeStock" placeholder="e.g. 24" />  
  <label>Weight per Coffee Pack (kg):</label>
  <input type="number" step="0.0001" id="coffeePackWeight" value="0.009" />

  <label>Topping Packs in Stock:</label>
  <input type="number" id="toppingStock" placeholder="e.g. 24" />  
  <label>Weight per Topping Pack (kg):</label>
  <input type="number" step="0.0001" id="toppingPackWeight" value="0.007" />

  <label>Chocolate Packs in Stock:</label>
  <input type="number" id="chocoStock" placeholder="e.g. 26" />  
  <label>Weight per Chocolate Pack (kg):</label>
  <input type="number" step="0.0001" id="chocoPackWeight" value="0.0005" />

  <button onclick="calculate()" style="margin-top: 20px; padding: 10px 20px;">Calculate</button>

  <div id="output"></div>

  <script>
    function calculate() {
      const minPeople = parseInt(document.getElementById("minPeople").value);
      const coffeePacks = parseInt(document.getElementById("coffeeStock").value || 0);
      const toppingPacks = parseInt(document.getElementById("toppingStock").value || 0);
      const chocoPacks = parseInt(document.getElementById("chocoStock").value || 0);

      const coffeePackWeight = parseFloat(document.getElementById("coffeePackWeight").value || 0);
      const toppingPackWeight = parseFloat(document.getElementById("toppingPackWeight").value || 0);
      const chocoPackWeight = parseFloat(document.getElementById("chocoPackWeight").value || 0);

      if (!minPeople || minPeople <= 0) {
        document.getElementById("output").innerHTML = "<p>Please enter a valid number of expected people.</p>";
        return;
      }

      const maxPeople = Math.ceil(minPeople * 1.5);
      const avgPeople = Math.round((minPeople + maxPeople) / 2);

      const items = [
        { name: "Coffee", perPerson: 0.009, packKg: coffeePackWeight, stockPacks: coffeePacks },
        { name: "Topping", perPerson: 0.007, packKg: toppingPackWeight, stockPacks: toppingPacks },
        { name: "Chocolate", perPerson: 0.0005, packKg: chocoPackWeight, stockPacks: chocoPacks }
      ];

      const boxSize = 6; // 6kg per box

      let table = `<h3>Calculated Needs Summary</h3>
      <p>Minimum Expected People: ${minPeople} | Maximum: ${maxPeople} | Best Estimate: ${avgPeople}</p>
      <table>
        <tr>
          <th>Item</th>
          <th>Per Person Use (kg)</th>
          <th>Packs in Stock</th>
          <th>Pack Weight (kg)</th>
          <th>Stock Total (kg)</th>
          <th>Required (Min Boxes)</th>
          <th>Required (Best Boxes)</th>
          <th>Required (Max Boxes)</th>
          <th>Shortfall (kg)</th>
          <th>Boxes to Order</th>
          <th>Shortfall (Boxes)</th>
          <th>Leftover (kg)</th>
          <th>Leftover (Boxes)</th>
        </tr>`;

      items.forEach(item => {
        const stockKg = item.stockPacks * item.packKg;
        const requiredMin = minPeople * item.perPerson;
        const requiredBest = avgPeople * item.perPerson;
        const requiredMax = maxPeople * item.perPerson;

        const requiredMinBoxes = Math.ceil(requiredMin / boxSize);
        const requiredBestBoxes = Math.ceil(requiredBest / boxSize);
        const requiredMaxBoxes = Math.ceil(requiredMax / boxSize);

        const shortfallKg = Math.max(0, requiredBest - stockKg);
        const boxesToOrder = Math.ceil(shortfallKg / boxSize);
        const shortfallBoxes = shortfallKg > 0 ? (shortfallKg / boxSize).toFixed(2) : "0.00";

        const leftoverKg = Math.max(0, stockKg - requiredBest);
        const leftoverBoxes = leftoverKg > 0 ? (leftoverKg / boxSize).toFixed(2) : "0.00";

        table += `
          <tr>
            <td>${item.name}</td>
            <td>${item.perPerson.toFixed(4)}</td>
            <td>${item.stockPacks}</td>
            <td>${item.packKg.toFixed(4)}</td>
            <td>${stockKg.toFixed(2)}</td>
            <td>${requiredMinBoxes}</td>
            <td>${requiredBestBoxes}</td>
            <td>${requiredMaxBoxes}</td>
            <td>${shortfallKg.toFixed(2)}</td>
            <td>${boxesToOrder}</td>
            <td>${shortfallBoxes}</td>
            <td>${leftoverKg.toFixed(2)}</td>
            <td>${leftoverBoxes}</td>
          </tr>`;
      });

      table += `</table>`;

      document.getElementById("output").innerHTML = table;
    }
  </script>
</body>
</html>
