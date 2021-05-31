<template>
  <div class="row register-page">
    <div class="col-sm-4 offset-4">
      <h3>Register</h3>
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

        <b-button type="submit" variant="primary">Submit</b-button>
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
        console.log(users);
        if(users.map( (e)=> e.email).indexOf(this.form.email) > -1){
          this.error = '<div class="text-danger">Email already exist, Go to login<a href="/#/login"> page</a></div>';
          return false;
        }
        localStorage.setItem('receeve_user', JSON.stringify({email:this.form.email}));
        users.push({email:this.form.email, password:this.form.password});
        localStorage.setItem('receeve_users', JSON.stringify(users));
        this.$router.push({ name: "Dashboard"})
      }
    }
  }
</script>