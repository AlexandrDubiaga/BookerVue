<template>
   
    <div class="regForm">
      <p class="alert-danger">{{errorMsg}}</p>
      <div v-if="success !== 'success'">
        <fieldset>
            <div id="legend">
            <legend class="title">Add new Employees</legend>
            </div>
            <div class="control-group">
            <!-- login -->
            <label class="control-label"  for="login">login</label>
            <div class="controls">
                <input v-model="login"   type="text" id="login" name="login" placeholder="" >
            </div>
            </div>
            <div class="control-group">
            <!-- Password-->
            <label class="control-label" for="email">Email</label>
            <div class="controls">
                <input v-model="email" type="email" id="email" name="email" placeholder="" >
            </div>
            </div>
        
            <div class="control-group">
            <!-- Password-->
            <label class="control-label" for="password">password</label>
            <div class="controls">
                <input v-model="pass" type="password" id="password" name="password" placeholder="" >
            </div>
            </div>
            <div class="control-group">
            <!-- Button -->
            <div class="controls">
                <button v-on:click="registration()" class="btn  btn-success">Add Employee</button>
               <router-link class="link" to='/EmployeeAdd'><button class="btn btn-success">Back</button></router-link>
            </div>
            </div>
        </fieldset>
    </div>
    <div v-else class="success">
      <h2>Thank you <strong>{{fullName}}</strong>, </h2>
      <router-link class="link" to='/EmployeeAdd'>Back to employees list</router-link>
    </div>
    </div>
 
</template>

<script>
import axios from 'axios'
export default {
  name: 'EmployeesForm',
  data () {
    return {
      login: '',
      pass: '',
      email:'',
      passConf: '',
      config: {
        headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
            }
      },
      success: '',
      errorMsg: ''
    }
  },
  methods: {
      registration: function () {
          var self = this
          self.errorMsg = ''
          if (self.login && self.pass && self.email)
          {
              if (self.pass.length <= 3)
              {
                  self.errorMsg = 'Password should be at least 4 characters'
                  return false
              }
              
            
                var data = new URLSearchParams();
                data.append('login', self.login);
                data.append('email', self.email);
                data.append('pass', self.pass);
                axios.post('http://BoardroomBooker/user2/Booker/client/api/employees/', data, self.config)
                 //axios.post('http://192.168.0.15/~user2/Booker/client/api/employees/', data, self.config)
                    .then(function (response) {
                     
                    if (response.data === 1)
                    {
                        self.success = 'success'
                    }
                    else
                    {
                        self.errorMsg = response.data
                    }
                })
                    .catch(function (error) {
                    console.log(error);
                });
          }
          else{
              self.errorMsg =  'Enter data in all fields!'
          }
      },
      checkUser: function(){
          var self = this
          if (localStorage['user']){
             self.$router.push('/EmployeesForm')
          }else{
                 self.$router.push('/')
          }
      }
  },
  computed: {
    fullName(){
      return this.login;
    }
  },
  created(){
      this.checkUser()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.x
{
    position:absolute;
    top:10px;
}
input
{
    height:30px;
    border-radius:10px;
}
button
{
    margin-top:10px;
}
</style>