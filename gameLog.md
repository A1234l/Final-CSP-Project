# (Game Log records all data down order from most recent to least recent)
<table id="recentGames" style="width: 100%;">
  <tr>
    <th>Username</th>
    <th>Distance</th>
    <th>Score</th>
    <th>Date</th>
    <th>Location</th>
  </tr>
  <tbody id="get">
  </tbody>
</table>

<script>
// Function to update the leaderboard
function updateLeaderboard() {
    console.log('Updating leaderboard...');

    // Make the asynchronous GET request to retrieve leaderboard data from the API
    $.getJSON('http://172.28.82.34:8086/api/leaderboardUser/get')
        .done(function (data) {
            // Clear the current leaderboard on update
            $('#recentGames').empty();

            // Add labels to the leaderboard table
            var labelsRow = '<tr>' +
                '<th>Name</th>' +
                '<th>Score</th>' +
                '<th>Date</th>' +
                '<th>Locations</th>' +
                '<th>Total Distance</th>' +
                '</tr>';

            $('#recentGames').append(labelsRow);

            // Adds the new data to the leaderboard from the API response
            data.forEach(function (entry) {
                var name = entry.name;
                var score = entry.score;
                var date = entry.dateG;
                var locations = entry.locations.list.join(', ');
                var totalDistance = entry.tot_distance;

                var row = '<tr>' +
                    '<td>' + name + '</td>' +
                    '<td>' + score + '</td>' +
                    '<td>' + date + '</td>' +
                    '<td>' + locations + '</td>' +
                    '<td>' + totalDistance + '</td>' +
                    '</tr>';

                $('#recentGames').append(row);
            });

            console.log('Leaderboard updated.');
        })
        .fail(function (error) {
            console.log('Error:', error);
        });
}

updateLeaderboard();
</script>