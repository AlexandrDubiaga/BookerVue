<template>
  <div class="main container-fluid">
<button v-on:click="counter  = 1">go</button>
  <div id="app" class="col-md-9 col-md-offset-3">
  <div id="calendar">
    <div class="head"><b class="ltMonth" @click="ltMonth">«</b><b>{{months[currMonth]}} {{currYear}}</b><b class="gtMonth" @click="gtMonth">»</b></div>
      <div v-if="counter == 1">
      <div class="week"><b v-for="day in daysSun">{{day}}</b></div>
          <div class="days">
            <time v-if="nullWeek !==7" v-for="blank in nullWeek">&nbsp;</time>
            <time v-for="i in daysInMonth" :class="{currDay: i == currDay}"> 
              <div @click="showEvents(i)">
         {{i}}
      </div>
            </time>
          </div>
      </div>
      <div v-else="counter1 == 2">
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
    
  </div>

</template>

<script>
import axios from "axios";
export default {
  name: "Main",
  data() {
    return {
      counter: '',
      counter1: '',
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
 
  created() {
  },
    computed: {    
    currYear() {
      return this.inst_date.getFullYear()
    },
    currMonth() {
      return this.inst_date.getMonth()
    },
    currWD() {
      return this.inst_date.getDay()
    },
    currDay() {
      const now = new Date();
      if ( this.inst_date.getMonth() == now.getMonth() && this.inst_date.getFullYear() == now.getFullYear() ) {
        return now.getDate()
      } else return
    },
    daysInMonth() {
      return new Date(this.currYear, this.currMonth+1, 0).getDate();
    },
    nullWeek() {
      var res =  new Date(this.currYear, this.currMonth, 0).getDay()+1;
      return res
    },

    nullWeek1() {
      return new Date(this.currYear, this.currMonth, 0).getDay();
    }
  },
  methods: {
    showEvents(day) {
      alert(day)
    },
    
    showEvents(day) {
      alert(day)
    },
    ltMonth() {
      this.inst_date = new Date( this.currYear, this.currMonth-1 )
    },
    gtMonth() {
      this.inst_date = new Date( this.currYear, this.currMonth+1 )
    }
  },

  components: {
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main {
 

}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  font-family: sans-serif;

}
#app {
  margin: 0 auto;
  
}
.head,
.week,
.days {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
}
.main
{
  background:pink;
  height:2000px;
}
#calendar {
  box-shadow: 0 1em 10em -2em #000;
  width: 985px;
  text-align: center;
  padding-bottom: 20px;
  padding-left: 20px;
  margin-left: 20%;
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
  width: 145px;
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
  width: 135px;
  height: 80px;
  border: 1px solid #D4D4D4;
}
.currDay {
  background: #B9B9B9;
  border: 1px solid #546A8C;
}
.head {
  background: rgba(238,238,238,0.3);
  -webkit-box-pack: justify;
      -ms-flex-pack: justify;
          justify-content: space-between;
  line-height: 40px;
  height: 60px;
  font-size: 20px;
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



</style>
