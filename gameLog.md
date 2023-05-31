# (Game Log records all data down order from most recent to least recent)
<script>
const url="http://172.24.191.207:8086/api/leaderboardUser";
fetch(url)
.then((response)=>{
    var data=response.json();
})
console.log(data);
</script>