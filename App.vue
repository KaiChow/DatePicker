<template>
  <div class="box">
     <DatePicker v-model="now"></DatePicker>
  </div>
</template>

<script>
import DatePicker  from './DatePicker'
export default {
  components:{
    DatePicker
  },
  data(){
    return {
      now: new Date()
    }
  },
}
</script>
