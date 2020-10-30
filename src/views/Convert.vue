<template>
  <div>
    <b-card bg-variant="secondary" text-variant="light" title="Convert between different currencies">
      <b-card-text>
        Select the desired currency from the drop down menu, and enter the initial amount
        to get the converted amount!<br/><strong>Try now!</strong>
      </b-card-text>
      <br/><br/>
      <b-form inline class="forms">
        <b-form-select 
          v-model="fromCurrency" :options="options" 
          v-on:change="convertCurrency(0)">
        </b-form-select>
        <b-input
          id="inline-form-input-name"
          class="mb-2 mr-sm-2 mb-sm-0"
          placeholder="Amount"
          type ="number"
          v-model="fromAmount"
          v-on:change="convertCurrency(0)"
        ></b-input>
      </b-form><br/>

      <b-form inline class="forms">
        <b-form-select 
          v-model="toCurrency" :options="options" 
          v-on:change="convertCurrency(0)">
        </b-form-select>
        <b-input
          id="inline-form-input-name"
          class="mb-2 mr-sm-2 mb-sm-0"
          placeholder="Amount"
          type ="number"
          v-model="toAmount"
          v-on:change="convertCurrency(1)"
        ></b-input>
      </b-form><br/><br/>
      <b-container class="bv-example-row">
        <b-row>
          <b-col  md="4"></b-col>
          <b-col  md="4" >
            <b-button variant="outline-light"
              v-on:click="showGraph">
              Show graph</b-button>
          </b-col>
        </b-row>
      </b-container>
    </b-card>
  </div>
</template>

<script>
import axios from 'axios';
  export default {
    name: 'Convert',
    data(){
    return {
      currencies:{},
      options:[],
      fromCurrency:'',
      toCurrency:'',
      convertedCurrency:'',
      fromAmount:1,
      toAmount:1,
      convertedAmount:null
    }
  },
  created(){

    //api call to get name of currencies
    axios.get('https://free.currconv.com/api/v7/currencies?apiKey=[api-key]')
    .then(res=> {
      this.currencies = {...res.data.results}

      //define options
      Object.values(this.currencies).forEach(key=>{
        key.value = key.id
        if(key.currencySymbol)
          key.text = key.currencyName+"  "+ key.currencySymbol
        else 
          key.text = key.currencyName
        this.options.push(key);
      }
      )
      })
    .catch(e=>console.log(e))
    },

  methods:{

    //api call to convert the currency
    convertCurrency(id){
      if(this.toCurrency.length>=1 && this.fromCurrency.length>=1)
      {
        axios.get("https://free.currconv.com/api/v7/convert?q="+
        this.fromCurrency+"_"+this.toCurrency+","+
        this.toCurrency+"_"+this.fromCurrency+
        "&compact=ultra&apiKey=[api-key]")
        .then(res=>{

          //change converted amount based on value of input(0 or 1)
          if(id==0){
            this.convertedAmount = res.data[this.fromCurrency+"_"+this.toCurrency]*this.fromAmount
            this.convertedAmount=Math.round(this.convertedAmount * 100) / 100
            this.convertedCurrency = this.toCurrency
            this.toAmount = this.convertedAmount
          }
          else{
            this.convertedAmount = res.data[this.toCurrency+"_"+this.fromCurrency]*this.toAmount
            this.convertedAmount=Math.round(this.convertedAmount * 100) / 100
            this.convertedCurrency = this.fromCurrency
            this.fromAmount = this.convertedAmount
          }}
        )
        .catch(err=>console.log(err))
      }
      //emit the names of the two currencies
      this.$emit('currency-1',this.fromCurrency)
      this.$emit('currency-2',this.toCurrency)
    },

    //emit event to load the graph
    showGraph(){
      this.$emit('show-graph')
    }
  }
  }
</script>

<style scoped>
  .card{
    padding:2% 15%;
    text-align: center;
    display:flex;
    flex-direction:column;
    justify-content: center;
    align-items: center;
  }
  .forms{
    /* padding: auto 20%; */
    display: inline flex;
    align-items:center;
    justify-content: center;
  }
</style>



