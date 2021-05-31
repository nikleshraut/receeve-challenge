<template>
  <div>
    <h1>Account Details</h1>
    <div class="row">
      <div class="col-sm-6">
        Account Id : <strong>{{accountDetail.id}}</strong>
      </div>
      <div class="col-sm-6">
        Name : <strong>{{accountDetail.name}}</strong>
      </div>
      <div class="col-sm-6">
        Email : <strong>{{accountDetail.email}}</strong>
      </div>
      <div class="col-sm-6">
        Mobile : <strong>{{accountDetail.mobile}}</strong>
      </div>
      <div class="col-sm-12">
        Address : <strong>{{accountDetail.address}}</strong>
      </div>
    </div>
    <div class="row">
      <div class="col-sm-3 form-inline p-4">
          <div class="form-group">
              <label for="filter" class="sr-only">Filter</label>
              <input type="text" class="form-control" v-model="filter" placeholder="Filter" @keydown="$event.stopImmediatePropagation()">
          </div>
      </div>
      <datatable :columns="claims.columns" :data="claims.data" :filter="filter" :per-page="10"></datatable>
      <datatable-pager class="d-flex justify-content-center pt-4" v-model="page" type="short"></datatable-pager>
    </div>
    <div><h3 class="text-primary share-link"><span @click="shareLink()">Share ></span></h3></div>
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
  .share-link span{
    cursor: pointer;
  }
</style>
<script>
import Vue from 'vue'
import axios from 'axios'
import { VuejsDatatableFactory } from 'vuejs-datatable';
Vue.use( VuejsDatatableFactory );
export default {
  mounted() {
    this.getAccounts();
    this.accountId = this.$route.params.id;
    this.claims.columns = [
      { label: 'ID', field: 'id', filterable: false, align:'center' },
      { label: 'Currency', field: 'currency',align:'center' },
      { label: 'Base Amount', field: 'baseAmount', align:'center' },
      { label: 'Fees', field: 'fees', align:'center' },
      { label: 'Due Date', field: 'dueDate', align:'center' },
      { label: 'Status', field: 'status', align:'center' }
    ];
  },

  methods:{
      shareLink: function (){
        var tempInput = document.createElement("input");
        tempInput.value = window.location.href;
        document.body.appendChild(tempInput);
        tempInput.select();
        document.execCommand("copy");
        document.body.removeChild(tempInput);
        alert("Link copied to share")
      },
      getClaims: function (){
        axios.get("http://localhost:9001/claims")
        .then( (response) => {
          this.claims.data = response.data.filter( (e) =>{ 
            return e.accountId === this.accountId;
          });
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
          const accountIndex = response.data.map( (elem)=> elem.id ).indexOf(this.accountId);
          const elem = response.data[accountIndex];
          this.accountDetail = {
              id: elem.id,
              name: `${elem.debtor.title} ${elem.debtor.firstName} ${elem.debtor.firstName}`,
              address: `${elem.debtor.address.address}, ${elem.debtor.address.city},  ${elem.debtor.address.state}, ${elem.debtor.address.zip}, ${elem.debtor.address.country}`,
              mobile: elem.debtor.mobilePhone,
              email: elem.debtor.email
            }
            this.getClaims();
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
      accountId:'',
      accountDetail:{},
      claims: {columns:[], data:[]},
      filter: "",
      page: 1
    }
  }
}
</script>