<template>
   <div id="appointment">
      <div class="appointment-name">{{appointment.name}} @ {{time}}</div>
   </div>
</template>

<script>
export default {
   data() {
      return {
         appointmentName: '',
         appointmentTime: '',
      }
   },
   props: {
      appointment: Object,
      time: String
   },
}
</script>

<style scoped>
#appointment {
   background:#dc9cb7;
   border: 1px solid black;
   display: flex;
   justify-content: space-between;
   padding: 0 10px;
}
#appointment:hover {
   cursor: pointer;
   background: #eadee3;
}
</style>