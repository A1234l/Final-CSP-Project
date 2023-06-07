---
permalink: /javascriptTicket
---
<head>
    <script src="https://code.jquery.com/jquery-1.12.4.min.js"></script>
</head>
<h1 style="color:white; text-align: left;">Table</h1>
<style>
  #sample_style{
    width: 100%;
    color:white;
    border: 4px solid #808080;
  }
</style>
<label>Name:</label>
<input type="text" id="name" name="name"><br><br>
<label>Score:</label>
<label id="score" name="score">0</label><br><br>
<button onclick="addEntry()">Submit</button>
<table id="table" style="width: 100%; color: #707070; border: 4px solid #909090;">
  <tr>
    <th>Name</th>
    <th>Score</th>
  </tr>
  <tbody id="get">
  </tbody>
</table>
<br><br>
<canvas id="canvas" width="1500" height="800" style="border:1px solid #000000;"></canvas>

<script>
    function partition(arr, l, m, r){
        var n1 = m - l + 1;
        var n2 = r - m;
        var L = new Array(n1);
        var R = new Array(n2);
        
        for (var i = 0; i < n1; i++)
            L[i] = arr[l + i];
        for (var j = 0; j < n2; j++)
            R[j] = arr[m + 1 + j];
        
        var i = 0;
        var j = 0;
        var k = l;
     
        while (i < n1 && j < n2) {
            if (L[i]["score"] <= R[j]["score"]) {
                arr[k] = L[i];
                i++;
            }
            else {
                arr[k] = R[j];
                j++;
            }
            k++;
        }
        while (i < n1) {
            arr[k] = L[i];
            i++;
            k++;
        }
        while (j < n2) {
            arr[k] = R[j];
            j++;
            k++;
        }
    }
    

    function mergeSort(arr,l, r){
        if(l>=r){
            return;
        }
        var m =l+ parseInt((r-l)/2);
        mergeSort(arr,l,m);
        mergeSort(arr,m+1,r);
        partition(arr,l,m,r);
    }

    let array = [];

    function addEntry(){
        name = document.getElementById('name').value;
        score = document.getElementById("score").value;
        array.push({name,score});
        mergeSort(array,0,array.length-1);
        for (let i=0;i<array.length-1;i++){
            $('tr:last-child').remove();
        }
        array.forEach(function (record){
            var name = record["name"];
            var score = record["score"];
            var row = '<tr>' +
                '<td>' + name + '</td>' +
                '<td>' + score + '</td>' +
                '</tr>';
    
            $('#table').append(row);
        });
    }
    let c = document.getElementById("canvas");
    let ctx = c.getContext("2d");
    ctx.beginPath();
    ctx.arc(250, 400, 10, 0, 2 * Math.PI, true);
    ctx.fill();
    let clicked = false;
    c.addEventListener('mousedown', function (e) {
        // Get the target
        const target = e.target;
    
        // Get the bounding rectangle of target
        const rect = target.getBoundingClientRect();
    
        // Mouse position
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;
        console.log(x,y);
        if (!(clicked)){
            if (240<x&&x<260 && 390<y&&y<410){
                let counter=0
                clicked = true;
                const id = setInterval(() => {
                    ctx.clearRect(0, 0, canvas.width, canvas.height);
                    ctx.beginPath();
                    ctx.arc(250, 400, 10+counter, 0, 2 * Math.PI, true);
                    ctx.fillStyle = "#30db72";
                    ctx.fill(); 
                    ctx.beginPath();
                    ctx.arc(250, 400, 10-counter, 0, 2 * Math.PI, true);
                    ctx.fillStyle = "#000000";
                    ctx.fill();
                    counter+=1;
                    if (counter==9){
                        clearInterval(id);
                    }
                  }, 10);
            }
        } else {
            let counter = 9
            clicked = false;
            const id = setInterval(() => {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                ctx.beginPath();
                ctx.arc(250, 400, 10+counter, 0, 2 * Math.PI, true);
                ctx.fillStyle = "#30db72";
                ctx.fill(); 
                ctx.beginPath();
                ctx.arc(250, 400, 10-counter, 0, 2 * Math.PI, true);
                ctx.fillStyle = "#000000";
                ctx.fill();
                counter-=1;
                if (counter==-1){
                    clearInterval(id);
                }
              }, 10);
        }
    });
</script>