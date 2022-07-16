<template>
<transition name="fade">
  <div class="elevator" :class="{ reload: elevator.isElevatorReloading }" :data-elevator="elevator.id">
    <div class="elevator__info" :class="{ down: elevator.currentFloor > elevator.targetFloor }" v-if="!elevator.isElevatorFree">
      <img src="@/assets/arrow.png" alt="">
      <span>{{ elevator.targetFloor }}</span>
    </div>
  </div>
</transition>
</template>

<script>
export default {
  props: {
    elevator: Object
  }
}
</script>

<style scoped>
@keyframes fade {
  0% {
    opacity: 1;
  }
  50% {
    opacity: .5;
  }
  100% {
    opacity: 1;
  }
}
.elevator {
  padding: 15px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  position: absolute;
  left: 2px;
  bottom: 0;
  width: 100%;
  height: 100%;
  background-color: blue;
}
.elevator.reload {
  animation: fade 1s infinite;
}

.elevator__info {
  margin: 0 auto;
  padding: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #fff;
  border-radius: 10%;
}

img {
  width: 30px;
  height: auto;
}

.down img {
  transform: rotate(180deg);
}

span {
  font-size: 24px;
  font-weight: bold;
  line-height: 100%;
}
</style>
