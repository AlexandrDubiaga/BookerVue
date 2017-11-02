<template>
    <div>
      {{message}}
    </div>
        
</template>

<script>
import axios from 'axios'


export default {
  name: 'AddAppointment',
  data () {
    return {
      message:"Hello Appointment"
    }
  }
}
</script>

<style scoped>

</style>