<template>
  <v-layout>
    <v-flex xs12>
      <v-card>
        <v-date-picker full-width :min="minimo" :max="maximo" @change="getDollar(fecha)" locale="es-co" v-model="fecha"></v-date-picker>
      </v-card>
      <v-card color="error" dark>
        <v-card-text class="display-1 text-md-center">
          {{valor}}
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import axios from 'axios'
import { mapMutations } from 'vuex'

export default {
  name: 'Home',
  data(){
    return {
      fecha: new Date().toISOString().substr(0, 10),
      minimo: '1984',
      maximo: new Date().toISOString().substr(0, 10),
      valor: null
    }
  },
  methods: {
    ...mapMutations(['mostrarLoading', 'ocultarLoading']),
    async getDollar(dia){
      const fecha = dia.split(['-']).reverse().join(['-'])
      try {
        this.mostrarLoading({titulo: 'Accediendo informacion', color: 'secondary'})
        const datos = await axios(`https://mindicador.cl/api/dolar/${fecha}`)
        if(datos.data.serie.length > 0){
          this.valor = await datos.data.serie[0].valor
        } else {
          this.valor = 'Sin resultados'
        }
      } catch (error) {
        console.log(error);
      } finally {
        this.ocultarLoading()
      }
    }
  },
  created(){
    this.getDollar(this.fecha)
  }
}
</script>
