<template>
   <div class="modal-mask">
      <div class="modal-wrapper">
         <div id="modal">
            <slot>
            </slot>

            <div @click="$emit('close')" class="close-btn btn">
               x
            </div>
         </div>
      </div>
   </div>
</template>

<script>
export default {

}
</script>

<style scoped>
.modal-mask {
   position: fixed;
   z-index: 9998;
   top: 0;
   left: 0;
   width: 100%;
   height: 100%;
   background-color: rgba(0, 0, 0, .5);
   display: table;
}

.modal-wrapper {
   display: table-cell;
   vertical-align: middle;
}

#modal {
   width: 400px;
   margin: 0px auto;
   text-align:left;
   background: white;
   padding: 20px;
   padding-top: 30px;
   position: relative;
}

.modal-entry {
   display: flex;
   justify-content: space-between;
   margin-bottom: 15px;
}

.close-btn {
   position: absolute;
   right: 5px;
   top: 5px;
}
</style>