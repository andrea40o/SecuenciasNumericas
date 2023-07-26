<script setup>
import { ref, onMounted,computed,watch } from 'vue';
import flecha3 from '@/imagenes/flecha3.png';
import checkImage from '@/imagenes/Check.png';
import timeImage from '@/imagenes/Time.png';

//Constantes Secuencia
const secuenciaR = ref([]);

const secuenciaInicial = ref([]);

const numeroFaltante=ref(null);

const indiceCuadroBlanco=ref(null);

const draggedNumber=ref(null);

const resultadoEvaluacion = ref({});

const tiempoRestante = ref(10);

const botones = ref([]);

const mostrarInicio = ref(true);

const secuenciaCorrecta = ref();
  
const mostrarTemporizador = ref(false);
   
const mostrarResponder = ref(false);
  
let temporizadorInterval; 

const isDragging = ref(false);

const secuenciasCorrectas = ref(0);

const mostrarCheck = ref(false);

const mostrarError = ref(false);

const mostrarBotones = ref(true);

const mostrarBotonContinuar = ref(false);

const indiceFlechaEspecial = ref(0); 

const totalSecuencias = 5; // Número total de secuencias para el juego
const secuenciasCompletadas = ref(0);
const porcentajeUsuario = ref(0);
const mostrarCalificacion = ref(false);

//Secuencia
const generarSecuencia = () => {
const secuencia = [];

const inicio = Math.floor(Math.random() * 9) + 1;

for (let i = 0; i < 7; i++) {
   secuencia.push(inicio + i);
}

secuenciaInicial.value=secuencia;

const indiceOmitido = Math.floor(Math.random() * 7);

numeroFaltante.value = secuencia[indiceOmitido]; 

secuencia[indiceOmitido] = null;

secuenciaR.value = secuencia;

indiceCuadroBlanco.value = secuencia.indexOf(null);
indiceFlechaEspecial.value=secuencia.indexOf(null);
};

// Botones izquierdo, omitido, derecho
const generarBotones = () => {
botones.value = [];
if (indiceCuadroBlanco.value > 0) {
    botones.value.push({
    numero: numeroIzquierda.value,
    draggable: true,
    });
  }
  botones.value.push({
  numero: numeroFaltante1.value,
  draggable: true,
  });

  if (indiceCuadroBlanco.value < secuenciaR.value.length - 1) {
    botones.value.push({
    numero: numeroDerecha.value,
    draggable: true,
    });
  }
};

// Llamar generar botones

const indiceCuadroBlancoReactive = computed(() => indiceCuadroBlanco.value);

const secuenciaRReactive = computed(() => secuenciaR.value);

onMounted(generarBotones);
onMounted(() => {
  watch([indiceCuadroBlancoReactive, secuenciaRReactive], generarBotones);
});

watch([indiceCuadroBlanco, secuenciaR], () => {
  generarBotones();
});

onMounted(generarSecuencia);

//Eventos arrastrables computadoras 
const ArrastrarNumeroInicio = (num) => {
isDragging.value = true;
draggedNumber.value = num;
};

const ArrastrableSoltar = (event) => {
event.preventDefault();
const indexCuadroBlanco = secuenciaR.value.indexOf(null);
secuenciaR.value[indexCuadroBlanco] = draggedNumber.value;
clearInterval(temporizadorInterval);
mostrarResponder.value = true;
isDragging.value=false;
};

const ArrastrableDetectar = (event) => {
   isDragging.value = false;
  event.preventDefault();
};

//Eventos arrastrables en telefono

const TouchInicio = (num) => {
  draggedNumber.value=num;
};

const TouchMover = (event) => {
  event.preventDefault();
  if (draggedNumber.value !== null) {
    const container = document.querySelector('.secuenciaR');
    const boundingRect = container.getBoundingClientRect();
    const touchX = event.touches[0].clientX - boundingRect.left;

    const newIndex = Math.max(0, Math.min(Math.floor(touchX / 100), secuenciaR.value.length - 1));

    if (newIndex !== indiceCuadroBlanco.value) {
      secuenciaR.value[indiceCuadroBlanco.value] = draggedNumber.value;
    }
     console.log(draggedNumber.value);
  }
};

