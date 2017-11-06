<template>
  <div class="main container-fluid"  v-if="checkUser >0">
     <div class="row">
      <p class="rooms">
            <button v-for="(room,index) in rooms" v-on:click=" getAppointmentByIdUserBoardroomId(index)">{{room.name}}</button>
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
    <div class="col-md-5 head solo"><h1>Boardroom Booker</h1></div>
      </div>
      <div class="title">
          <button v-on:click="minusMonth()" class="btn btn-default">&#9668;</button>
          <p>{{getMonth}} {{currentYear}}</p>
          <button v-on:click="plusMonth()" class="btn btn-default">&#9658;</button>
        </div>
        <table class="table table-bordered">
          <thead>
          <tr class="info">
            <th v-for="wday in getDays">{{wday}}</th>
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

    <div class="col-md-3">  
        <div class="col-md-12 booker-but">
          <td><router-link :to="{name:'AddAppointment',params:{id:selRoom.id}}"><button class="btn btn-success">Book it</button></router-link></td>
           <td><router-link to='/EmployeeAdd'><button class="btn btn-success"  v-if="checkUser == 2">Employee List</button></router-link></td>    
        </div>
      
    </div>
  </div>
</template>
<script>
import axios from "axios";
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
      sentEvent: {}
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
          //axios.get('http://BoardroomBooker/user2/Booker/client/api/events/'+ self.selRoom.id)
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
          axios.get('http://192.168.0.15/~user2/Booker/client/api/events/' + self.selRoom.id)
          //axios.get('http://BoardroomBooker/user2/Booker/client/api/events/'+ self.selRoom.id)
            .then(function (response) {
              if (response.status == 200) {
                self.eventsMonth = response.data;  
                // self.getArrayCalendar()
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
      self.getEvents()
      self.getArrayCalendar()
      
    },
    minusMonth: function(){
      var self = this
      self.currentMonth -= 1
      if (self.currentMonth < 0){
        self.currentMonth = 11
        self.currentYear -= 1
      }
      self.getEvents()
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
    getRu: function(){
      var self = this
      self.nameMonth = 'ru'
      self.weekDays = 'ru'
      self.getArrayCalendar()
    },
    getEn: function(){
      var self = this
      self.nameMonth = 'en'
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
    getDays(){
      var self = this
      return self.weekDays
    },
    getMonth(){
      var self = this
      return self.nameMonth
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
  components: {
  },
  created(){
    var self = this
    this.checkUserFun()
    this.getMonthYear()
    this.getRooms()
    this.getEvents()
    
     
  }
};
</script>

<style scoped>
.shadow {
  padding: 0;
  box-shadow: 0 0 10px rgba(0,0,0,0.5);
}
.date{
  background-color: #b1b1da;
  color: #b12d1f;
  font-weight: bold;
}
tbody{
  background-color: white;
}
.title{
  text-align: center;
  color: darkblue;
  font-size: 17px;
  font-weight: bold;
  background-color: #d9edf7;
  padding-top: 10px;
}
.title p{
  width: 150px;
  display: inline-table;
}
.title button{
  background-color: #d9edf7;
  border-color: #d9edf7;
}
.day{
  cursor: pointer;
  width: 122.61px;
  height: 118.33px;
}
td:hover{
  background: #c7e3f1;
}
.ru-en-btn{
  margin-bottom: 15px;
}
.mon-sun-btn{
  margin-bottom: 15px;
  width: 115px;
}
.right-top-menu{
  height: 100px;
}
.btn-Book-Emp{
  height: 300px;
}
.btn-Book-Emp button{
  margin-top: 70px;
  width: 115px;
}
.btnRoom{
  border-radius: 0;
}
.selBtn{
    color: red;
}
.rooms{
  margin: 0;
  background-color: #d9edf7;
  text-align: center;
}
.roomSel{
  margin: 0;
  background-color: #d9edf7;
  text-align: center;
  color: darkblue;
  font-size: 18px;
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
.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}
.exit
{
float:right;
}
.link
{
  font-size:15px;
}




</style>
