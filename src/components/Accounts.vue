<template>
  <div>
    <h1>Accounts</h1>
    <div class="row">
      <div class="col-sm-3 form-inline p-4">
          <div class="form-group">
              <label for="filter" class="sr-only">Filter</label>
              <input type="text" class="form-control" v-model="filter" placeholder="Filter" @keydown="$event.stopImmediatePropagation()">
          </div>
      </div>
      <datatable :columns="accounts.columns" :data="accounts.data" :filter="filter" :per-page="10" />
      <datatable-pager v-model="page"></datatable-pager>
    </div>
  </div>
</template>
<style>
  nav ul {
    display: flex;
    list-style-type: none;
  }
  nav ul li {
    padding: 0 10px;
    color: #0d6efd;
  }
  nav ul li.active {
    background: #6c757d;
    color: #fff;
  }
</style>
<script lang="ts">
import Vue from 'vue'
import Component from 'vue-class-component'
import axios from 'axios'
import { VuejsDatatableFactory } from 'vuejs-datatable';
Vue.use( VuejsDatatableFactory );
@Component({})
export default {
  created() {
    console.log('Accounts created!')
  },
  mounted() {
    console.log('Dashboard mounted')
    this.getAccounts();
    this.getClaims();
    this.accounts.columns = [
      { label: 'id', field: 'id', filterable: false },
      { label: 'Name', field: 'name' },
      { label: 'Address', field: 'address' },
      { label: 'Mobile', field: 'mobile' },
      { label: 'Email', field: 'email' }
    ];
  },
  methods:{
      getClaims: function (){
        axios.get("http://localhost:9001/claims")
        .then( (response) => {
          this.claims = response.data
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        })
        .then(function () {
          // always executed
        });
      },
      getAccounts: function (){
        axios.get("http://localhost:9001/accounts")
        .then( (response) => {
          response.data.map((elem)=>{
            this.accounts.data.push({
              id: elem.id,
              name: `${elem.debtor.title} ${elem.debtor.firstName} ${elem.debtor.firstName}`,
              address: `${elem.debtor.address.address}, ${elem.debtor.address.city},  ${elem.debtor.address.state}, ${elem.debtor.address.zip}, ${elem.debtor.address.country}`,
              mobile: elem.debtor.mobilePhone,
              email: elem.debtor.email
            })
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
      claims: [],
      accounts: {columns:[], data:[]},
      filter: "",
      page: 1
    }
  }
}
</script>