const TouchFinalizar = (event) => {
  event.preventDefault();
  ArrastrableSoltar(event); 
};


const numeroDerecha = computed(() => secuenciaR.value[indiceCuadroBlanco.value + 1]);
const numeroFaltante1 = computed(() => numeroFaltante.value);
const numeroIzquierda = computed(() => secuenciaR.value[indiceCuadroBlanco.value - 1]);

const evaluarSecuencia = () => {
const correcta = secuenciaR.value.every((numero, elemento) => numero === secuenciaInicial.value[elemento]);

const correcto = numeroFaltante.value === secuenciaInicial.value[indiceCuadroBlanco.value];
resultadoEvaluacion.value = { correcta, correcto };

     console.log("CORRECTA"+correcta);
     console.log("CORRECTO"+correcto);
    if (correcta && correcto) {

    secuenciaCorrecta.value = correcta && correcto;
    secuenciasCorrectas.value++;
  }else{

     if (correcto!=true) {
      
      secuenciaCorrecta.value=correcto;
      console.log("correctisimo"+secuenciaCorrecta.value);
     }
  }
};

const generarNuevaSecuencia = () => {
  generarSecuencia();
};

//Reinicio de variables en el juego
const ocultarInicio = () => {
  secuenciaR.value = [];
  numeroFaltante.value = null;
  indiceCuadroBlanco.value = null;
  draggedNumber.value = null;
  resultadoEvaluacion.value = {};
  tiempoRestante.value = 10;
  mostrarInicio.value = true;
  secuenciaCorrecta.value = null;
  mostrarTemporizador.value = false;
  mostrarResponder.value = false;
  isDragging.value = false;
  secuenciasCorrectas.value = 0;
  mostrarCheck.value = false;
  mostrarError.value = false;
  mostrarBotones.value = true;
  mostrarBotonContinuar.value = false;
  indiceFlechaEspecial.value = 0;
  secuenciasCompletadas.value = 0;
  porcentajeUsuario.value = 0;
  mostrarCalificacion.value = false;
  generarNuevaSecuencia();
  mostrarInicio.value = false;
  mostrarTemporizador.value = true; 
   iniciarTemporizador();
};

const iniciarTemporizador = () => {
let tiempoRestanteValue = tiempoRestante.value;

temporizadorInterval = setInterval(() => {
tiempoRestanteValue--;

    if (tiempoRestanteValue <= 0) {
      clearInterval(temporizadorInterval); // Detener el intervalo
      mostrarTemporizador.value = false;
      mostrarBotones.value = false;
      mostrarResponder.value = false;
      mostrarBotonContinuar.value = true;
      mostrarError.value = true;
      evaluarSecuencia();
    }

    tiempoRestante.value = tiempoRestanteValue;
  }, 1000); // Intervalo de actualización de 1 segundo (1000 ms).
};
const responderSecuencia = () => {
    mostrarBotones.value=false;
    mostrarCheck.value = true
    mostrarError.value=true;
    mostrarResponder.value = false;
    mostrarBotonContinuar.value = true;
    mostrarCalificacion.value=false;
    mostrarTemporizador.value = false;

  if (secuenciaCorrecta==true) {
    console.log("Correcto");
  }else{
    console.log("Incorrecto");
  }
  evaluarSecuencia();
};

const generarDesempeno= () => {
    mostrarBotones.value=true;
    mostrarError.value=false;
    secuenciasCompletadas.value++;

    console.log("SEEE"+secuenciasCompletadas.value);

    porcentajeUsuario.value = (secuenciasCorrectas.value / secuenciasCompletadas.value) * 100;

    mostrarCalificacion.value = true;
    mostrarBotonContinuar.value=false;
    mostrarResponder.value = false;

    generarNuevaSecuencia();

   tiempoRestante.value = 10;

  if (secuenciasCompletadas.value < totalSecuencias) {
      generarNuevaSecuencia();

  } else {

    mostrarPorcentajeFinal();
    mostrarInicio.value = true;
  }
};

const continuarJuego = () => {

  mostrarTemporizador.value = true

  iniciarTemporizador();

  mostrarBotonContinuar.value=false;

  mostrarCheck.value = false

  mostrarCalificacion.value = false;

  if (secuenciasCompletadas.value < totalSecuencias) {
    generarNuevaSecuencia();
  } else {
    mostrarPorcentajeFinal();
  }
};

