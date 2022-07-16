<template>
  <li class="floor" :data-floor="floorData.number">
    <div class="shaft" v-for="elevator in elevators" :key="elevator.id">
      <my-elevator
        v-if="elevator.appStartFloor === floorData.number"
        :elevator="elevator"
      />
    </div>
    <span class="floor__number">{{ floorData.number }}</span>
    <button class="floor__btn" :class="{ active: floorData.isActiveFloor }" @click="callElevator"></button>
  </li>
</template>

<script>
import MyElevator from '@/components/MyElevator.vue'

export default {
  components: { MyElevator },
  props: {
    floorData: Object,
    elevators: Array,
    floorsLine: Array
  },
  computed: {
    floorInTarget () {
      return this.elevators.filter(elevator => elevator.targetFloor === this.floorData.number)
    },
    isFloorInLine () {
      return this.floorsLine.find(floorNumber => floorNumber === this.floorData.number)
    }
  },
  methods: {
    callElevator () {
      if (this.floorInTarget.length > 0) return
      if (this.isFloorInLine) return
      this.$emit('callElevator', this.floorData)
    }
  }
}
</script>

<style scoped>
.floor {
  display: flex;
  padding-left: 20px;
  align-items: center;
  height: 150px;
  width: 100%;
  border-top: 1px solid #d1d1d1;
}

.floor:first-child {
  border-bottom: 1px solid #d1d1d1;
}

.shaft {
  margin-right: 20px;
  position: relative;
  width: 150px;
  height: 100%;
}

.shaft::after {
  content: "";
  position: absolute;
  top: -1px;
  left: 0;
  width: 100px;
  bottom: 0;
  width: 100%;
  background-color: transparent;
  border-right: 2px solid #000;
  border-left: 2px solid #000;
}

span {
  margin-right: 7px;
  font-size: 20px;
}

.floor__btn {
  align-self: center;
  width: 30px;
  height: 30px;
}

.floor__btn {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
  margin: 0;
  width: 30px;
  height: 30px;
  background-color: #fff;
  border: 2px solid #000;
  border-radius: 10%;
  cursor: pointer;
  transition: border-color, .2s linear;
}

.floor__btn::after {
  content: "";
  display: block;
  width: 10px;
  height: 10px;
  background-color: #000;
  border-radius: 100%;
  transition: background-color, .2s linear;
}

.floor__btn.active {
  border-color: orange;
  cursor: default;
}

.floor__btn.active::after {
  background-color: orange;
}
</style>
