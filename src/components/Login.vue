<template>
  <div class="row register-page">
    <div class="col-sm-4 offset-4">
      <h3>Login</h3>
      <b-form @submit="onSubmit">
        <b-form-group
          id="input-group-1"
          label="Email:"
          label-for="input-1"
        >
          <b-form-input
            id="input-1"
            v-model="form.email"
            type="email"
            placeholder="Enter email"
            required
          ></b-form-input>
        </b-form-group>
        <b-form-group
          id="input-group-1"
          label="Password:"
          label-for="input-1"
        >
          <b-form-input
            id="input-1"
            v-model="form.password"
            type="password"
            placeholder="Enter password"
            required
          ></b-form-input>
        </b-form-group>
        <b-form-group
          id="input-group-1"
          label="Password:"
          label-for="input-1"
        >
          <div v-html="error"></div>
        </b-form-group>

        <b-button type="submit" variant="primary">Login</b-button>
        <div> <a href="/#/register">Register if you are new</a></div>
      </b-form>
    </div>
  </div>
</template>


<style>
  .register-page {
    height: calc(100vh - 72px);
    align-items: center;
  }
</style>

<script>
  export default {
    data() {
      return {
        form: {
          email: '',
          password: ''
        },
        error:""
      }
    },
    mounted(){
      const receeve_user = localStorage.getItem('receeve_user');
      if(receeve_user){
        this.$router.push({ name: "Dashboard"});
      }
    },
    methods: {
      onSubmit(event) {
        event.preventDefault();
        const receeve_users = localStorage.getItem('receeve_users');
        const users = receeve_users ? JSON.parse(receeve_users) : [];
        const index = users.map( (e)=> e.email).indexOf(this.form.email);
        if( index > -1){
          if(users[index].password == this.form.password) {
            localStorage.setItem('receeve_user', JSON.stringify({email:this.form.email}));
            this.$emit('user-data-updated', null);
            this.$router.push({ name: "Dashboard"});
          }else{
            this.error = '<div class="text-danger">Password not matched.</div>';
            return false;  
          }
        }else{
          this.error = '<div class="text-danger">Email not found, Go to register<a href="/#/register"> page</a></div>';
          return false;
        }
        
      }
    }
  }
</script>