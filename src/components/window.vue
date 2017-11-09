<template>
  <transition name="modal">
    <div class="modal-mask">
 
      <div class="modal-wrapper">
        <div class="modal-container">
          <div class="btnclose">
          <button type="button" v-on:click="$emit('close')">close</button>
          </div>
            <h6>B.B. DETAILS</h6>
            <p v-if="success != ''" class="alert-danger" style="text-align: center;" >{{success}}</p>
              
            <p v-if="msg != ''" class="alert-info" style="text-align:center">{{msg}}</p>
            <table class="table table-bordered">
              <tbody>
                <tr>
                  <th>1</th>
                  <td>
                    <select  v-model="StartHour">
                <option :value="StartHour"  v-for="StartHour in 12">{{StartHour}}</option>
              </select>
              <select v-model="StartMinutes">
                <option :value="StartMinutes * 15" v-for="StartMinutes in 4">{{StartMinutes * 15}}</option>
              </select>
              <!-- <select v-model="timeStartMidnight">
                <option value="0">AM</option>
                <option value="1">PM</option>
              </select> -->
              <select  v-model="EndHour">
                <option :value="EndHour"  v-for="EndHour in 24">{{EndHour}}</option>
              </select>
              <select v-model="EndMinutes">
                <option :value="EndMinutes * 15" v-for="EndMinutes in 4">{{EndMinutes * 15}}</option>
              </select>
              <!-- <select v-model="timeEndMidnight">
                <option value="0">AM</option>
                <option value="1">PM</option>
              </select> -->
                    </td>
                 
                </tr>
               
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
          <!-- <div v-if="occurrenceSection == 'show'" class="checkA">
              <input type="checkbox" id="checkbox" v-model="checked">
              <label for="checkbox">Apply to all occurrences?</label>
          </div> -->
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
      curentCreateTime:'',
      StartHour:'',
      StartMinutes:'',
      EndHour:'',
      EndMinutes:'',
      startDate:'',
      endDate:'',
      FullDateStart:'',
      success:'',
       config: {
                headers: {'Content-Type': 'application/x-www-form-urlencoded'
                    }
          },

    }
  },
  methods: {
    setProporties: function(){
      var self = this
      self.currentEvent = self.sentEvent
      
      self.currentEventId = self.currentEvent.id
      self.nameUserFromCurrentEvent = self.currentEvent.user_name
      self.roomIdCurrentEvent = self.currentEvent.id_room
      self.vModelForDescription = self.currentEvent.description
      self.curentIdUserInCurrentEvent = self.user.id
      self.curentCreateTime = self.currentEvent.create_time
      self.currenForUsersVmodel = self.curentIdUserInCurrentEvent  

      self.timeStart =  self.currentEvent.time_start
      self.timeEnd =  self.currentEvent.time_end

      self.startTime = new Date(self.timeStart);
      self.monthsStart = ['01','02','03','04','05','06','07','08','09','10','11','12'];
      self.yearStart = self.startTime.getFullYear();
      self.monthStart = self.monthsStart[self.startTime.getMonth()];
      self.dateStart =  self.startTime.getDate();
      self.StartHour = self.startTime.getHours();
      self.StartMinutes =  self.startTime.getMinutes();
     
     
      self.endTime = new Date(self.timeEnd);
      self.monthsEnd = ['01','02','03','04','05','06','07','08','09','10','11','12'];
      self.yearEnd = self.endTime.getFullYear();
      self.monthEnd = self.monthsEnd[self.endTime.getMonth()];
      self.dateEnd =  self.endTime.getDate();
      self.EndHour = self.endTime.getHours();
      self.EndMinutes =  self.endTime.getMinutes();
 
 
    

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
                    self.role = response.data[0].role
                    self.checkUserRole()
                    //console.log(self.access)
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
        self.error = ''
        var data = {}
        self.timeStart = self.yearStart + '-' + self.monthStart + '-' +self.dateStart  + ' ' + self.StartHour + ':' + self.StartMinutes + ':' + '00' ;
        self.timeEnd = self.yearEnd + '-' + self.monthEnd + '-' +self.dateEnd  + ' ' + self.EndHour + ':' + self.EndMinutes + ':' + '00' ;
        data.id =  self.currentEventId 
        data.id = self.currentEventId
        data.id_user = self.currenForUsersVmodel;
        data.id_room = self.roomIdCurrentEvent
        data.description = self.vModelForDescription
        data.time_start =  self.timeStart 
        data.time_end =    self.timeEnd
        data.create_time =   (Date.now()/1000).toFixed()
          //axios.put('http://BoardroomBooker/user2/Booker/client/api/events/', data, self.config)
           axios.put('http://192.168.0.15/~user2/Booker/client/api/events/', data, self.config)
          .then(function(response){
            if (response)
            {  
          
              self.success = 'Update success'
              self.$emit('refresh')
            }
            else {
              self.error = 'Error update!'
              console.log('alex')
            } 
          })
    }

  },
  
  created(){
    this.checkUserFun()
    this.getAllUsers()
   
  
  },
 computed: {
    hoursSelector(){
      var self = this
        var hours = []
          for(var i=8;i<=20;i++){
            hours.push({value:i, title:i})
        }
        return hours
    },
      hoursStSelector(){
      var self = this
        var hours = []
          for(var i=8;i<=19;i++){
            hours.push({value:i, title:i})
        }
        return hours
    },
    minutesSelector(){
      var self = this
      var minutes = []
      self.minutes.forEach(function(m){
        minutes.push({value:m, title:m})
      })
      return minutes
    }
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