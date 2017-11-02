<template>
  <div class="author">
 
      <table class="table table-striped">
         <thead>
           <tr class="head">
          
             <th>Login Employee</th>
             <th>Email Employee</th>
             <th>Role Employee</th>
             <th>Edit Employee</th>
             <th>Remove Employee</th>
           </tr>
         </thead>
         <tbody>
           <tr v-for="employee in userItem">
             <td><div class="col-sm-10">
                    <input v-model="employee.login" :value="employee.login" type="text" class="form-control" id="login" name="login">
                </div></td>
             <td><div class="col-sm-10">
                    <input v-model="employee.email" :value="employee.email" type="text" class="form-control" id="email" name="email">
                </div></td>
             <td><div class="col-sm-10">
                    <input v-model="employee.role" :value="employee.role" type="text" class="form-control" id="role" name="role">
                </div></td>
             <td><button class="btn btn-outline-primary" v-on:click="putEmployeeById(employee.id)">Edit</button></td>
             <td><button class="btn btn-outline-primary" v-on:click="deleteEmployeeById(employee.id)">Remove</button></td>
           </tr>
         </tbody>
       </table>
      
          

  </div>
  
  
</template>

<script>
import axios from 'axios'
export default {
  props: ['userItem'],
 data(){
   return{
     employee:'',
     userById:[],
   }
 },
 methods: {
 deleteEmployeeById: function($id){
      var self = this
     axios.delete('http://BoardroomBooker/user2/Booker/client/api/users/' + $id)
            .then(function (response) {
            self.employee = response.data[0]            
        })
    },
    putEmployeeById: function($id){
      var self = this  
     axios.put('http://BoardroomBooker/user2/Booker/client/api/employees/' + $id)
            .then(function (response) {
               console.log(response)
            self.employee = response.data[0]            
        })
    },
 },
    

 created(){
    this.deleteEmployeeById()
    this.putEmployeeById()
  },
}
</script>
<style scoped>
.author{
  text-align: center;
}
.table{
  width: 700px;
  margin: auto;
  border: 2px solid red;
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