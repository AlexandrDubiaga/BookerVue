<template>
  <div class="main container-fluid"  v-if="checkUser >0">
         <window
     v-if="showModal" 
      :sentEvent="sentEvent" 
      v-on:close="showModal = false">
     </window>

     <div class="row">
      <p class="rooms">
     
            <button class="btn btn-default" v-for="(room,index) in rooms" v-on:click=" getAppointmentByIdUserBoardroomId(index)">{{room.name}}</button>
           
                 <router-link :to="{name:'AddAppointment',params:{id:selRoom.id}}"><button class="btn btn-success">Book it</button></router-link>
           <router-link to='/EmployeeAdd'><button class="btn btn-success"  v-if="checkUser == 2">Employee List</button></router-link>  
                <button v-on:click="logoutFun()" type="submit" class="btn btn-primary exit">Exit</button>
              <p><span class="">{{uservar}}</span></p>
            
          </p>
          <p class="roomSel">
            Room is: <strong>{{selRoom.name}}</strong>
          </p> 
          
    </div>

    <!--<div class="ch-year ">
      <button class="btn btn-primary"v-on:click="counter  = 2">First day Su</button>
      <button class="btn btn-primary" v-on:click="counter  = 1">First day Mon</button>
    </div>-->
   <div class="row">  
    <div class="col-md-5  solo"><h1>Boardroom Booker</h1></div>
      </div>
      <div class="title">
          <p class="leftCursor" v-on:click="minusMonth()">«</p>
          <p>{{months[currentMonth]}} {{currentYear}}</p>
           <p class="rightCursor" v-on:click="plusMonth()">»</p>
        </div>
        
   
        
        <table class="table table-bordered">
          <thead>
          <tr class="heads">
            <th v-for="wday in daysSun">{{wday}}</th>
          </tr>
         
          </thead>
          <tbody>
          <tr v-for="week in weeks">
            <td class="day" v-for="day in week" :class="{date: day[0] == currentDay}">{{day[0]}}
                <p  class="events" v-if="day.length > 1" v-for="event in day[1]" >
                  <button class="btn btn-link" v-on:click="showEvent(event)" >{{event.timeString}}</button>
                  </p>
              </td>  
          </tr>
          </tbody>
        </table>
         <!--<div class="col-md-3 linkDiv">  
        <div class="col-md-12 booker-but">
         
        </div>
          </div>-->

      
    </div>
  </div>
