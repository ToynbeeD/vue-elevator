<template>
  <div class="shaft">
    <my-elevator v-if="elevator.currentFloor === floorData.number" :elevator="elevator"/>
  </div>
</template>

<script>
import MyElevator from '@/components/MyElevator.vue'

export default {
  components: { MyElevator },
  props: {
    elevator: Object
  }
}
</script>