//Consola en web
const mostrarPorcentajeFinal = () => {
  const porcentajeFinal = (secuenciasCorrectas.value / totalSecuencias) * 100;
  alert(`Juego completado!\nPorcentaje de aciertos: ${porcentajeFinal.toFixed(2)}%`);
};

//Animación comienzo de juego
const startAnimation = (event) => {
  event.target.classList.add('active');
  setTimeout(() => {
    event.target.classList.remove('active');
  }, 200);
};


</script>

<template>
  <div v-if="mostrarInicio">
    <div class="center-container">
      <button class="start-button" @click="ocultarInicio" @touchstart="startAnimation" @mouseover="startAnimation">Comenzar Juego</button>
    </div>
  </div>
  <div v-else>
    <div class="secuenciaR-container">
      <div class="container">
        <div v-if="!mostrarCalificacion">
          <div class="secuencia-text">Completa la Secuencia</div>
          <div v-if="secuenciaR.length" class="secuenciaR">
            <div v-for="(num, index) in secuenciaR" :draggable="true" :key="index"  
              :class="['mi-elemento', {
                'cuadro-vacio': num === null,
                'con-check': secuenciaCorrecta && index === indiceCuadroBlanco,
                'con-error': !secuenciaCorrecta && index === indiceCuadroBlanco
              }]"
              @touchmove="TouchMover" @touchend="TouchFinalizar" @drop="ArrastrableSoltar" @dragover="ArrastrableDetectar"
            >
              <div v-if="secuenciaCorrecta && index === indiceCuadroBlanco && mostrarCheck">
                <!-- Imagen para la respuesta correcta -->
                <img class="respuesta-correcta" :src="checkImage" alt="Respuesta correcta" />
              </div>
              <div v-else-if="!secuenciaCorrecta && index === indiceCuadroBlanco && mostrarError">
                <!-- Agregar aquí la imagen para la respuesta incorrecta -->
                <h1 class="respuesta-incorrecta">La respuesta correcta es {{numeroFaltante1 }}</h1>
              </div>
              <img :src="flecha3" alt="Flecha" :class="index === indiceFlechaEspecial && num === null ? 'flecha-especial' : 'flecha'" />
              <div>{{ num }}</div>
            </div>
          </div>
          <!-- Mostrar los botones debajo del cuadro vacío -->
          <div v-if="mostrarBotones">
            <div class="containerPrueba" :key="indiceCuadroBlanco">
              <div v-for="(boton, index) in botones" :key="index" class="item">
                <button v-if="boton.draggable" class="number-box2" :draggable="true" @dragstart="ArrastrarNumeroInicio(boton.numero)" @touchstart="TouchInicio(boton.numero)" @touchmove="TouchMover" @touchend="TouchFinalizar">
                  {{ boton.numero }}
                </button>
              </div>
            </div>
          </div>
          <!-- Mostrar el temporizador cuando mostrarTemporizador es true -->
          <div v-bind:style="{ visibility: mostrarTemporizador ? 'visible' : 'hidden' }" class="time-container">
            <div class="time-text">Tiempo restante: {{ tiempoRestante }} segundos</div>
            <div class="image-check"></div>
          </div>
          <div class="time-image">
            <img :src="timeImage" alt="Ejemplo de imagen">
          </div>
          <div v-if="secuenciaCorrecta==true" class="time-container">
          </div>
          <div v-else="secuenciaCorrecta==false"></div>

           <div v-if="mostrarResponder" class="container-responder">
          <button class="request-button2" @click="responderSecuencia">RESPONDER</button>
        </div>

                <!-- Mostrar el botón "Continuar" si mostrarBotonContinuar es true -->
        <div class="container-responder">
         <button v-if="mostrarBotonContinuar" class="request-button2" @click="generarDesempeno">CONTINUAR</button>
        </div>
        </div>
        <!-- Mostrar el botón de responder solo si no se ha mostrado el desempeño -->
        <div v-if="mostrarCalificacion" class="desempeno-container">
          <div class="desempeno-text">DESEMPEÑO</div>
          <div class="desempeno-value">{{ porcentajeUsuario.toFixed(2) }}%</div>
          <button class="continue-button" @click="continuarJuego">CONTINUAR</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