</template>
<script>
import axios from "axios";
import Modalwindow from './window'
export default {
  name: "Calendar",
  data() {
  
    return {
      uservar:'',
      checkUser: 0,
      dataInCalendar:'',
      rooms: [],
      timeData:{},
      selRoom: {},
      counter: 2,
      typeC:'',
      inst_date: new Date(),
      daysSun: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'],
      daysMon: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
      months: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'June', 'July', 'Aug', 'Sept', 'Oct', 'Nov', 'Dec'],
      config: {
        headers: {
          "Content-Type": "application/x-www-form-urlencoded"
        }
      },
       msg: '',
      errorMsg: '',
      date: new Date(),
      weeks: [],
      currentMonth: '',
      currentYear: '',
      rooms: [],
      eventsMonth: [],
      sentEvent: {},
         showModal: false
  

    };
  },
   methods: {
      showEvent: function(event){
      var self = this
      self.showModal = true
      self.sentEvent = event
      
    },
    getRooms: function(){
      var self = this
      //axios.get('http://BoardroomBooker/user2/Booker/client/api/rooms/')
      axios.get('http://192.168.0.15/~user2/Booker/client/api/rooms/')
          .then(function (response) {
            self.rooms = response.data
            self.selRoom = self.rooms[0]
      })
      .catch(function (error) {
        console.log(error)
      })
    },
    getEvents: function(){
   
      var self = this
    
          axios.get('http://192.168.0.15/~user2/Booker/client/api/events/')
          //axios.get('http://BoardroomBooker/user2/Booker/client/api/events/')
            .then(function (response) {
              if (response.status == 200) {
                self.eventsMonth = response.data;  
                 self.getArrayCalendar()
              }
            else{
              self.errors = response.data
            }
        })
        .catch(function (error) {
          console.log(error)
        });
      },
    getAppointmentByIdUserBoardroomId: function(index){
   
      var self = this
      self.selRoom = self.rooms[index]
        var year = self.currentYear
      var month = self.currentMonth+1
      var dateString = year + '-' + month 
        var data = new URLSearchParams();
        data.append('data_string', dateString);
        data.append('id_room', self.selRoom.id);
    
          axios.get('http://192.168.0.15/~user2/Booker/client/api/events/' + self.selRoom.id)
          //axios.get('http://BoardroomBooker/user2/Booker/client/api/events/'+ data)
            .then(function (response) {
              if (response.status == 200) {
                self.eventsMonth = response.data;  
                console.log(self.eventsMonth)
                self.getArrayCalendar()
              }
            else{
              self.errors = response.data
            }
        })
        .catch(function (error) {
          console.log(error)
        });
      },
       getMonthYear: function()
    {
      var self = this
      self.currentMonth =self.date.getMonth()
      self.currentYear = self.date.getFullYear()
    },
    getArrayCalendar: function(){
      var self = this
      self.weeks = []
      var date = new Date(self.currentYear, self.currentMonth)
      self.weeks[0] = []
      for (var i=0; i < self.getNumDay(date); i++)
      {
        self.weeks[0].push([])
      }
      var count = 0
      while (date.getMonth() == self.currentMonth)
      {
        self.weeks[count].push([date.getDate()])
        if (self.getNumDay(date) % 7 == 6)
        {
          count++
          self.weeks[count] = []
        }
        date.setDate(date.getDate()+1)
      }
      self.addEventsToCal()
    },
    addEventsToCal: function(){
      var self = this
      var calendar = self.weeks
      
      calendar.forEach(function(week) {
        week.forEach(function(day){
          if (day[0]){
            self.eventsMonth.forEach(function(event)
            {
              if (event.id_room == self.selRoom.id)
              {
              var dateEvStart = new Date(event.time_start)
              var dateEvEnd = new Date(event.time_end)
              var date = new Date(self.currentYear, self.currentMonth+1, day[0])
              if (dateEvStart.getDate() === day[0])
              {
                  var str = ''
                  var start = dateEvStart.getHours()
                  var end = dateEvEnd.getHours()
                  if (dateEvStart.getMinutes() == 0)
                  {
                  start +=':' + dateEvStart.getMinutes() + '0-'
                  }
                  else{
                  start +=':' + dateEvStart.getMinutes() + '-'
                  }
                  if (dateEvEnd.getMinutes() == 0)
                  {
                    end += ':' + dateEvEnd.getMinutes() + '0'
                  }
                  else{
                    end += ':' + dateEvEnd.getMinutes() 
                  }
                  str = start + end
                  event.timeString = str
                  if (day.length == 1)
                  {
                    day.push([event])
                  }
                  else{
                    day[1].push(event)
                  }
              }
              }
            })
          }
        })
      });
    },
    getNumDay: function(date){
      var self = this
      var numDay = date.getDay()
      if (self.weekDays == 'mon')
      {
        if (numDay == 0)
        {
          numDay = 7
        }
        return numDay - 1
      }
      if (self.weekDays == 'ru')
      {
        if (numDay == 0)
        {
          numDay = 7
        }
        return numDay - 1
      }
      else
      {
        return numDay
      }
    },

    plusMonth: function(){
      var self = this
      self.currentMonth += 1
      if (self.currentMonth > 11)
      {
        self.currentMonth = 0
        self.currentYear += 1
      }
      //self.getAppointmentByIdUserBoardroomId(self.selRoom.id)
      self.getArrayCalendar()
      
    },
    minusMonth: function(){
      var self = this
      self.currentMonth -= 1
      if (self.currentMonth < 0){
        self.currentMonth = 11
        self.currentYear -= 1
      }
      //self.getAppointmentByIdUserBoardroomId(self.selRoom.id)
      self.getArrayCalendar()
    },
    firstMonday: function(){
      var self = this 
      self.weekDays = 'mon'
      self.getArrayCalendar()
    },
    firstSunday: function(){
      var self = this
      self.weekDays = 'sun'
      self.getArrayCalendar()
    },
    
     logoutFun: function(){
      var self = this
      delete localStorage['user'] 
       self.checkUser = ''
       self.$router.push("/");
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
                  if(response.data[0].role == "admin")
                  {
                    self.checkUser = 2;
                    self.role = response.data[0].role
                    self.uservar = "Hello,"+ response.data[0].login
               
                    return true
                  }
                   if(response.data[0].role == "user")
                  {
                    self.checkUser = 1;
                    self.role = response.data[0].role
                   self.uservar = "Hello,"+ response.data[0].login
                    return true
                  }
                  
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
   

  },
 
    computed: {
   currYear() {
      var self = this;
      return self.inst_date.getFullYear();
    },
    currMonth() {
      var self = this;
      return self.inst_date.getMonth();
    },
    currWD() {
      var self = this;
      return self.inst_date.getDay();
    },
    currDay() {
      var self = this;
      const now = new Date();
      if (
        self.inst_date.getMonth() == now.getMonth() &&
        self.inst_date.getFullYear() == now.getFullYear()
      ) {
        return now.getDate();
      }
    },
    currentDay(){
      var self = this
      if (self.date.getMonth() == self.currentMonth && self.date.getFullYear() == self.currentYear)
      {
        return self.date.getDate()
      }
      else
      {
        return false
      }
    },
     
  },

  created(){
    var self = this
 
    this.checkUserFun()
    this.getMonthYear()
    this.getRooms()
    this.getEvents()
    this.getAppointmentByIdUserBoardroomId(self.selRoom.id)
    
     
  },
    components: {
      'window': Modalwindow
  }
};
</script>

<style scoped>
.main{
 background:#EDEEF0;
}
.heads
{
  background: red;
}
.leftCursor
{
  padding-right: 20px;
  cursor: pointer;
}
.rightCursor
{
  padding-left: 20px;
   cursor: pointer;
}
.solo
{
  padding-right: 230px;
}
.shadow {
  padding: 0;
  box-shadow: 0 0 10px rgba(0,0,0,0.5);
}
.date{
  background-color: pink;
  color: #b12d1f;
  font-weight: bold;
}

.title{
  text-align: center;
  color: darkblue;
  font-size: 17px;
  font-weight: bold;
  background-color: #3CB371;
  padding-top: 10px;
  width: 500px;
}
table{
  width: 1500px;
}
table tr{
    background:#8FBC8F;
}
table th{
    background: #3CB371;
   
}
.title p{ 
  display: inline-table;
}

.day{
  cursor: pointer;
  width: 122.61px;
  height: 118.33px;
}

.events{
  text-align: center;
  color: black;
  font-weight: normal; 
  margin: 0;
}
.events button{
  padding: 0;
}


.link
{
  font-size:15px;
}
.linkDiv
{
  
  position: absolute;
  left: 1600px;
  top:210px;
}




</style>
