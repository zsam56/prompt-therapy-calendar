<template>
  <div id="app">
     <div class="top-wrapper">
        <select @change="handleNewMonth($event)" >
           <option v-for="(month, index) in months" :key="month" :value="index" :selected="month == currMonthName">{{month}}</option>
         </select>
         <h1 class="header">{{currMonthName}}</h1>
         <div class="view-selections">
            <div class="month selection" @click="view='Month'" :class="{selected: view=='Month'}">Month</div>
            <div class="week selection" @click="view='Week'" :class="{selected: view=='Week'}">Week</div>
         </div>
     </div>

      <div id="calendar">

         <div class="month-view calendar-container" v-if="view=='Month' && weatherDone">
            <div v-for="(week, i) in weeks" :key="i" class="week-wrapper">
               <day-square
                  v-for="(day, index) in week"
                  :key="index"
                  :day="day"
                  :month="month"
                  :forecast="getForecast(day)"
                  :users="users"> 
               </day-square>
            </div>
         </div>

         <div class="week-view calendar-container" v-if="view=='Week' && weatherDone">
            <div class="week-wrapper">
               <day-square
                  v-for="(day, index) in currWeek"
                  :key="index"
                  :day="day"
                  :month="month"
                  :forecast="getForecast(day)"
                  :users="users"> 
               </day-square>
            </div>
         </div>
      </div>
   </div>
</template>

<script>
import DaySquare from './components/DaySquare';
import * as moment from 'moment';
import axios from 'axios';

export default {
   name: 'app',
   components: {
      DaySquare
   },
   data() {
      return {
         weeks: [],
         days: [], //list of date objects in the current month
         users: [
            {id: 1, name: 'Doug'},
            {id: 2, name: 'John'},
            {id: 3, name: 'Kelly'},
            {id: 4, name: 'Sam'}
         ],
         month: 0,
         appointmentModal: false,
         view: 'Month', //current view between Month and Week
         weatherList: [],
         weatherDone: false,
         months: ['January', 'February', 'March', 'April',
         'May', 'June', 'July', 'August', 'September', 'October',
         'November', 'December'],
      }
   },
   created() {
      this.month = moment().month();
      this.getDays();
      this.getWeather();
   },
   computed: {
      currMonthName() {
         return this.months[this.month];
      },
      currWeek() {
         return this.weeks[Math.ceil(moment().date()/7)];
      }
   },
   methods: {
      daysInWeek(index){
         return this.days.slice((index - 1) * 7, index * 7);
      },
      //Organize days into a list of weeks
      getDays() {
         this.weeks = [];

         let startWeek = moment().month(this.month).startOf('month').week();
         let endWeek = moment().month(this.month).endOf('month').week();
         //endWeek gets set to 1 for December so manually setting that to 53 here
         if (endWeek == 1) {
            endWeek = 53;
         }

         while (startWeek<=endWeek) {
            let week = Array(7);
            week.fill(0);
            week = week.map((n, i) => {
               //get the start of the week and add i days
               let day = moment().week(startWeek).startOf('week').add(i, 'days');
               return day;
            });
            this.weeks.push(week);
            startWeek++;
         }

      },
      //used by the Week View to get the current week
      getDaysInWeek() {
         this.days = [];
         let start = moment().startOf('week').toDate();
         let end = moment().endOf('week').toDate();

         while (start <= end) {
            this.days.push(new Date(start));
            start.setDate(start.getDate() + 1);
         }
      },
      getForecast(day) {
         let date = moment(day);
         for (let w of this.weatherList) {
            let wDT = moment(w.dt_txt);
            if (moment(wDT).dayOfYear() == moment(date).dayOfYear()) {
               return w;
            }
         }
      },
      getWeather() {
         let url = 'http://api.openweathermap.org/data/2.5/forecast?zip=07601,us&APPID=d4ecd101c320520ba5f972f9f605fcbc';
         let self = this;
         axios.get(url)
            .then(function (response) {
               self.weatherList = response.data.list;
            }).catch(function (error) {
               console.log(error);
            }).finally(() => {
               self.weatherDone = true;
            });
      },
      handleNewMonth(event) {
         this.month = parseInt(event.target.value);
      }
   },
   watch: {
      month() {
         this.getDays();
      }
   }
}
</script>

<style>
#app {
   font-family: 'Avenir', Helvetica, Arial, sans-serif;
   -webkit-font-smoothing: antialiased;
   -moz-osx-font-smoothing: grayscale;
   text-align: center;
   color: #2c3e50;
   margin-top: 60px;

   width: 90%;
   margin: 0 auto;
}

#calendar {
   margin-top: 30px;
} 

.btn:hover {
   cursor: pointer;
}

.top-wrapper {
   display: flex;
   align-items: baseline;
   justify-content: space-between;
}

.header {
   /* flex: 1; */
   margin: 0;
}

ul {
   list-style: none;
}

.week-wrapper {
   display: flex;
   border-bottom: 1px solid black;
   height: 20%;
}

.view-selections {
   display: flex;
   border: 1px solid black;
   border-radius: 5px;
}

.calendar-container {
   height: 800px;
}

.selection {
   padding: 5px;
}
.selection:hover {
   cursor: pointer;
}
.selection.selected {
   background: #2f80ed;
   color: white;
}
.selection:first-child {
   border-right: 1px solid black;
}
</style>
