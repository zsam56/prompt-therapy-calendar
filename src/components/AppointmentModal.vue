<template>
   <modal @close="$emit('close')">
      <div id="appointment_modal">
         <div class="modal-entry">
            <label>Appointment Name</label>
            <input v-model="appointmentName"> 
         </div>
         <div class="modal-entry">
            <label>Appointment Time</label>
            <input type="time" v-model="appointmentTime" v-on:keyup.enter="$emit('add', appointmentName, appointmentTime)"> 
         </div>

         <label>Invite Other Users</label>
         <ul class="user-list">
            <li v-for="user in users" :key="user.id">
               <input type="checkbox" :value="user.id" v-model="invitedUsers">{{user.name}}
            </li>
         </ul>

         <button v-if="!editing" @click="$emit('add', appointmentName, appointmentTime, invitedUsers)">Add Appointment</button>
         <button v-else @click="$emit('edit', appointmentName, existingAppTime, appointmentTime, invitedUsers)">Edit</button>
         <button v-if="editing" @click="$emit('delete', existingAppTime)">Delete</button>
      </div>
   </modal>
</template>

<script>
import Modal from './Modal';

export default {
   data() {
      return {
         editing: false,
         appointmentName: this.existingAppName,
         appointmentTime: this.existingAppTime,
         invitedUsers: this.existingInvitedUsers
      }
   },
   props: {
      existingAppName: {
         default: '',
         type: String
      },
      existingAppTime: {
         default: '',
         type: String
      },
      existingInvitedUsers: {
         default: [],
         type: Array
      },
      users: Array
   },
   created() {
      if (this.existingAppName != '') {
         this.editing = true;
      }
   },
   methods: {
      onCheck(e) {
         console.log(e);
      }
   },
   components: {
      Modal
   }
}
</script>

<style scoped>

</style>