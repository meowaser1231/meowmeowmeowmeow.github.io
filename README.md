<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Roblox Dev Product Scanner</title>
  <style>
    body { font-family: sans-serif; padding: 20px; background: #f4f4f4; }
    h1 { color: #333; }
    input, button {
      padding: 10px; font-size: 16px; margin: 5px 0;
    }
    button { cursor: pointer; }
    .product {
      background: white; padding: 10px; margin: 10px 0;
      border-radius: 6px; box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
  </style>
</head>
<body>
  <h1>🧾 Roblox Dev Product Scanner</h1>
  <input type="text" id="gameIdInput" placeholder="Enter Place or Universe ID" />
  <button onclick="startScan()">Fetch Developer Products</button>
  <div id="results"></div>

  <script>
    async function getUniverseId(placeId) {
      const res = await fetch(`https://apis.roblox.com/universes/v1/places/${placeId}/universe`);
      if (!res.ok) throw new Error("Invalid Place ID or Roblox API issue.");
      const data = await res.json();
      return data.universeId;
    }

    async function fetchDevProducts(universeId) {
      const res = await fetch(`https://develop.roblox.com/v1/universes/${universeId}/developerproducts`);
      if (!res.ok) throw new Error("Failed to fetch developer products.");
      const data = await res.json();
      return data;
    }

    async function startScan() {
      const input = document.getElementById("gameIdInput").value.trim();
      const resultsDiv = document.getElementById("results");
      resultsDiv.innerHTML = "⏳ Scanning...";

      try {
        let universeId = input;

        // If input is a placeId, convert it
        if (isFinite(input) && input.length < 12) {
          universeId = await getUniverseId(input);
        }

        const products = await fetchDevProducts(universeId);

        if (products.length === 0) {
          resultsDiv.innerHTML = "No developer products found.";
          return;
        }

        resultsDiv.innerHTML = "";
        for (const product of products) {
          const div = document.createElement("div");
          div.className = "product";
          div.innerHTML = `<strong>${product.Name}</strong><br>ID: ${product.ProductId}<br>Price: ${product.PriceInRobux || "N/A"} Robux<br>Description: ${product.Description || "None"}`;
          resultsDiv.appendChild(div);
        }
      } catch (e) {
        resultsDiv.innerHTML = `<span style="color: red;">❌ ${e.message}</span>`;
      }
    }
  </script>
</body>
</html>
