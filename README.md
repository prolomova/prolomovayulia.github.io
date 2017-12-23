<!DOCTYPE html>
<html>
<head>
    <style>
        .divCell {
            position: absolute;
            width: 10px;
            height: 10px;
            border-width: 1px;
            border-style: dotted;
            border-color: black;
            background: white;
        }

        #universe {
            position: relative;
            margin: 20px auto;
        }

        .buttons {
            width: 80%;
            margin: 0 auto;
        }
    </style>
    <script>
        var DEAD = 0,
            ALIVE = 1,
            CELL_X_COUNT = 60,
            CELL_Y_COUNT = 60,
            TIMEOUT = 100,
            interval;

        function createUniverse() {
            var universe = document.getElementById('universe');
            for (var i = 0; i < CELL_X_COUNT; i++) {
                for (var j = 0; j < CELL_Y_COUNT; j++) {
                    var element = document.createElement('div');

                    element.className = 'divCell';
                    element.style.left = (i * 11) + 'px';
                    element.style.top = (j * 11) + 'px';
                    element.id = "x" + i + "y" + j;
                    element.setAttribute("state", DEAD);
                    universe.appendChild(element);
                }
            }
        }

        function cellClick(event) {
            if (event.target.tagName == "DIV") {
                if (event.target.getAttribute("state") == DEAD) {
                    event.target.setAttribute("state", ALIVE);
                    event.target.style.background = "green";
                }
                else {
                    event.target.setAttribute("state", DEAD);
                    event.target.style.background = "white"
                }

            }
            event.stopPropagation();
        }

        function getCellState(x, y) {
            if (x < 0 || x > CELL_X_COUNT || y < 0 || y > CELL_Y_COUNT) {
                return null;
            }

            var cell = document.getElementById("x" + x + "y" + y);
            return cell.getAttribute("state");
        }

        function setCellState(x, y, deadOrAlive) {

            if (x < 0 || x > CELL_X_COUNT || y < 0 || y > CELL_Y_COUNT) {
                return;
            }

            var cell = document.getElementById("x" + x + "y" + y);
            cell.setAttribute("state", deadOrAlive);

            cell.style.background =
                deadOrAlive == DEAD ? "white" : "green";
        }

        function countNeighbors(x, y) {
            var count = 0;
            for (var j = Math.max(0, y - 1); j <= Math.min(y + 1, CELL_Y_COUNT - 1); j++)
                for (var i = Math.max(0, x - 1); i <= Math.min(x + 1, CELL_X_COUNT - 1); i++) {
                    if (j === y && i === x)
                        continue;
                    if (getCellState(i, j) == ALIVE)
                        count++;
                }
            return count;
        } 

        function updateField(newField) {
            for (var i = 0; i < CELL_Y_COUNT; i++)
                for (var j = 0; j < CELL_X_COUNT; j++)
                    setCellState(j, i, newField[i][j]);
        }

        function lifeStep() {
            var arr = [];
            var state = 0;
            for (var i = 0; i < CELL_Y_COUNT; i++) {
                arr[i] = [];
                for (var j = 0; j < CELL_X_COUNT; j++) {
                    var numberOfNeighbors = countNeighbors(j, i);
                    var state = getCellState(j, i);

                    if (numberOfNeighbors === 3) {
                        arr[i][j] = 1;
                    } else if (state == 1 && numberOfNeighbors === 2) {
                        arr[i][j] = 1;
                    }
                    else
                        arr[i][j] = 0;
                }
            }
            updateField(arr);
        }

        function startLife() {
            interval = setInterval(lifeStep, TIMEOUT);
        }

        function pauseLife() {
            clearInterval(interval);
        }

        function killLife() {
            pauseLife();
            for (var i = 0; i < CELL_X_COUNT; i++) {
                for (var j = 0; j < CELL_Y_COUNT; j++) {
                    setCellState(i, j, DEAD);
                }
            }
        }

    </script>
</head>
<body onload="createUniverse();" onclick="cellClick(event)">
    <div style="margin:10px auto; width:800px;">
        <h1>Conway's game of life</h1>
        <div state="0" class="buttons">
            <button onclick="startLife()">Start Life</button>
            <button onclick="pauseLife()">Pause Life</button>
            <button onclick="killLife()">Kill Life</button>
        </div>
        <div id='universe'></div>
    </div>
</body>
</html>
