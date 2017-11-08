<template>
  <transition name="modal">
    <div class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container" :class="{plusWidth: timeFormat == 'am-pm'}">
          <div class="btnclose">
          <button type="button" v-on:click="$emit('close')">close</button>
          </div>
            <h6>B.B. DETAILS</h6>
            <p v-if="errorMsg != ''" class="alert-danger" style="text-align: center;" >{{errorMsg}}</p>
            <p v-if="msg != ''" class="alert-info" style="text-align:center">{{msg}}</p>
            <table class="table table-bordered">
              <tbody>
                <!-- 12-24 Format -->
                <!--<tr v-if="timeFormat != 'am-pm'">
                  <th>when:</th>
                  <td v-if="access == '2'">
                    <select v-model="selTimeH_Start">
                      <option v-for="tH_s in timeH_S" :value="tH_s">{{tH_s}}</option>
                    </select>
                    <select v-model="selTimeM_Start">
                      <option value="0">00</option>
                      <option value="30">30</option>
                    </select>
                     - 
                    <select v-model="selTimeH_End">
                      <option v-for="tH_e in timeH_E" :value="tH_e">{{tH_e}}</option>
                    </select>
                    <select v-model="selTimeM_End">
                      <option value="0">00</option>
                      <option value="30">30</option>
                    </select>
                  </td>
                  <td v-else>
                   {{curentCreateTime}}
                  </td>
                </tr>

             
                <tr v-else>
                  <th>When:</th>
                  <td v-if="access == '2'">
                    <select v-model="selTimeH_Start">
                      <option v-for="tAmPmS in timeAMPM_Start" :value="tAmPmS.val">{{tAmPmS.title}}</option>
                    </select>
                    <select v-model="selTimeM_Start">
                      <option value="0">00</option>
                      <option value="30">30</option>
                    </select>
                    <select v-model="selAmPmStart">
                      <option value="am">AM</option>
                      <option value="pm">PM</option>
                    </select>
                     - 
                    <select v-model="selTimeH_End">
                      <option v-for="tAmPmE in timeAMPM_End" :value="tAmPmE.val">{{tAmPmE.title}}</option>
                    </select>
                    <select v-model="selTimeM_End">
                      <option value="0">00</option>
                      <option value="30">30</option>
                    </select>
                    <select v-model="selAmPmEnd">
                        <option value="am">AM</option>
                        <option value="pm">PM</option>
                    </select>
                  </td>
                  <td v-else>
                   {{curentCreateTime}}
                  </td>
                </tr>-->
                <tr>
                  <th>notes:</th>
                  <td v-if="access == '2'">
                    <input type="text" v-model="vModelForDescription" :value="currentEvent.description">
                  </td>
                  <td v-else>{{currentEvent.description}}</td>
                </tr>
                <tr>
                  <th>who:</th>
                  <td v-if="nameUserFromCurrentEvent && access == '2' && success != 'success'">
                    <select class="selUser" v-model="currenForUsersVmodel">
                        <option v-for="user in users" :value="user.id">{{user.login}}</option>
                    </select>
                  </td>
                  <td v-else-if="nameUserFromCurrentEvent && access == '1' && success != 'success'">{{nameUserFromCurrentEvent}}</td>
                  <td v-else-if="!nameUserFromCurrentEvent" class="alert-danger">No-access</td>
                  <td v-else>{{nameUserFromCurrentEvent}}</td>
                </tr>
                <tr>
                  <td colspan="2">Submitted: {{curentCreateTime}}</td>
                </tr>
              </tbody>
            </table>
          <div v-if="occurrenceSection == 'show'" class="checkA">
              <input type="checkbox" id="checkbox" v-model="checked">
              <label for="checkbox">Apply to all occurrences?</label>
          </div>
          <div v-if="success != 'success'">
          <div v-if="access == '2' || user.id== curentIdUserInCurrentEvent" class="btn-section">
            <button  v-on:click="updateEvent()">Update</button>
            <button v-on:click="deleteEvent()">Delete</button>
          </div>
          </div>
          <div v-else style="text-align:center">
            <button class="btn btn-primary" v-on:click="$emit('close')" >Close</button>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>
<script>
import axios from 'axios'
export default {
  name: 'HelloWorld',
  props: ['sentEvent', 'timeFormat'],
  data () {
    return {
      msg: '',
      errorMsg: '',
      success: '',
      access: '',
      currentEvent: {},
      user: {},
      users:[],
      nameUserFromCurrentEvent:'',
      currentDescription:'',
      curentIdUserInCurrentEvent:'',
    }
  },
  methods: {
    setProporties: function(){
      var self = this
      self.currentEvent = self.sentEvent
      self.nameUserFromCurrentEvent = self.currentEvent.user_name
      self.vModelForDescription = self.currentEvent.description
      self.curentIdUserInCurrentEvent = self.currentEvent.id_user
      self.curentCreateTime = self.currentEvent.create_time
      self.currenForUsersVmodel = self.curentIdUserInCurrentEvent  
    },
      getAllUsers: function(){
      var self = this
          //axios.get('http://192.168.0.15/~user2/Booker/client/api/employees/', self.config)
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
    checkUserFun: function(){
      var self = this
      if (localStorage['user'])
      {    
        self.user = JSON.parse(localStorage['user'])
       //axios.get('http://192.168.0.15/~user2/Booker/client/api/users/' + self.user.id)
          axios.get('http://BoardroomBooker/user2/Booker/client/api/users/' + self.user.id)
            .then(function (response) {
                if (self.user.hash === response.data[0].hash)
                {
                    self.role = response.data[0].role
                    self.checkUserRole()
                    self.setProporties()
                      
                     
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
     checkUserRole: function(){
      var self = this
      if (self.role == 'admin')
      {
        self.access = '2'
      }
      else{
        self.access = '1'
      }
    },
    updateEvent: function(){
            var self = this
            //console.log(self.currenForUsersVmodel)
    }
  },
  
  created(){
    this.checkUserFun()
    this.getAllUsers()
   
  
  },
  computed: {

  }
}
</script>

<style scoped>
.modal-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: table;
}
.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}
.modal-container {
  width: 300px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color:  #3CB371;
  border:2px solid red;

 
}
table tr td{
     border:2px solid #6495ED;
}
table tr th{
     border:2px solid #6495ED;
}
.btnclose{
  float: right;
}
.btn-section{
  text-align: center;
}
.btn-section .btn-danger{
  margin-left: 20px;
}
h6{
  text-align: center;
}
th{
  width: 50px;
}
.checkA{
  text-align: center;
  margin-bottom: 10px;
}
.plusWidth{
  width: 420px;
}
</style>