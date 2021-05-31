<template>
  <div>
    <div class="p-5">
      <b-card-group deck class="row">
        <div class="col-sm-4">
          <b-card
            border-variant="primary"
            header="PAID CLAIMS"
            header-bg-variant="primary"
            header-text-variant="white"
            align="center"
          >
            <b-card-text>Counts: <strong>{{paidClaims.length}}</strong></b-card-text>
            <b-card-text>Amounts: <strong>{{getPaidClaims | showInEuro}}</strong></b-card-text>
          </b-card>  
        </div>
        <div class="col-sm-4">
          <b-card
            border-variant="primary"
            header="OPEN CLAIMS"
            header-bg-variant="primary"
            header-text-variant="white"
            align="center"
          >
            <b-card-text>Counts: <strong>{{openClaims.length}}</strong></b-card-text>
            <b-card-text>Amounts: <strong>{{getOpenClaims | showInEuro}}</strong></b-card-text>
          </b-card>  
        </div>
        <div class="col-sm-4">
          <b-card
            border-variant="primary"
            header="DELETED CLAIMS"
            header-bg-variant="primary"
            header-text-variant="white"
            align="center"
          >
            <b-card-text>Counts: <strong>{{deletedClaims.length}}</strong></b-card-text>
            <b-card-text>Amounts: <strong>{{getDeletedClaims | showInEuro}}</strong></b-card-text>
          </b-card>  
        </div>
      </b-card-group>
      <div class="pt-5">
        <mdb-container v-if="lineChartData.labels.length">
        <mdb-line-chart
          :data="lineChartData"
          :options="lineChartOptions"
          :width="600"
          :height="300"
        ></mdb-line-chart>
      </mdb-container>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import { mdbLineChart, mdbContainer } from "mdbvue";
import axios from 'axios';
export default {
    name: 'dashboard',
    components: {
      mdbLineChart,
      mdbContainer
    },
    mounted() {
      const receeve_user = localStorage.getItem('receeve_user');
      if(!receeve_user){
        this.$router.push({ name: "Login"});
      }
      this.getClaims();

    },
    computed: {
      getPaidClaims: function () {
        return this.paidClaims.reduce((a, b) => a + b, 0);
      },
      getOpenClaims: function () {
        return this.openClaims.reduce((a, b) => a + b, 0);
      },
      getDeletedClaims: function () {
        return this.deletedClaims.reduce((a, b) => a + b, 0);
      }
    },
    filters: {
      showInEuro: function (value) {
        return `€${value.toFixed(2)}`
      }
    },
    methods:{
      getClaims: function (){
        axios.get("http://localhost:9001/claims")
        .then( (response) => {
          response.data.sort(function(a,b){
            return new Date(a.dueDate) - new Date(b.dueDate);
          });
          const feesAmount = {};
          const monthWise = response.data.reduce((acc, cur) => {
            const dueDate = new Date(cur.dueDate);
            const monthNames = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul","Aug", "Sep", "Oct", "Nov", "Dec"];
            const indexKey = `${monthNames[dueDate.getMonth()]} ${dueDate.getFullYear()}`
            acc[indexKey] = acc[indexKey] + parseInt(cur.baseAmount/100) || parseInt(cur.baseAmount/100);
            feesAmount[indexKey] = feesAmount[indexKey] + parseInt(cur.fees/100) || parseInt(cur.fees/100);
            return acc;
          }, {});
          this.lineChartData.labels = Object.keys(monthWise);
          this.lineChartData.datasets[0].data = Object.values(monthWise);
          this.lineChartData.datasets[1].data = Object.values(feesAmount);

          response.data.map((elem)=>{
            if(elem.status === 'OPEN'){
              this.openClaims.push(elem.baseAmount/100);
            }else if(elem.status === 'PAID'){
              this.paidClaims.push(elem.baseAmount/100);
            }else if(elem.status === 'DELETED'){
              this.deletedClaims.push(elem.baseAmount/100);
            }
          });
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        })
        .then(function () {
          // always executed
        });
      }
    },
    data() {
      return {
        openClaims:[],
        paidClaims:[],
        deletedClaims:[],
        lineChartData: {
          labels: [],
          datasets: [
            {
              label: "Monthly Claims Amount",
              backgroundColor: "rgba(255, 99, 132, 0.1)",
              borderColor: "rgba(255, 99, 132, 1)",
              borderWidth: 0.7,
              data: []
            },
            {
              label: "Monthly Claims Fees",
              backgroundColor: "rgba( 0, 0,255, 0.1)",
              borderColor: "rgba(0, 0,255, 1)",
              borderWidth: 0.7,
              data: []
            }
          ]
        },
        lineChartOptions: {
          responsive: true,
          maintainAspectRatio: true,
          scales: {
            xAxes: [
              {
                gridLines: {
                  display: true,
                  color: "rgba(0, 0, 0, 0.1)"
                }
              }
            ],
            yAxes: [
              {
                gridLines: {
                  display: true,
                  color: "rgba(0, 0, 0, 0.1)"
                }
              }
            ]
          }
        }
      }
    }
  }
</script>