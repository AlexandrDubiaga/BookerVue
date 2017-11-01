<template>

            <div class="control-group">
                <userlist :userItem="users" ></userlist>
            <!-- Button -->
            <div class="controls">
                <router-link to='/employeesForm'><button class="btn btn-success">Add user</button></router-link>
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
         config: {
                headers: {'Content-Type': 'application/x-www-form-urlencoded'
                    }
              },
    }
  },
  methods: {
   
    getAllUsers: function(){
      var self = this
          axios.get('http://192.168.0.15/~user2/Booker/client/api/users/', self.config)
            .then(function (response) {
                console.log(response.data)
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
  created(){
    this.getAllUsers()
  },
},
components:{
          'userlist': UserList
    },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>


</style>