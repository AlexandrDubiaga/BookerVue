<template>

            <div class="control-group">
              <div v-if="success !== ''" class="alert alert-danger">
                {{ success }}
            </div>
               <div class="author">
 
      <table class="table table-striped">
         <thead>
           <tr class="head">
          
             <th>Login Employee</th>
             <th>Email Employee</th>
             <th>Edit Employee</th>
             <th>Remove Employee</th>
           </tr>
         </thead>
         <tbody>
           <tr v-for="(employee,index) in users">
             <td><div class="col-sm-10">
                    <input v-model="employee.login" :value="employee.login" type="text" class="form-control" id="login" name="login">
                </div></td>
             <td><div class="col-sm-10">
                    <input v-model="employee.email" :value="employee.email" type="text" class="form-control" id="email" name="email">
                </div></td>
             <td><button class="btn btn-outline-primary" v-on:click="saveDataUsers(index)">Edit</button></td>
             <td><button class="btn btn-outline-primary" v-on:click="deleteEmployeeById(employee.id)">Remove</button></td>
           </tr>
         </tbody>
       </table>
      
          

  </div>
  
            <!-- Button -->
            <div class="controls">
                <router-link to='/employeesForm'><button class="btn btn-success">Add employee</button></router-link>
                 <router-link class="link" to='/calendar'><button class="btn btn-success">Back to Calendar</button></router-link>
            </div>
            </div>
</template>

<script>
import axios from 'axios'


export default {
  name: 'EmployeesAdd',
  data () {
    return {
       users:[],
       checkUser:0,
       success:'',
       errorMsg:'',
      config: {
                headers: {'Content-Type': 'application/x-www-form-urlencoded'
                    }
          },
      employee:'',
    }
  },
  methods: {
    deleteEmployeeById: function($id){
      var self = this
     //axios.delete('http://BoardroomBooker/user2/Booker/client/api/users/' + $id)
     axios.delete('http://192.168.0.15/~user2/Booker/client/api/users/' + $id)
            .then(function (response) {
              if (response.data === 1)
                    {
                     
                        self.success = 'Employee deleted'
                        console.log( self.success)
                    }
                    else
                    {
                        self.errorMsg = 'error'
                    }            
        })
    },
  
  
  
   
    getAllUsers: function(){
      var self = this
          axios.get('http://192.168.0.15/~user2/Booker/client/api/employees/', self.config)
          //axios.get('http://BoardroomBooker/user2/Booker/client/api/employees/', self.config)
            .then(function (response) {
              if (response.status == 200) {
                  self.users = response.data;
                
              }
            else{
              self.errors = response.data
            }
        })
        .catch(function (error) {
          console.log(error)
        });
      },
  computed: {
   
  },
    checkUserFun: function(){
      var self = this
      if (localStorage['user'])
      {    
        self.user = JSON.parse(localStorage['user'])
       axios.get('http://192.168.0.15/~user2/Booker/client/api/users/' + self.user.id)
          //axios.get('http://BoardroomBooker/user2/Booker/client/api/users/' + self.user.id)
            .then(function (response) {
                if (self.user.hash === response.data[0].hash)
                {
                      
                    self.checkUser = 1;
                    self.role = response.data[0].role
                    return true
                }
            })
            .catch(function (error) {
              console.log(error)
            });
      }
      else{
        self.checkUser = ''
          delete localStorage['user']
          self.$router.push('/')
          return false
      }
    },
     saveDataUsers: function(index){
        var self = this        
              var data = {}
              data = self.users[index]
              //console.log(data)
              axios.put('http://192.168.0.15/~user2/Booker/client/api/employees/', data, self.config)
              //axios.put('http://BoardroomBooker/user2/Booker/client/api/employees/', data, self.config)
                    .then(function (response) {
                       if (response.data === 1)
                    {
                     
                        self.success = 'Employee update'
                        console.log( self.success)
                    }
                    else
                    {
                        self.errorMsg = 'error'
                    }
                   
                })
                    .catch(function (error) {
                    console.log(error)
                })
    },
},
  
 created(){
    this.getAllUsers()
    this.checkUserFun()
   
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.author{
  text-align: center;
}
.table{
  width: 700px;
  margin: auto;
}
.table tr th{
  text-align: center;
   border: 2px solod pink;
}
.title{
  color: blue;
  font-size: 18px;
}

</style>