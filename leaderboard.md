<style>
  #title_{
    font-size: 150%;
  }
</style>
<h1>(Leaderboard records down the top scorers and omits all other data)</h1>
<h1><strong>User Information</strong></h1>
<table id="recentGames" style="width: 100%;">
  <tr>
    <th>Username</th>
    <th>Distance</th>
    <th>Score</th>
    <th>Date</th>
    <th>Locations</th>
  </tr>
  <tbody id="get">
  </tbody>
</table>


<script>
 // prepare HTML result container for new output
  const resultContainer = document.getElementById("get");
  // prepare URL's to allow easy switch from deployment and localhost
  //const url = "http://localhost:8086/api/users"
  const url = "http://206.188.196.247:8086/api/leaderboardUser"
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
        for (let i = 0; i < 5; i++) {
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
    const tot_distance = document.createElement("td");
    const score = document.createElement("td");
    const dateG = document.createElement("td");
    const locations = document.createElement("td")
  
    // obtain data that is specific to the API
    name.innerHTML = data.name; 
    tot_distance.innerHTML = data.tot_distance; 
    score.innerHTML = data.score; 
    dateG.innerHTML = data.dateG; 
    locations.innerHTML = data.locations;

    // add HTML to container
    tr.appendChild(name);
    tr.appendChild(tot_distance);
    tr.appendChild(score);
    tr.appendChild(dateG);
    tr.appendChild(locations);

    resultContainer.appendChild(tr);
  } 
</script>
<style>
  .banner{
    width: 100%;
    height: 200% !important;
    background-image: linear-gradient(rgba(0,0,0,0.75), rgba(0,0,0,0.75)),url(homepg.jpg);
    background-size: cover;
    background-position: center !important ;
  }
</style>