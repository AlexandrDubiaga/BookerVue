<template>
  <div class="container">
    <div class="row">
      <h3>Event for:{{room}}</h3>
      <div class="col-md-4 col-md-offset-4">
        <div class="col-md-12">
          <p class="text-left">1. Booked for:</p>
          <select v-if="admin" v-model="user" class="form-control">
            <option :value="user.id_user" v-for="user in usersArr">{{user.name}}</option>
          </select>
          <p class="text-left" v-if="!admin && userObj.id_user == user" v-for="userObj in usersArr">{{userObj.name}}</p>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-md-4 col-md-offset-4">
        <div class="col-md-12"><p class="text-left">2. I would like to book this meeting:</p></div>
        <div class="col-md-4">
          <select  v-model="month" class="form-control">
            <option :value="key"  v-for="(month,key) in monthArr">{{month}}</option>
          </select>
        </div>
        <div class="col-md-4">
          <select v-model="day" class="form-control">
            <option :value="key" v-for="(day,key) in days">{{day}}</option>
          </select>
        </div>
        <div class="col-md-4">
          <select v-model="year"  class="form-control">
            <option :value="key" v-for="(year,key) in yearsArr">{{year}}</option>
          </select>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-md-4 col-md-offset-4">
        <div class="col-md-12"><p class="text-left">3. Specify what the time and end of the meeting(This will be what people see on the calendar.)</p></div>
        <div class="col-md-4 marg">
          <select  v-model="timeStartHour" class="form-control">
            <option :value="hour"  v-for="hour in 12">{{hour}}</option>
          </select>
        </div>
        <div class="col-md-4 marg">
          <select v-model="timeStartMin" class="form-control">
            <option value="0" >0</option>
            <option :value="minute * 15" v-for="minute in 4">{{minute * 15}}</option>
          </select>
        </div>
        <div class="col-md-4 marg">
          <select v-model="timeStartMidnight"  class="form-control">
            <option value="0">AM</option>
            <option value="1">PM</option>
          </select>
        </div>
        <div class="col-md-4">
          <select  v-model="timeEndHour" class="form-control">
            <option :value="hour"  v-for="hour in 12">{{hour}}</option>
          </select>
        </div>
        <div class="col-md-4">
          <select v-model="timeEndMin" class="form-control">
            <option value="0" >0</option>
            <option :value="minute * 15" v-for="minute in 4">{{minute * 15}}</option>
          </select>
        </div>
        <div class="col-md-4">
          <select v-model="timeEndMidnight"  class="form-control">
            <option value="0">AM</option>
            <option value="1">PM</option>
          </select>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-md-4 col-md-offset-4">
        <div class="col-md-12">
          <p class="text-left">4. Enter the specifics for the meetings(This will be what peolpe see when they click on an. event link.)</p>
          <textarea v-model="desc" class="form-control" rows="5"></textarea>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-md-4 col-md-offset-4 text-left">
        <div class="col-md-12">
          <p >It's going to be recurring event?</p>
          <div class="radio">
            <label>
              <input v-model="recurring" type="radio" name="optionsRadios" id="optionsRadios1" :value="false" checked>
              No
            </label>
          </div>
          <div class="radio">
            <label>
              <input v-model="recurring" type="radio" name="optionsRadios" id="optionsRadios2" :value="true">
              Yes
            </label>
          </div>
        </div>
      </div>
    </div>
    <div v-if="recurring" class="row">
      <div class="col-md-4 col-md-offset-4">
        <div class="col-md-12 text-left">
          <p>5. If it's recurring specify weekly, bi-weekly, or monthly</p>
          <div class="radio">
            <label>
              <input v-model="recurringType" type="radio" name="recurringType" id="recurringType1" :value="1" checked>
              weekly
            </label>
          </div>
          <div class="radio">
            <label>
              <input v-model="recurringType" type="radio" name="recurringType" id="recurringType2" :value="2">
             bi-weekly
            </label>
          </div> 
          <div class="radio">
            <label>
              <input v-model="recurringType" type="radio" name="recurringType" id="recurringType3" :value="3">
            monthly 
            </label>
          </div>
          <p>6. If weekly or be weekly, specify number of weeks for it to keep recurring. If monthly, specify the number of months</p>
          <div class="col-md-7">
            <input v-model="recurringDuration" class="form-control" type="text">
            <label>duration(max 4 weeks)</label>
          </div>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-md-4 col-md-offset-4">
        <button v-on:click="bookRoom" type="button" class="btn btn-success">Send</button>
     </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'book',
  data () {
    return {
    msg:'book',
    room:'',
    roomId:'',
    usersArr:[],
    user:'1',
    yearsArr: ['2017','2018','2019'],
    monthArr: [
      "January", 
      "February", 
      "March", 
      "April", 
      "May", 
      "June", 
      "Julay", 
      "August", 
      "September", 
      "October", 
      "November", 
      "December"
    ],
    days:[],
    month:0,
    year:0,
    day:0,
    timeStartHour:1,
    timeEndHour:1,
    timeStartMin:0,
    timeEndMin:0,
    timeStartMidnight:0,
    timeEndMidnight:0,
    recurring:false,
    recurringType:1,
    recurringDuration:'',
    desc:'',
    admin:false
    }
  },
  created(){
    this.checkAdmin()
    this.getRooms()
    this.getUsers()
    this.setDateSelect()
  },
  methods: {
    setDateSelect(month=false){
      let date = new Date
      if(month){
        var mon = month + 1 
      }else{
        var mon = date.getMonth() + 1 
      }
      this.days = []
      let col = new Date(date.getFullYear(),mon , 0).getDate()
      for(let i = 1; i <= col; i++){
        this.days.push(i)
      }
    },
    getRooms(){
      let roomData = (data) => {
        for(let obj in data){
          if(this.$route.params.id_room == data[obj].id_room){
            this.room = data[obj].name
            this.roomId = data[obj].id_room  
          }
        }
      }
      getQuery(this,myPath + 'application/api/rooms/').then(roomData,null)
      /*let data = [
      {
        id_room:1,
        name:'room 1'
      },
      {
        id_room:2,
        name:'room 2'
      },
      {
        id_room:3,
        name:'room 3'
      }
      ]
      roomData(data)
  */
    },
    getUsers(){
      let succ = (result) => {
        this.usersArr = result
      }
      let fail = (result) => {
        console.log(result)
      }
     getQuery(this,myPath + 'application/api/users/').then(succ,fail)
     /* let data = [
        {
          id_user:1,
          name:'user 1'
        },
        {
          id_user:2,
          name:'user 2'
        },
        {
          id_user:3,
          name:'user 3'
        },
        {
          id_user:11,
          name:'user'
        }
      ]
      succ(data)*/
    }, 
    checkAdmin(){
      let adminCheck = (result) => {        
        if(result.id_role == "1"){
          this.admin = true
        }else{
          this.admin = false
          this.user = getCookie('id')
        }
      }
      getQuery(this,myPath + 'application/api/auth/').then(adminCheck,null)
      //adminCheck({id_role:'2'})
    },
    bookRoom(){
      let now = new Date()
      
      var timeStartH = this.timeStartHour
      var timeEndH = this.timeEndHour
      if(this.timeStartMidnight == '1'){
        timeStartH += 12
      }
      
      if(this.timeEndMidnight == '1'){
        timeEndH += 12
      }
      let eventStart = new Date(this.yearsArr[this.year],this.month,this.days[this.day],timeStartH,this.timeStartMin)
      let eventEnd = new Date(this.yearsArr[this.year],this.month,this.days[this.day],timeEndH,this.timeEndMin)
      let data = {
        roomId:this.$route.params.id_room,
        user:this.user,
        dayCreation: new Date(now.getFullYear(), now.getMonth(), now.getDate()).getTime() / 1000,
        startEvent:eventStart.getTime() / 1000,
        endEvent:eventEnd.getTime() / 1000,
        desc:this.desc,
        recurring:this.recurring,
      }
      if(data.recurring){
        data.recType = this.recurringType
        data.recDuration = this.recurringDuration
      }
      let eventGet = (result) =>{
        console.log(result)
      }
      postQuery(this,myPath + 'application/api/events/',data).then(eventGet,null)
      console.log(data)
    }
  },
  watch:{
    month(newMonth){
      this.setDateSelect(newMonth)
    
    }
  
  }
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