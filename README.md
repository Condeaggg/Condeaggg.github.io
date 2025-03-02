<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Contador de Días de Gimnasio</title>
    <style>
        body {
            font-family: monospace;
            display: flex;
            justify-content: space-between;
            padding: 20px;
            background-color: #f5f5f5;
        }
        #calendario {
            width: 350px;
            padding: 20px;
        }
        #historial-anual {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
        }
        .mes {
            cursor: pointer;
            padding: 5px;
            background: lightgray;
            border-radius: 5px;
            width: 200px;
            text-align: center;
        }
        #historial-mes {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            max-width: 350px;
            gap: 5px;
            background: white;
            padding: 10px;
            border-radius: 10px;
            box-shadow: 0px 0px 5px gray;
        }
        .dia, .dia-header {
            width: 40px;
            height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 5px;
            background-color: lightgray;
            font-family: monospace;
            cursor: pointer;
        }
        .dia-header {
            font-weight: bold;
            background-color: transparent;
        }
        .dia.seleccionado {
            border: 2px solid darkblue;
        }
        .dia.registrado {
            background-color: limegreen !important;
        }
        #contador-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }
        #contador {
            font-size: 48px;
            margin: 10px 0;
        }
        .boton-contador {
            font-size: 72px;
            padding: 10px 20px;
            margin: 5px;
            cursor: pointer;
            border: none;
            background: none;
        }
        .botones {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
            position: absolute;
            right: 20px;
            top: 50%;
            transform: translateY(-50%);
        }
    </style>
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.5.1/dist/confetti.browser.min.js"></script>
</head>
<body>
    <div id="calendario">
        <h2>Calendario Anual</h2>
        <div id="historial-anual"></div>
        <h2>Calendario Mensual</h2>
        <div id="historial-mes"></div>
    </div>
    <div id="contador-container">
        <p id="contador">0</p>
    </div>
    <div class="botones">
        <button class="boton-contador" onclick="modificarRegistro('add')">🏋️</button>
        <button class="boton-contador" onclick="modificarRegistro('remove')">😴</button>
    </div>
    <script>
        let diaSeleccionado = null;
        let registros = JSON.parse(localStorage.getItem("registrosGym")) || {};
        
        function seleccionarDia(diaDiv, mes, dia) {
            document.querySelectorAll(".dia").forEach(d => d.classList.remove("seleccionado"));
            diaDiv.classList.add("seleccionado");
            diaSeleccionado = { diaDiv, mes, dia };
        }
        
        function modificarRegistro(accion) {
            if (!diaSeleccionado) return;
            let { diaDiv, mes, dia } = diaSeleccionado;
            
            if (accion === 'add') {
                diaDiv.classList.add("registrado");
                registros[`${mes}-${dia}`] = true;
                lanzarConfetti();
            } else if (accion === 'remove') {
                diaDiv.classList.remove("registrado");
                delete registros[`${mes}-${dia}`];
            }
            
            localStorage.setItem("registrosGym", JSON.stringify(registros));
            actualizarContador();
        }
        
        function actualizarContador() {
            let diasVerdes = Object.keys(registros).length;
            document.getElementById("contador").innerText = diasVerdes;
        }
        
        function lanzarConfetti() {
            for (let i = 0; i < 2; i++) {
                confetti({
                    particleCount: 400,
                    spread: 150,
                    startVelocity: 50,
                    origin: { y: 0, x: Math.random() },
                    scalar: 2
                });
            }
        }
        
        function mostrarAnual() {
            let historialAnual = document.getElementById("historial-anual");
            historialAnual.innerHTML = "";
            for (let mes = 0; mes < 12; mes++) {
                let mesDiv = document.createElement("div");
                mesDiv.classList.add("mes");
                mesDiv.innerText = new Date(2025, mes, 1).toLocaleString('es', { month: 'long' });
                mesDiv.onclick = () => mostrarMes(mes);
                historialAnual.appendChild(mesDiv);
            }
        }
        
        function mostrarMes(mes) {
            let historialMes = document.getElementById("historial-mes");
            historialMes.innerHTML = "";
            historialMes.style.display = "flex";
            
            let diasSemana = ["L", "M", "X", "J", "V", "S", "D"];
            diasSemana.forEach(dia => {
                let diaHeader = document.createElement("div");
                diaHeader.classList.add("dia-header");
                diaHeader.innerText = dia;
                historialMes.appendChild(diaHeader);
            });
            
            let primerDiaSemana = new Date(2025, mes, 1).getDay();
            let ajuste = primerDiaSemana === 0 ? 6 : primerDiaSemana - 1;
            for (let i = 0; i < ajuste; i++) {
                let espacioVacio = document.createElement("div");
                espacioVacio.classList.add("dia");
                espacioVacio.style.visibility = "hidden";
                historialMes.appendChild(espacioVacio);
            }
            
            let ultimoDia = new Date(2025, mes + 1, 0).getDate();
            for (let i = 1; i <= ultimoDia; i++) {
                let diaDiv = document.createElement("div");
                diaDiv.classList.add("dia");
                diaDiv.innerText = i;
                if (registros[`${mes}-${i}`]) diaDiv.classList.add("registrado");
                diaDiv.onclick = () => seleccionarDia(diaDiv, mes, i);
                historialMes.appendChild(diaDiv);
            }
        }
        
        mostrarAnual();
        mostrarMes(new Date().getMonth());
        actualizarContador();
    </script>
</body>
</html>
