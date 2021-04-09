<template>
    <div class="relative w-full px-14 lg:px-20">
        <h1 class="pt-10 pb-4 text-base font-semibold text-gray-800 bg-white font-open">
            Resuelve ejercicios de Regresión Lineal Simple
        </h1>
        <hr>
        <div class="flex items-center align-middle pt-7">
            <p class="text-sm font-poppins text-gray">Ingresa la cantidad de filas: </p>
            <input type="number" @keyup.enter="crearMatriz" v-model="cFilas" class="font-poppins focus:outline-none focus:ring-2 focus:ring-blue-300 w-20 text-sm px-2 py-1.5 ml-2 border border-gray-400 rounded-md">
            <button @click="crearMatriz" class="w-auto px-4 py-2 ml-2 text-xs font-bold text-white bg-blue-500 border-none rounded-md font-poppins hover:bg-blue-400 hover:text-white focus:outline-none focus:ring-2 focus:ring-blue-300">Crear columnas</button>
            <button v-if="displayMatriz" @click="clear" class="w-auto px-4 py-2 ml-2 text-xs font-bold text-white bg-blue-500 border-none rounded-md font-poppins hover:bg-blue-400 hover:text-white focus:outline-none focus:ring-2 focus:ring-blue-300">Reset</button>
        </div>
        <div class="flex mb-20">
            <!-- procedimientos -->
            <div class="flex flex-col">
                <div v-if="displayMatriz && Number(cFilas) > 1" class="items-center py-5">
                    <p class="text-sm font-poppins">Llena las columnas de <strong>X</strong> y <strong>Y</strong></p>
                    <div class="justify-center align-middle">
                        <div class="grid grid-flow-col grid-cols-2 gap-3 mt-4 text-center">
                            <span class="ml-2"><strong> </strong></span>
                            <span class="mr-14"><strong>X</strong></span>
                            <span class="mr-8"><strong>Y</strong></span>
                        </div>
                    </div>
                    <div class="flex items-center justify-center align-middle width-auto"> 
                        <div :class="`mt-1 grid gap-3 grid-cols-1 grid-rows-${Number(cFilas)}`">
                            <div class="items-center px-2 py-1.5 m-1" v-for="a in Number(cFilas)" :key="a">
                                {{ a }}
                            </div>
                        </div>
                        <div :class="`mt-1 grid gap-3 grid-cols-2 grid-flow-col-dense grid-rows-${Number(cFilas)}`">
                            <div v-for="a in Number(cFilas)*2" :key="a" :id="'row'+(a-1)">
                                <input type="number" :id="a" class="text-gray rounded border-gray-400 m-1 border w-16 px-2 font-poppins text-sm py-1.5 focus:outline-none focus:ring-2 focus:ring-cm-green" v-model="columnas[a-1]">
                            </div>
                        </div>
                    </div>
                    <div class="flex items-center justify-center mt-4 align-middle width-auto">
                        <button v-if="displayMatriz && columnas.length == Number(cFilas)*2" @click="calcularCoefDeCorrelacion" class="justify-center w-auto px-4 py-2 ml-2 text-xs font-bold text-white bg-blue-500 border-none rounded-md font-poppins hover:bg-blue-400 hover:text-white focus:outline-none focus:ring-2 focus:ring-blue-300">Calcular (r)</button>
                    </div>
                </div>
            </div>
            <!-- tablas de resultados -->
            <div v-if="showResults"  class="flex flex-col items-center justify-center h-20 px-4 py-2 text-gray-600 align-middle bg-green-200 rounded shadow ml-14 mt-14">
                <span class="text-sm font-bold font-poppins">
                    Coeficiente de Correlacion (r): 
                </span>
                <span class="text-sm font-medium font-poppins">
                    {{ coefCorrelacion }}
                </span>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            cFilas: null,
            displayMatriz: false,
            showResults: false,
            columnas: [],
            coefCorrelacion: null,
        }
    },
    computed: {
        matriz() {
            let matriz = [];

            for (let i = 0; i < this.columnas.length; i += Number(this.cFilas)) {
                let column = this.columnas.slice(i, i + Number(this.cFilas));
                matriz.push(column)
            }

            return matriz;
        },
        sumatoriaX() {
            let sumatoriaX = 0;
            this.matriz[0].forEach((v) => {
                sumatoriaX += Number(v);
            })
            return sumatoriaX;
        },
        sumatoriaY(){
            let sumatoriaY = 0;
            this.matriz[1].forEach((v) => {
                sumatoriaY += Number(v);
            })
            return sumatoriaY;
        },
        sumatoriaXporY(){
            const array = this.arrayMultiplicacionXporY();
            let sumatoriaXporY = 0;
            array.forEach((v) => {
                sumatoriaXporY += v;
            })
            return sumatoriaXporY;
        },
        sumatoriaX2() {
            const array = this.calcularArrayXal2();
            let sumatoriaX2 = 0;
            array.forEach((v) => {
                sumatoriaX2 += v;
            })
            return sumatoriaX2;
        },
        sumatoriaY2(){
            const array = this.calcularArrayYal2();
            let sumatoriaY2 = 0;
            array.forEach((v) => {
                sumatoriaY2 += v;
            })
            return sumatoriaY2;
        }
    },
    methods: {
        crearMatriz() {
            // Inicializar
            this.validateCantFilas();
        },
        validateCantFilas() {
            if(Number(this.cFilas) <= 1 || this.cFilas == null || Number(this.cFilas > 12)) {
                return this.$swal('La cantidad de filas debe ser mayor a 1 y menor a 13')
            } else {
                return this.displayMatriz = true;
            }
        },
        clear() {
            this.displayMatriz = false;
            this.columnas = [];
            this.cFilas = null;
            this.allFieldsValid = false;
            this.showResults = false;
        },
        someFieldMissing() {
            this.columnas.forEach((v) => {
                if(v == 0) {
                    return true;
                }
            })
            return false;
        },
        calcularCoefDeCorrelacion() {
            // Hacer todas la operaciones para Calcular R y mostrar resultados
            console.log(this.matriz)
            console.log(Number(this.cFilas), this.sumatoriaX, this.sumatoriaY, this.sumatoriaY2, this.sumatoriaX2, this.sumatoriaXporY)
            this.coefCorrelacion = this.calcularCoefCorrelacion(Number(this.cFilas), this.sumatoriaX, this.sumatoriaY, this.sumatoriaY2, this.sumatoriaX2, this.sumatoriaXporY)
            console.log(this.coefCorrelacion)
            this.showResults = true;
        },
        arrayMultiplicacionXporY() {
            let array=[];
            for(let i = 0; i < Number(this.cFilas); i++) {
                array.push(Number(this.matriz[0][i]) * Number(this.matriz[1][i]));
            }
            return array;
        },
        calcularArrayXal2(){
            let array=[];
            for(let i = 0; i < Number(this.cFilas); i++) {
                array.push(Number(this.matriz[0][i]) * Number(this.matriz[0][i]));
            }
            return array;
        },
        calcularArrayYal2(){
            let array=[];
            for(let i = 0; i < Number(this.cFilas); i++) {
                array.push(Number(this.matriz[1][i]) * Number(this.matriz[1][i]));
            }
            return array;
        },
        calcularCoefCorrelacion(n, sumX, sumY, sumY2, sumX2, XporY) {
            const valor = (n*sumX2- sumX*sumX) * (n*sumY2-sumY*sumY)
            console.log('Valor: ', valor)
            const resultadoMatriz = Math.sqrt(valor)
            console.log('Resultado matriz: ', resultadoMatriz)
            const resultado = (n*XporY-sumX*sumY) / resultadoMatriz
            console.log("resultado", Number(resultado))
            return resultado;
        }
    }
}
</script>
