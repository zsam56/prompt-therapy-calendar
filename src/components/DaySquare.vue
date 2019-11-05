<template>
<div id="day_square" :class="{fade: notInCurrentMonth, highlight: isToday}">
   <span @click="displayAppModal()" class="add-button btn">+</span>
   <span class="date-text">{{getDay}}</span>

   <div class="weather-data" v-if="forecast">
      <div class="weather-txt">{{forecast.weather[0].description}}</div>
      <div class="weather-txt">
         Temp: {{tempKelvinToFarenheit(forecast.main.temp)}}<span>&#176;</span>F
      </div>
      <div class="weather-txt">
         Humidity: {{forecast.main.humidity}}%
      </div>
   </div>

   <appointment v-for="(value, key) in appointments"
      :key="key"
      :appointment="value"
      :time="key"
      @delete="handleDelete(key)"
      @click.native="displayAppModal(value, key)"
      ></appointment>

   <appointment-modal v-if="addingAppointment"
      :existingAppName="modalAppName"
      :existingInvitedUsers="modalAppInvitedUsers"
      :existingAppTime="modalAppTime"
      :users="users"
      @add="handleAdd"
      @edit="handleEdit"
      @delete="handleDelete"
      @close="addingAppointment=false"></appointment-modal>
</div>
</template>

<script>
import Appointment from './Appointment';
import AppointmentModal from './AppointmentModal';
import * as moment from 'moment';

export default {
    data: function () {
      return {
         appointments: {},
         addingAppointment: false,

         //for passing into the appointment modal
         modalAppName: '',
         modalAppTime: '',
         modalAppInvitedUsers: [],
      }
   },
   props: {
      day: Object,
      month: Number,
      users: Array,
      forecast: Object
   },
   components: {
      Appointment,
      AppointmentModal
   },
   computed: {
      getDay() {
         return moment(this.day).date();
      },
      notInCurrentMonth() {
         return moment(this.day).month() != this.month;
      },
      isToday() {
         return moment().dayOfYear() == moment(this.day).dayOfYear();
      }
   },
   methods: {
      //displays the modal for adding an appointment
      displayAppModal(app='', time='') {
         if (app != '') {
            this.modalAppName = app.name;
            this.modalAppInvitedUsers = app.users;
         }
         this.modalAppTime = time;
         this.addingAppointment = true;
      },

      //adds an appointment to the list of appointments for this day
      handleAdd(appointmentName, appointmentTime, invitedUsers) {
         if (appointmentName == '') {
            alert('You must enter a name for this appointment');
            return;
         }

         if (appointmentTime == '') {
            alert('You must enter a time for this appointment');
         }
         
         if (this.appointments[appointmentTime] != undefined) {
            alert('There is already an appointment at this time');
         } else {
            let appointment = {};
            appointment.name = appointmentName;
            appointment.users = invitedUsers;
            this.appointments[appointmentTime] = appointment;
            this.addingAppointment = false;
            this.modalAppName = '';
            this.modalAppTime = '';
         }
      },

      //handles editing an existing appointment
      handleEdit(appointmentName, oldTime, newTime, invitedUsers) {
         if (this.appointments[newTime] == this.appointments[oldTime]) {
            let appointment = {};
            appointment.name = appointmentName;
            appointment.users = invitedUsers;
            this.appointments[newTime] = appointment;
            this.addingAppointment = false;
            this.modalAppName = '';
            this.modalAppTime = '';
         } else {
            if (this.appointments[newTime] != undefined) {
               alert('There is already an appointment at this time');
            } else {
               delete this.appointments[oldTime];
               let appointment = {};
               appointment.name = appointmentName;
               appointment.users = invitedUsers;
               this.appointments[newTime] = appointment;
               this.addingAppointment = false;
               this.modalAppName = '';
               this.modalAppTime = '';
            }
         }
      },

      handleDelete(time) {
         delete this.appointments[time];
         this.addingAppointment = false;
         this.modalAppName = '';
         this.modalAppTime = '';
      },

      tempKelvinToFarenheit(kelvin) {
         let celsius = kelvin - 273;
         return Math.floor(celsius * (9/5) + 32);
      },
   },

   watch: {
      month() {
         this.appointments = {};
      }
   }
}
</script>

<style scoped>
#day_square {
   width: 15%;
   text-align: center;
   border-left: 1px solid black;

   position: relative;
   padding-top: 12px;
}

#day_square.highlight {
   border-top: 10px solid #2f80ed;
   padding-top: 2px;
}

#day_square:last-child {
   border-right: 1px solid black;
}

#day_square:not(.fade) .date-text {
   font-weight: bold;
}

#day_square.fade .date-text {
   opacity: .6;
}

.add-button{
   position: absolute;
   right: 5px;
   background: #c5c5c5;
   border-radius: 19px;
   font-size: 15px;
   width: 18px;
   color: white;
}

.add-button:hover {
   background-color: #737373;
}

.weather-data {
   position: absolute;
   bottom: 0;
   text-align: left;
   padding-left: 5px;
   padding-bottom: 5px;
   opacity: .6;
   font-size: 12px;
   text-transform: capitalize;
}
</style>