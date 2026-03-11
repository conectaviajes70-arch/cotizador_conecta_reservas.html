<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">

<title>Cotizador Conecta Reservas</title>

<style>

body{
background:#0b0b0b;
font-family:Arial;
color:white;
margin:0;
padding:20px;
}

.container{
max-width:600px;
margin:auto;
background:#111;
padding:25px;
border-radius:16px;
box-shadow:0 0 20px rgba(0,0,0,0.6);
}

h2{
color:#4cc3ff;
margin-top:20px;
}

input,select{
width:100%;
padding:12px;
margin-top:6px;
border-radius:10px;
border:none;
background:#1e1e1e;
color:white;
font-size:15px;
}

.row{
display:flex;
gap:10px;
}

button{
width:100%;
padding:14px;
margin-top:10px;
border:none;
border-radius:12px;
font-size:16px;
cursor:pointer;
}

.btn-blue{background:#2d8bd6;color:white;}
.btn-green{background:#1ecf73;color:black;}
.btn-gray{background:#444;color:white;}

.result{
margin-top:15px;
font-size:20px;
color:#40e0ff;
text-align:center;
}

</style>

</head>

<body>

<div class="container">

<h2>CONECTA • Reservas</h2>

<label>API Key Google Maps</label>
<input id="apiKey" placeholder="Pega tu API Key">

<h2>Datos del cliente</h2>

<label>Nombre del cliente</label>
<input id="cliente">

<label>Número de pasajeros</label>
<input type="number" id="pasajeros" value="1">

<label>Tipo de servicio</label>

<select id="servicio">
<option>Conecta Go</option>
<option>Reserva</option>
<option>Larga distancia</option>
</select>

<h2>Direcciones</h2>

<label>Origen</label>
<input id="origen">

<label>Destino</label>
<input id="destino">

<label>Parada intermedia (opcional)</label>
<input id="parada">

<h2>Fecha y hora</h2>

<div class="row">
<input type="date" id="fecha">
<input type="time" id="hora">
</div>

<h2>Tarifas y umbrales</h2>

<div class="row">

<div>
<label>Tarifa base</label>
<input id="base" value="40">
</div>

<div>
<label>$ por km</label>
<input id="km" value="8">
</div>

</div>

<div class="row">

<div>
<label>$ por minuto</label>
<input id="minuto" value="2">
</div>

<div>
<label>Velocidad estimada km/h</label>
<input id="velocidad" value="28">
</div>

</div>

<label>
<input type="checkbox" id="trafico" checked>
Tráfico escalonado
</label>

<h2>Calcular</h2>

<button class="btn-blue" onclick="calcular()">Calcular</button>

<button class="btn-gray" onclick="prueba()">Prueba CDMX → Toluca</button>

<button class="btn-green" onclick="abrirWaze()">Abrir en Waze</button>

<button class="btn-gray" onclick="limpiar()">Limpiar</button>

<button class="btn-green" onclick="enviarWhats()">Enviar cotización por WhatsApp</button>

<button class="btn-blue" onclick="guardarReserva()">Guardar reserva</button>

<div class="result" id="resultado">

Total estimado: $0 MXN

</div>

</div>

<script>

let distanciaKM=0
let tiempoMin=0
let total=0

async function calcular(){

let origen=document.getElementById("origen").value
let destino=document.getElementById("destino").value

let api=document.getElementById("apiKey").value

let url=`https://maps.googleapis.com/maps/api/distancematrix/json?origins=${origen}&destinations=${destino}&key=${api}`

try{

let res=await fetch(url)
let data=await res.json()

distanciaKM=data.rows[0].elements[0].distance.value/1000
tiempoMin=data.rows[0].elements[0].duration.value/60

let base=parseFloat(document.getElementById("base").value)
let tarifaKM=parseFloat(document.getElementById("km").value)
let tarifaMin=parseFloat(document.getElementById("minuto").value)

let trafico=document.getElementById("trafico").checked

total=base+(distanciaKM*tarifaKM)+(tiempoMin*tarifaMin)

if(trafico){

if(tiempoMin/distanciaKM>2) total*=1.25
}

total=Math.round(total)

document.getElementById("resultado").innerHTML=

"Distancia: "+distanciaKM.toFixed(1)+" km<br>"+
"Tiempo estimado: "+Math.round(tiempoMin)+" min<br>"+
"Total estimado: $"+total+" MXN"

}

catch{

alert("Error con la API")

}

}

function prueba(){

document.getElementById("origen").value="Ciudad de México"
document.getElementById("destino").value="Toluca"

}

function abrirWaze(){

let destino=document.getElementById("destino").value

let url="https://waze.com/ul?q="+encodeURIComponent(destino)

window.open(url)

}

function limpiar(){

document.querySelectorAll("input").forEach(e=>e.value="")

document.getElementById("resultado").innerHTML="Total estimado: $0 MXN"

}

function enviarWhats(){

let cliente=document.getElementById("cliente").value
let origen=document.getElementById("origen").value
let destino=document.getElementById("destino").value

let msg=

"Cotización Conecta%0A"+
"Cliente:"+cliente+"%0A"+
"Origen:"+origen+"%0A"+
"Destino:"+destino+"%0A"+
"Total:$"+total+" MXN"

let link="https://wa.me/5619508426?text="+msg

window.open(link)

}

function guardarReserva(){

let reserva={

cliente:document.getElementById("cliente").value,
origen:document.getElementById("origen").value,
destino:document.getElementById("destino").value,
fecha:document.getElementById("fecha").value,
hora:document.getElementById("hora").value,
total:total

}

localStorage.setItem("reservaConecta",JSON.stringify(reserva))

alert("Reserva guardada")

}

</script>

</body>
</html>