body {
  font-family: 'Montserrat', sans-serif;
}
.secuenciaR-container {
  display: flex;
  justify-content: center; 
  align-items: center; 
  height: 100vh; 
}

.secuenciaR {
  display: flex;
  flex-wrap: wrap;
  /*gap: 10px;*/
  max-width: 70vw;
  justify-content: center; 
  align-items: center; 
  color: #2B4457;
}

.time-image {
    top: 0;
    z-index: unset;
    position: fixed;
    right: 1200px;
    height: 20px;
}

.container-button {
  position: absolute;
  right: 10px; 
  display: flex;
  justify-content: center; 
  margin-right: 570px;
}

.container-responder {
  display: flex;
  justify-content: center;
  align-items: center;
}

.respuesta-correcta-container {
  position: relative;
}
.respuesta-correcta {
    position: absolute;
    bottom: 0;
    left: 58%;
    transform: translateX(-50%);
    top: 120%;
    transform: translateY(-50%);
}

.respuesta-incorrecta {
    position: absolute;
    bottom: 0;
    left: 50%;
    transform: translateX(-50%);
    top: 120%;
    transform: translateY(-50%);
    color: #B60006;
    width: 168.72px;
    height: 48.46px;
    font-size: 24px;
    transform: translate(-25%, -50%);
}

.con-check {
  position: relative;
}
.con-error {
  position: relative; 
}
.number-box {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 5rem;
  height: 5rem;
  border: 0px solid #ccc;
  font-size: 32px;
}


*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  font-weight: bold; 
}
.number-box2{
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 80px; 
  height: 80px; 
  border: 1px solid #ccc;
  font-size: 10px; 
  font-weight: bold;
  
}

.flecha-y-numero {
  display: flex;
  align-items: center; 
}

.flecha-especial {
  margin-right:160px;

}
.flecha {
  width: 38.61px;
  height: 22.35px;
  margin-right: 5px; 
}
 .start-button {
    padding: 20px 30px;
    font-size: 40px;
    font-weight: bold;
    text-transform: uppercase;
    border: none;
    border-radius: 4px;
    color: white;
    background-color: blue;
    cursor: pointer;
    transition: background-color 0.2s ease;
  }

.request-button {
    width: 174.5px;
    height: 56px;
    border-radius: 5px;
    background-color: #495259;
    color: #FFFFFF;
    font-weight: bold;
    border: 0px solid black;
    margin-top: -80px;
}

.request-button2 {
    width: 174.5px;
    height: 56px;
    border-radius: 5px;
    background-color: #495259;
    color: #FFFFFF;
    font-weight: bold;
    border: 0px solid black;
    margin-top: -80px;
}
.cuadro {
  display: inline-block;
  width: 40px;
  height: 40px;
  border: 1px solid black;
  text-align: center;
  line-height: 40px;
  margin: 5px;
}

.mi-elemento {
   display: flex;
  font-family: Montserrat, sans-serif;
  font-size: 90px;
  font-weight: 600;
  margin-left: 15px;
  line-height: 117px;
  letter-spacing: 0em;
  text-align: center;
  align-items: center;
  gap: 15px;
  position: relative;
  margin-top: 50px;
}

.resultados {
  margin-top: 20px;
  text-align: center;
}

.time-container {
  display: flex;
  justify-content: center;
  margin-top: 20px;
  margin-left: 60px;
}

.time-text {
  font-size: 24px;
  font-weight: bold;
  color: red; 
  margin-right: 10px;
  flex-direction: column;
  padding-bottom: 80px;
  text-align:center;
  margin-left: 15px;
}

.time-value {
  font-size: 24px;
  font-weight: bold;
  color: #ff4500; 
}
body .container {
 position: absolute;
}

.container{
  margin-left: 0; 
  display:flex;
  flex-direction:row;
  flex-wrap:wrap;
  justify-content:center;
  align-items:center;
  align-content:center;
  height:100vh;
   margin-left: 0;
   margin: 20px;
}

