# (Game Log records all data down order from most recent to least recent)
<script>
const url="http://206.188.196.247:8086/api/leaderboardUser";
fetch(url)
.then((response)=>{
    var data=response.json();
})
console.log(data);
</script>
