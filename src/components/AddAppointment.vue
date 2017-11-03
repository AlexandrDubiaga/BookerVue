<template>
   <div class="container"  v-if="checkUser >0">
    <div class="row">
      <h3>{{boardroom[0].name}}</h3>
      <div class="col-md-4 col-md-offset-4">
        <div class="col-md-12">
          <p class="text-left">1. Booked for:</p>
          <select  v-model="chooseUserId" class="form-control">
            <option :value="user.id" v-for="user in users">{{user.login}}</option>
          </select>
        </div>
      </div>
    </div>
     <div class="row">
      <div class="col-md-4 col-md-offset-4">
        <button v-on:click="addEvent" type="button" class="btn btn-success">Send</button>
     </div>
    </div>
     </div>
</template>

<script>
import axios from 'axios'
export default {
  
  data () {
    return {
    checkUser:0,
    room:'',
    role:'',
    boardroom:'',
    users:[],
    chooseUserId:'1',
    }
  },
  watch:{
    chooseUserId: function(){
       var self = this
      console.log(self.chooseUserId)
    }
  },
  
  methods: {
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
     checkUserFun: function(){
      var self = this
      if (localStorage['user'])
      {    
        self.user = JSON.parse(localStorage['user'])
      axios.get('http://192.168.0.15/~user2/Booker/client/api/users/' + self.user.id)
         // axios.get('http://BoardroomBooker/user2/Booker/client/api/users/' + self.user.id)
            .then(function (response) {
                if (self.user.hash === response.data[0].hash)
                {
                      
                    self.checkUser = 1;
                    self.role = response.data[0].role
                    return true
                }
                else
                {
                  self.checkUser = ''
                  delete localStorage['user']
                }
            })
            .catch(function (error) {
              console.log(error)
            });
      }
      else{
        self.checkUser = ''
        return false
      }
    },
     
     getBoadRoomById: function(id){
      var self = this
          axios.get('http://192.168.0.15/~user2/Booker/client/api/rooms/' + id)
          //axios.get('http://BoardroomBooker/user2/Booker/client/api/rooms/'+ id)
            .then(function (response) {
              if (response.status == 200) {
                  self.boardroom = response.data;
                  console.log( self.boardroom)
                
              }
            else{
              self.errors = response.data
            }
        })
        .catch(function (error) {
          console.log(error)
        });
      },
      addEvent: function () {
          var self = this
          console.log("add event")
               /* var data = new URLSearchParams();
                data.append('id_room', self.boardroom[0].id);
                data.append('id_user', self.chooseUserId);
                //axios.post('http://BoardroomBooker/user2/Booker/client/api/event/', data, self.config)
                 axios.post('http://192.168.0.15/~user2/Booker/client/api/event/', data, self.config)
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
                });*/
          }
  },

  
 
created(){
   this.checkUserFun()
   this.getBoadRoomById(this.$route.params.id)
   this.getAllUsers()


     
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.row{
  margin-bottom:15px;
}
.marg{
  margin-bottom:15px;
}
</style>