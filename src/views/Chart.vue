<script>
import { Line } from "vue-chartjs";
import axios from 'axios';

export default {
  extends: Line,
  props:["currency"],
  data() {
    return {
      gradient: null,
      dates:[],
      points:[]
    };
  },

    mounted() {
        //get today's date and date date one week prior
        var temp2 = new Date();
        var temp1 = temp2.getDate() - 7;
        temp2.setDate(temp1);
        var d1 = temp2.toISOString().slice(0,10)
        var d2 = new Date().toISOString().slice(0,10)


        //api call to get the converted currency
        axios.get('https://free.currconv.com/api/v7/convert?q='+
        this.currency[0]+"_"+this.currency[1]+
        '&date='+d1+'&endDate='+d2+'&apiKey=[api-key]')
        .then(res=> {
            var temp =res.data.results[this.currency[0]+"_"+this.currency[1]].val

            //define chart label and coordinates
            Object.keys(temp).forEach(key=>{
                var x = new Date(key).toUTCString().slice(4,16)
                this.dates.push(x)
            })
            Object.values(temp).forEach(key=>{
                key = Math.round(key * 100) / 100
                this.points.push(key)
            })

            //define gradient of graph
            this.gradient = this.$refs.canvas
            .getContext("2d")
            .createLinearGradient(0, 0, 0, 450);

            this.gradient.addColorStop(0, "rgba(0, 231, 255, 0.9)");
            this.gradient.addColorStop(0.5, "rgba(0, 231, 255, 0.25)");
            this.gradient.addColorStop(1, "rgba(0, 231, 255, 0)");

            //render chart
            this.renderChart(
            {
                labels: this.dates,
                datasets: [
                {
                    label: this.currency[0]+" to "+this.currency[1],
                    borderColor: "#05CBE1",
                    pointBackgroundColor: "#1fcbde",
                    borderWidth: 1,
                    pointBorderColor: "#10a0b0",
                    backgroundColor: this.gradient,
                    data: this.points,
                }
                ],
            },
            { responsive: true, maintainAspectRatio: false }
            );
        })
        .catch(e=>console.log(e))
    },
};
</script>
