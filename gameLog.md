<html>
<head>
    <meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">
</head>
<body>
# (Game Log records all data down order from most recent to least recent)

</body>
<script>
const url="http://206.188.196.247:8086//api/leaderboardUser/get";
fetch(url)
.then((response)=>{
    var data=response.json();
})
console.log(data);
</script>
</html>