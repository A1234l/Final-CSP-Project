<html>
<head>
<h1>Game Log</h1>
</head>
<body>
<table id="recentGames" style="width: 100%;">
  <tr>
    <th>Username</th>
    <th>Distance</th>
    <th>Score</th>
    <th>Date</th>
    <th>Location</th>
  </tr>
  <tbody id="getList">
  </tbody>
</table>

<script>
  const resultContainer = document.getElementById("getList");
  const url = "http://192.168.121.27:8086/api/leaderboardUser"
  const read_fetch = url + '/get';

  // Load users on page entry
  read_users();

  // Display User Table, data is fetched from Backend Database
  function read_users() {
    // prepare fetch options
    const read_options = {
      method: 'GET', // *GET, POST, PUT, DELETE, etc.
      mode: 'cors', // no-cors, *cors, same-origin
      cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
      credentials: 'omit', // include, *same-origin, omit
      headers: {
        'Content-Type': 'application/json'
      },
    };

    // fetch the data from API
    fetch(read_fetch, read_options)
      // response is a RESTful "promise" on any successful fetch
      .then(response => {
        // check for response errors
        if (response.status !== 200) {
            const errorMsg = 'Database read error: ' + response.status;
            console.log(errorMsg);
            const tr = document.createElement("tr");
            const td = document.createElement("td");
            td.innerHTML = errorMsg;
            tr.appendChild(td);
            resultContainer.appendChild(tr);
            return;
        }
        // valid response will have json data
      response.json().then(data => {
          console.log(data);
          data.sort(function(a, b) {
            return b.score - a.score;
          });
        for (let i = 0; i < data.length; i++) {
          const row = data[i];
          console.log(row);
          add_row(row);
          }
        })
    })
    // catch fetch errors (ie ACCESS to server blocked)
    .catch(err => {
      console.error(err);
      const tr = document.createElement("tr");
      const td = document.createElement("td");
      td.innerHTML = err;
      tr.appendChild(td);
      resultContainer.appendChild(tr);
    });
  }

  function add_row(data) {
    const tr = document.createElement("tr");
    const name = document.createElement("td");
    const score = document.createElement("td");
    const locations = document.createElement("td");
    const tot_distance = document.createElement("td");
    const calc_distance = document.createElement("td");
    const dateG = document.createElement("td");
  
    // Convert integer data into strings
    let scoreGet = data.score;
    let scoreString = scoreGet.toString();

    let tot_distanceGet = data.tot_distance;
    let tot_distanceString = tot_distanceGet.toString();

    let calc_distanceGet = data.calc_distance;
    let calc_distanceString = calc_distanceGet.toString();

    // obtain data that is specific to the API
    name.innerHTML = data.name;
    score.innerHTML = scoreString;

    // Obtain location data by iterating through the list in the data
    locationsText = "";
    let locationsGet = data.locations;
    for(let i=0; i < locationsGet.length; i++){
      locationsText += locationsGet[i] + ", ";
    }

    locations.innerHTML = locationsText;
    tot_distance.innerHTML = tot_distanceString;
    calc_distance.innerHTML = calc_distanceString;
    dateG.innerHTML = data.dateG;

    // add HTML to container
    tr.appendChild(name);
    tr.appendChild(score);
    tr.appendChild(locations);
    tr.appendChild(tot_distance);
    tr.appendChild(calc_distance);
    tr.appendChild(dateG);

    resultContainer.appendChild(tr);
  }
</script>
</body>
</html>