# Where-is-my-bus-
Find your bus time 
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Where is My Bus</title>
<style>
body { font-family: Arial, sans-serif; background: #f5f5f5; margin: 0; padding: 0; }
header { background: #ff6f00; color: white; padding: 20px; text-align: center; }
.container { padding: 20px; }
input, button { padding: 10px; margin: 5px 0; width: 100%; }
table { width: 100%; border-collapse: collapse; margin-top: 20px; }
th, td { border: 1px solid #ccc; padding: 10px; text-align: left; }
th { background: #ffcc80; }
tr:nth-child(even) { background: #fff3e0; }
</style>
</head>
<body>
<header>
<h1>Where is My Bus</h1>
<p>Search bus routes from your stop to destination</p>
</header>
<div class="container">
<label>From Stop:</label>
<input type="text" id="fromStop" placeholder="Enter starting stop">
<label>To Stop:</label>
<input type="text" id="toStop" placeholder="Enter destination stop">
<button onclick="searchBus()">Search Bus</button>
<div id="results"></div>
</div>

<script>
// Google Sheet CSV link
const sheetURL = 'https://docs.google.com/spreadsheets/d/1OpZCg-nq8MwGDbUFLw7TnvRlxhjY1uUgqMXtwCG_93Y/gviz/tq?tqx=out:csv';

async function searchBus() {
    const from = document.getElementById('fromStop').value.trim().toLowerCase();
    const to = document.getElementById('toStop').value.trim().toLowerCase();
    const resultsDiv = document.getElementById('results');
    resultsDiv.innerHTML = '<p>Searching...</p>';

    try {
        const res = await fetch(sheetURL);
        const data = await res.text();
        const rows = data.split('\n').slice(1); // skip header
        let html = '<table><tr><th>Route</th><th>From</th><th>To</th><th>Departure</th><th>Arrival</th><th>Bus Type</th></tr>';
        let count = 0;

        rows.forEach(row => {
            const cols = row.split(',');
            if (cols.length < 6) return;
            const routeFrom = cols[1].trim().toLowerCase();
            const routeTo = cols[2].trim().toLowerCase();
            if (routeFrom.includes(from) && routeTo.includes(to)) {
                html += `<tr>
                <td>${cols[0]}</td>
                <td>${cols[1]}</td>
                <td>${cols[2]}</td>
                <td>${cols[3]}</td>
                <td>${cols[4]}</td>
                <td>${cols[5]}</td>
                </tr>`;
                count++;
            }
        });

        if (count === 0) {
            resultsDiv.innerHTML = '<p>No buses found for this route.</p>';
        } else {
            html += '</table>';
            resultsDiv.innerHTML = html;
        }

    } catch (err) {
        console.error(err);
        resultsDiv.innerHTML = '<p>Error loading bus data. Check your Google Sheet link.</p>';
    }
}
</script>
</body>
</html>

