<template>
  <div id="app">
    <div class="row">
      <div class="col-sm-12">
        <b-navbar toggleable="lg" type="dark" variant="info">
          <b-navbar-brand href="#"><router-link to="/dashboard"><span class="dashboard-menu">Dashboard</span></router-link></b-navbar-brand>
          <b-navbar-brand href="#"><router-link to="/accounts">Accounts</router-link></b-navbar-brand>
        </b-navbar>
      </div>
      <div class="col-sm-12">
        <router-view></router-view>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'app'
}
</script>
<style>
.navbar a{
  color: #343a40;
  text-decoration: none;
}
.dashboard-menu {
  padding-left:20px;
}
</style>