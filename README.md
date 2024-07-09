<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Separador de Times de Pelada</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        .container {
            display: flex;
            justify-content: space-around;
            margin-bottom: 20px;
        }
        .box {
            border: 1px solid #ccc;
            padding: 10px;
            width: 30%;
        }
        .box h2 {
            text-align: center;
        }
        .box textarea {
            width: 100%;
            height: 200px;
        }
        .button {
            text-align: center;
            margin-top: 20px;
        }
        .button button {
            padding: 10px 20px;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <h1>Separador de Times de Pelada</h1>
    <div class="container">
        <div class="box">
            <h2>1 Ponto (Mais Fraco)</h2>
            <textarea id="box1"></textarea>
        </div>
        <div class="box">
            <h2>2 Pontos (Médio)</h2>
            <textarea id="box2"></textarea>
        </div>
        <div class="box">
            <h2>3 Pontos (Mais Forte)</h2>
            <textarea id="box3"></textarea>
        </div>
    </div>
    <div class="button">
        <button onclick="generateTeams()">Gerar Times</button>
    </div>
    <div id="results"></div>

    <script>
        function generateTeams() {
            const box1 = document.getElementById('box1').value.split('\n').filter(name => name.trim() !== '');
            const box2 = document.getElementById('box2').value.split('\n').filter(name => name.trim() !== '');
            const box3 = document.getElementById('box3').value.split('\n').filter(name => name.trim() !== '');
            
            if (box1.length + box2.length + box3.length !== 21) {
                alert('Por favor, insira exatamente 21 nomes.');
                return;
            }

            const allPlayers = [...box1, ...box2, ...box3];
            const team1 = [], team2 = [], team3 = [];

            while (allPlayers.length) {
                [team1, team2, team3].forEach(team => {
                    if (allPlayers.length) {
                        const randomIndex = Math.floor(Math.random() * allPlayers.length);
                        team.push(allPlayers.splice(randomIndex, 1)[0]);
                    }
                });
            }

            document.getElementById('results').innerHTML = `
                <h2>Times Gerados</h2>
                <div><strong>Time 1:</strong> ${team1.join(', ')}</div>
                <div><strong>Time 2:</strong> ${team2.join(', ')}</div>
                <div><strong>Time 3:</strong> ${team3.join(', ')}</div>
            `;
        }
    </script>
</body>
</html>
