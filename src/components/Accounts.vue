<template>
  <div>
    <h1>Accounts</h1>
    <div class="">
      <div class="col-sm-3 form-inline p-4">
          <div class="form-group">
              <label for="filter" class="sr-only">Filter</label>
              <input type="text" class="form-control" v-model="filter" placeholder="Filter" @keydown="$event.stopImmediatePropagation()">
          </div>
      </div>
      <datatable :columns="accounts.columns" :data="accounts.data" :filter="filter" :per-page="10" >
        <template v-slot:item.action="{ item }">
          <v-icon small class="mr-2" @click="claimDetails(item)">edit</v-icon>
        </template>
      </datatable>
      <datatable-pager class="d-flex justify-content-center pt-4" v-model="page" type="short"></datatable-pager>
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
<script>
import Vue from 'vue'
import axios from 'axios'
import { VuejsDatatableFactory } from 'vuejs-datatable';
Vue.use( VuejsDatatableFactory );
export default {
  mounted() {
    const receeve_user = localStorage.getItem('receeve_user');
    if(!receeve_user){
      this.$router.push({ name: "Login"});
    }
    this.getAccounts();
    this.accounts.columns = [
      { label: 'id', field: 'id', filterable: false,align:'center' },
      { label: 'Name', field: 'name' ,align:'center'},
      { label: 'Address', field: 'address' ,align:'center'},
      { label: 'Mobile', field: 'mobile' ,align:'center'},
      { label: 'Email', field: 'email' ,align:'center'},
      { label: 'Claims', field: 'claim' , interpolate:true,align:'center'}
    ];
  },
  methods:{
      claimDetails: function (item) {
        console.log(item);
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
              email: elem.debtor.email,
              claim: `<a href='/#/account-claims/${elem.id}'>Account Claims</a>`
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
      accounts: {columns:[], data:[]},
      filter: "",
      page: 1
    }
  }
}
</script>