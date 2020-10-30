<template>
 <div>
  <Navbar/>
  <Convert 
    v-on:currency-1="setCurrency1" 
    v-on:currency-2="setCurrency2"
    v-on:show-graph="showGraph"/>

  <!-- load graph if showGraph() returns true -->
  <b-card 
    v-if="loaded"
    bg-variant="light" 
    text-variant="dark" 
    title="Conversion rates history(1 week)">
    <b-container class="bv-example-row">
      <b-row>
        <b-col  md="2" sm="0"></b-col>
        <b-col  md="8" sm="12" >
          <Chart v-bind:currency="currency" :key="componentKey"/>
          <br/>
        </b-col>
      </b-row>
    </b-container>
  </b-card>
 </div>
</template>


<script>
import Navbar from './components/Navbar';
import Convert from './views/Convert';
import Chart from './views/Chart';

export default {
  name: 'App',
  components:{
    Navbar,
    Convert,
    Chart
  },
  data(){
    return {
      currency:[],
      loaded:false,
      componentKey: 0,
    }
  },
  methods:{
    setCurrency1(name){
      this.currency[0] = name 
    },
    setCurrency2(name){
      this.currency[1] = name
    },
    showGraph(){
      //check if both the currencies are added
      if(this.currency.length==2 && 
      this.currency[0]!=this.currency[1] && 
      this.currency[0].length>0 && this.currency[1].length>0)
        this.loaded = true
      //reload the graph
      this.componentKey+=1
    }
  }
}
</script>

<style>
.card {
  padding-top:1%;
  text-align: center;
}
.btn{
  display: flex;
  align-items: center;
  flex-direction: column;
}
</style>
