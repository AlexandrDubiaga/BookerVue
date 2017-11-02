<template>

            <div class="control-group">
                <userlist :userItem="users" ></userlist>
            <!-- Button -->
            <div class="controls">
                <router-link to='/employeesForm'><button class="btn btn-success">Add employee</button></router-link>
                 <router-link class="link" to='/calendar'><button class="btn btn-success">Back to Calendar</button></router-link>
            </div>
            </div>
</template>

<script>
import axios from 'axios'
import UserList from './UserList'

export default {
  name: 'EmployeesAdd',
  data () {
    return {
       users:[],
       checkUser:0,
         config: {
                headers: {'Content-Type': 'application/x-www-form-urlencoded'
                    }
              },
    }
  },
  methods: {
  
   
    getAllUsers: function(){
      var self = this
          //axios.get('http://192.168.0.15/~user2/Booker/client/api/users/', self.config)
          axios.get('http://BoardroomBooker/user2/Booker/client/api/employees/', self.config)
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
       // axios.get('http://192.168.0.15/~user2/Booker/client/api/users/' + self.user.id)
          axios.get('http://BoardroomBooker/user2/Booker/client/api/users/' + self.user.id)
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
    }
 
},
 created(){
    this.getAllUsers()
    this.checkUserFun()
  },
components:{
          'userlist': UserList
    },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>


</style>