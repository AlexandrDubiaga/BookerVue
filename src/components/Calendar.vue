<template>
  <div class="main container-fluid"  v-if="checkUser >0">
    <div class="Boardroom">
      <button>Boardroom 1 </button>
      <button>Boardroom 2 </button>
      <button>Boardroom 3 </button>
      <button v-on:click="logoutFun()" type="submit" class="btn btn-primary exit">Exit</button>
     
    </div>

    <!--<div class="ch-year ">
      <button class="btn btn-primary"v-on:click="counter  = 2">First day Su</button>
      <button class="btn btn-primary" v-on:click="counter  = 1">First day Mon</button>
    </div>-->
  
    <div class="head solo"><h1>Boardroom Booker</h1></div>
      <div class="head"><b class="ltMonth" @click="ltMonth">«</b><b>{{months[currMonth]}} {{currYear}}</b><b class="gtMonth" @click="gtMonth">»</b></div>
    <div id="app" class="col-md-9 ">
      <div id="calendar">
      
          <div v-if="counter == 2">
          <div class="week"><b v-for="day in daysSun">{{day}}</b></div>
              <div class="days">
                <time v-if="nullWeek !==7" v-for="blank in nullWeek">&nbsp;</time>
                <time v-for="i in daysInMonth" :class="{currDay: i == currDay}"> 
                {{i}}
                </time>
              </div>
          </div>
          <div v-else="counter == 1">
          <div class="week"><b v-for="day in daysMon">{{day}}</b></div>
              <div class="days">
                <time v-for="blank in nullWeek1">&nbsp;</time>
                <time v-for="i in daysInMonth" :class="{currDay: i == currDay}"> 
                  {{i}} 
                </time>
              </div>
          </div>
      </div>
    </div>
    <div class="col-md-3">
      <div class="row">
        <div class="col-md-8 booker-but">
        
         <button>Book it</button>
        </div>
        <div class="col-md-8 booker-but">
         <router-link to='/employees'><button  v-if="checkUser == 2">Employee List</button></router-link>
        </div>
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
      checkUser: 0,
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
      }
    };
  },
   methods: {
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
        //axios.get('http://192.168.0.15/~user2/Booker/client/api/users/' + self.user.id)
       axios.get('http://BoardroomBooker/user2/Booker/client/api/users/' + self.user.id)
            .then(function (response) {
             
                if (self.user.hash === response.data[0].hash)
                {
                  if(response.data[0].role == "admin")
                  {
                    self.checkUser = 2;
                    self.role = response.data[0].role
                         console.log( self.checkUser)

                    return true
                  }
                   if(response.data[0].role == "user")
                  {
                    self.checkUser = 1;
                    self.role = response.data[0].role
                      console.log( self.checkUser)
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
    

    ltMonth() {
      var self = this
      
      self.inst_date = new Date( self.currYear, self.currMonth-1 )
    },
    gtMonth() {
      var self = this
      self.inst_date = new Date( self.currYear, self.currMonth+1 )
    }
  },
    computed: {    
    currYear() {
      var self = this
      return self.inst_date.getFullYear()
    },
    currMonth() {
      var self = this
      return self.inst_date.getMonth()
    },
    currWD() {
      var self = this
      return self.inst_date.getDay()
    },
    currDay() {
      var self = this
      const now = new Date();
      if ( self.inst_date.getMonth() == now.getMonth() && self.inst_date.getFullYear() == now.getFullYear() ) {
        return now.getDate()
      } else return
    },
    daysInMonth() {
      var self = this
      return new Date(self.currYear, self.currMonth+1, 0).getDate();
    },
    nullWeek() {
      var self = this
      var res =  new Date(self.currYear, self.currMonth, 0).getDay()+1;
      return res
    },
    nullWeek1() {
       var self = this
       var res = new Date(self.currYear, self.currMonth, 0).getDay();
       if(res == 0) {
         res = 1
       }
       return res
    }
  },
  components: {
  },
  created(){
    this.checkUserFun()
  }
};
</script>

<style scoped>
.booker-but {
  margin-top: 50px;
}
.boardroom-list {
  margin-top: 5px;
  margin-bottom: 2px;

}
.boardroom-list button {
  margin-left:30px;
  margin-right:30px;
}
.ch-year {
 padding-bottom: 20px;
}
.ch-year button {
 margin-left:20px;
  margin-right:20px;
}
#app {
  margin: 0 auto;
   display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  font-family: sans-serif;
}

.week,
.days {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
}
#calendar {
 background:#8FBC8F;
  width: 995px;
  text-align: center;
  padding-bottom: 20px;
  padding-left: 20px;
  margin-left: 10%;
}
.week {
  border-bottom: 1px solid rgba(204,204,204,0.3);
  line-height: 2em;
  font-size: 20px;
  color: #0C0C0C;
}
.week b {
  font-weight: normal;
  color: #546A8C;
  width: 155px;
  height: 50px;

}
.days {
  -ms-flex-wrap: wrap;
      flex-wrap: wrap;
  line-height: 40px;
  text-align: left;
  font-size: 20px;
  color: #0C0C0C;
}
time {
  width: 137px;
  height: 100px;
  border: 1px solid #D4D4D4;
}
.currDay {
  background: #B9B9B9;
  border: 1px solid #546A8C;
}
.head {

  -webkit-box-pack: justify;
      -ms-flex-pack: justify;
          justify-content: space-between;
  line-height: 40px;
  height: 60px;
  font-size: 20px;
  margin-left:-1300px;
}
.ltMonth,
.gtMonth {
  cursor: pointer;
  padding: 0 1em;
  background: rgba(238,238,238,0.3);
  font-size: 20px;
}
.ltMonth:hover,
.gtMonth:hover {
  background: rgba(238,238,238,0.2);
  color: #f00;
  font-size: 24px;
}
.Boardroom
{
  background:#00FF7F;
}
.exit
{
margin-left:300px;
}




</style>
