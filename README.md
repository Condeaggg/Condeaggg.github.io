<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Contador de Días de Gimnasio</title>
    <style>
        body {
            font-family: 'Poppins', sans-serif;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background: linear-gradient(to right, #fbc2eb, #a6c1ee);
            margin: 0;
        }
        #calendario {
            width: 450px;
            padding: 25px;
            background: white;
            border-radius: 12px;
            box-shadow: 0px 4px 15px rgba(0, 0, 0, 0.15);
            text-align: center;
        }
        h2 {
            color: #1565c0;
            margin-bottom: 15px;
        }
        #contador {
            margin-bottom: 15px;
            font-size: 18px;
            font-weight: bold;
            color: #1565c0;
        }
        #historial-anual {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin-bottom: 20px;
        }
        .mes {
            cursor: pointer;
            padding: 10px;
            background: #64b5f6;
            border-radius: 8px;
            color: white;
            font-weight: 600;
            transition: background 0.3s, transform 0.2s;
        }
        .mes:hover, .mes.seleccionado {
            background: #0d47a1;
            transform: scale(1.05);
        }
        #historial-mes {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 8px;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
            width: 100%;
        }
        .dia {
            width: 55px;
            height: 55px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 6px;
            background-color: #e3f2fd;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
        }
        .dia:hover {
            background-color: #42a5f5;
            color: white;
        }
        .dia.registrado {
            background-color: #66bb6a !important;
            color: white;
        }
        .dia.doble-click {
            background-color: #1e88e5 !important;
            color: white;
        }
    </style>
</head>
<body>
    <div id="calendario">
        <h2>Calendario Anual</h2>
        <div id="historial-anual"></div>
        <h2>Calendario Mensual</h2>
        <div id="historial-mes"></div>
        <div id="contador">Días de gimnasio: 0 | Días de descanso: 0</div>
    </div>
    <script>
        let datosMeses = JSON.parse(localStorage.getItem("datosMeses")) || {};
        let mesSeleccionado = null;

        function guardarDatos() {
            localStorage.setItem("datosMeses", JSON.stringify(datosMeses));
        }
        
        function actualizarContador(mes) {
            let diasGimnasio = 0, diasDescanso = 0;
            for (let dia in datosMeses[mes].dias) {
                if (datosMeses[mes].dias[dia] === 1) diasGimnasio++;
                if (datosMeses[mes].dias[dia] === 2) diasDescanso++;
            }
            document.getElementById("contador").innerText = `Días de gimnasio: ${diasGimnasio} | Días de descanso: ${diasDescanso}`;
        }
        
        function cambiarEstadoDia(mes, dia, elemento) {
            if (!datosMeses[mes].dias) datosMeses[mes].dias = {};
            if (!datosMeses[mes].dias[dia]) {
                datosMeses[mes].dias[dia] = 1;
                elemento.classList.add("registrado");
            } else if (datosMeses[mes].dias[dia] === 1) {
                datosMeses[mes].dias[dia] = 2;
                elemento.classList.remove("registrado");
                elemento.classList.add("doble-click");
            } else {
                delete datosMeses[mes].dias[dia];
                elemento.classList.remove("doble-click");
            }
            guardarDatos();
            actualizarContador(mes);
        }
        
        function mostrarAnual() {
            let historialAnual = document.getElementById("historial-anual");
            historialAnual.innerHTML = "";
            for (let mes = 0; mes < 12; mes++) {
                let mesDiv = document.createElement("div");
                mesDiv.classList.add("mes");
                mesDiv.innerText = new Date(2025, mes, 1).toLocaleString('es', { month: 'long' });
                mesDiv.dataset.mes = mes;
                mesDiv.onclick = () => seleccionarMes(mesDiv, mes);
                historialAnual.appendChild(mesDiv);
            }
        }
        
        function seleccionarMes(elemento, mes) {
            if (mesSeleccionado) mesSeleccionado.classList.remove("seleccionado");
            mesSeleccionado = elemento;
            mesSeleccionado.classList.add("seleccionado");
            mostrarMes(mes);
        }
        
        function mostrarMes(mes) {
            let historialMes = document.getElementById("historial-mes");
            historialMes.innerHTML = "";
            datosMeses[mes] = datosMeses[mes] || { dias: {} };
            for (let i = 1; i <= 31; i++) {
                let diaDiv = document.createElement("div");
                diaDiv.classList.add("dia");
                diaDiv.innerText = i;
                if (datosMeses[mes].dias[i] === 1) diaDiv.classList.add("registrado");
                if (datosMeses[mes].dias[i] === 2) diaDiv.classList.add("doble-click");
                diaDiv.onclick = () => cambiarEstadoDia(mes, i, diaDiv);
                historialMes.appendChild(diaDiv);
            }
            actualizarContador(mes);
        }
        
        mostrarAnual();
    </script>
</body>
</html>