.containerPrueba{
  display: flex;
  color: red;
  justify-content: center;
  margin: 50px;
}
.cuadro-vacio {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 80px;
  height: 80px;
  border-radius: 20px;
  border: 2px dashed #ccc;
  font-size: 14px;
  font-weight: bold;
  margin-left: 90px;
  margin-right: 10px;
}

.item-0{
  order:999;
  flex:0 1 auto;
  align-self:auto;
  height:auto;
  width:auto;
}
      
.item-1{
   order: 999;
  flex:0 1 auto;
  align-self:auto;
  height:auto;
  width:auto;
  
}
      
.item-2{
    order: 999;
  flex:0 1 auto;
  align-self:auto;
  height:auto;
  width:auto;
}

.desempeno-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.desempeno-text{
  width: 212px;
  height: 30px;
  font-size: 30px;
  color: #6A7680;
  display: flex;
  padding-bottom: 50px;
  font-weight: bold;
  padding-left: 20px;
}

.desempeno-value {
  font-size: 96px;
  font-weight: bold;
  display: flex;
  justify-content: center; 
  padding-left: 50px;
}

.secuencia-text{
  font-size: 36px;
  text-align: center;
  padding: 30px;
  position: relative;
  font-family: 'Montserrat', sans-serif;
  font-weight: bold;

}
.continue-button {
    display: inline-block;
    width: 255px;
    height: 76px;
    border: 0px solid black;
    text-align: center;
    line-height: 40px;
    margin: 5px auto; 
    background-color: #ED2A2F;
    border-radius: 40px;
    font-size: x-large;
    color: #FFFFFF;
    letter-spacing: 2.4px;
    font-weight: bold;
}
@media screen and (min-width: 1200px) {
  .container {
    max-width: 1140px;
    margin-left: 250px;
    flex-direction: column;
  }
    .green-outline {
    border: 2px solid #52E1C9;
  }

   #app {
    display: block;
  }
}

@media screen and (max-width: 992px) {
  .container {
    flex-direction: column;
  }
    .green-outline {
    border: 2px solid #52E1C9;
  }

}

@media screen and (max-width: 768px) {
  .container {
    padding: 20px;
  }
  
  .start-button{
  padding: 20px 30px;
  font-size: 25px;
  position: relative;
  top: -60px;
  }

  .containerPrueba{
  margin: 10px;
}

.desempeno-value {
    font-size: 80px;
    padding-left: 20px;
}
.respuesta-incorrecta {
  font-size: 18px;
  left: 30%;
}
  .respuesta-correcta{
    width: 30px;
    height: 30px;	
	  left: 58%;
    transform: translateY(-100%);
    left: 64%;
  }
  .number-box2 {
    font-size: 14px;
    width: 40px; 
    height: 40px; 
  }

  .mi-elemento {
    font-size: 33px; 
    line-height: 70px; 
  }

  .time-image {
    top: 0;
    z-index: unset;
    position: fixed;
    right: 200px;
}

 .cuadro-vacio {
    width: 45px;
    height: 45px;
    font-size: 12px;
    margin-left: 60px;
    border-radius: 10px;
}
  .flecha {
    width: 19.3px; 
    height: 11.175px; 
    margin-right: 2.5px; 
  }

  .flecha-especial{
     width: 19.3px; 
    height: 11.175px; 
     margin-left: 50px;
  }
.containerPrueba {
  display: flex;
  color: red;
}

.item-0 {
  order: 0;
}

.item-1 {
  order: calc(var(--indice-cuadro-vacio) + 1);
}

.item-2 {
  order: 999;
}

.green-bg {
  background-color: green;
}
  .green-outline {
    border: 2px solid #52E1C9;
  }
.secuencia-text{
  font-size: 29px;
  margin-top: -60px;
}

.time-container{
  margin-left: 10px;
}
.secuenciaR{
  max-width: 100vw;
  padding-bottom: 30px;
  margin-right: 30px;
}
}
.green-outline {
  border: 2px solid #52E1C9;
}

.secuencia-incorrecta {
  color: red;
}
  .start-button:active,
  .start-button:focus,
  .start-button:hover {
    background-color:lightskyblue;
    transition-delay: 0.2s; 
  }

    .center-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  .center-container2 {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>