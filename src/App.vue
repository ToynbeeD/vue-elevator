<template>
  <ul id="app">
    <my-floor
      v-for="floor in floors"
      :key="floor.number"
      :floor-data="floor"
      :elevators="elevators"
      :floors-line="floorsLine"
      @callElevator="callElevator"
    >
    </my-floor>
  </ul>
</template>

<script>
import MyFloor from '@/components/MyFloor.vue'
export default {
  components: { MyFloor },
  data () {
    return {
      floorsCount: 6,
      elevatorsCount: 4,

      floorsArray: [],
      elevatorsArray: [],
      floorsLine: []
    }
  },
  computed: {
    floors () {
      return this.floorsArray
    },
    elevators () {
      return this.elevatorsArray
    },
    nextFloorInLine () {
      return this.floorsArray.find(floor => floor.number === this.floorsLine[0])
    }
  },
  methods: {
    createFloor (number) {
      return {
        number,
        isActiveFloor: false
      }
    },
    createElevator (id) {
      return {
        id,
        appStartFloor: 1,
        currentFloor: 1,
        targetFloor: 1,
        isElevatorReloading: false,
        isElevatorFree: true
      }
    },
    saveElevatorToStorage (elevatorObj, floor) {
      const elevatorsArr = this.elevatorsArray.map(elevator => {
        return {
          ...elevator,
          appStartFloor: elevator.currentFloor,
          targetFloor: elevator.currentFloor,
          isElevatorFree: true,
          isElevatorReloading: false
        }
      })
      const currentElevator = elevatorsArr.find(elevator => elevator.id === elevatorObj.id)
      currentElevator.appStartFloor = floor
      localStorage.setItem('elevatorsState', JSON.stringify(elevatorsArr))
    },
    getFreeElevator (floorNumber) {
      const freeElevators = this.elevators.filter(elevator => elevator.isElevatorFree && !elevator.isElevatorReloading)
      if (freeElevators.length === 0) return null
      if (freeElevators.length === 1) return freeElevators[0]
      return freeElevators.reduce((prev, curr) => {
        return (Math.abs(curr.targetFloor - floorNumber) < Math.abs(prev.targetFloor - floorNumber) ? curr : prev)
      })
    },
    async callElevator (floorData) {
      const freeElevator = this.getFreeElevator(floorData.number)
      floorData.isActiveFloor = true
      if (!freeElevator) {
        this.floorsLine.push(floorData.number)
        localStorage.setItem('floorsLineState', JSON.stringify(this.floorsLine))
        return
      }

      const freeElevatorElement = document.querySelector(`[data-elevator="${freeElevator.id}"]`)
      const floorElement = document.querySelector(`[data-floor="${floorData.number}"]`)
      const direction = freeElevator.targetFloor < floorData.number
      freeElevator.isElevatorFree = false
      freeElevator.targetFloor = floorData.number

      await this.moveElevator(freeElevatorElement, floorElement, direction)

      floorData.isActiveFloor = false

      freeElevator.currentFloor = floorData.number
      freeElevator.isElevatorFree = true
      this.saveElevatorToStorage(freeElevator, floorData.number)

      await this.elevatorReloading(freeElevator)

      if (this.floorsLine.length > 0) {
        this.callElevator(this.nextFloorInLine)
        this.floorsLine.splice(0, 1)
        localStorage.setItem('floorsLineState', JSON.stringify(this.floorsLine))
      }
    },
    moveElevator (elevator, floor, direction) {
      const elevatorBottomValue = elevator.style.bottom
      let elevatorBottomNumber = Number(elevatorBottomValue.substring(0, elevatorBottomValue.length - 2))

      return new Promise((resolve) => {
        setTimeout(function elevatorStep () {
          elevatorBottomNumber = direction ? elevatorBottomNumber + 1 : elevatorBottomNumber - 1
          elevator.style.bottom = `${elevatorBottomNumber}px`

          // делаем проверку, что координата bottom лифта ещё не сравнялась с координатой этажа
          if (elevator.getBoundingClientRect().bottom + 1 !== floor.getBoundingClientRect().bottom) {
            return new Promise(() => {
              setTimeout(elevatorStep, 7)
            })
          }
          resolve()
        }, 7)
      })
    },
    elevatorReloading (elevator) {
      elevator.isElevatorReloading = true
      return new Promise((resolve) => {
        setTimeout(() => {
          elevator.isElevatorReloading = false
          resolve()
        }, 3000)
      })
    }
  },
  created () {
    for (let i = 1; i <= this.floorsCount; i++) {
      this.floorsArray.push(this.createFloor(i))
    }

    if (localStorage.elevatorsState) {
      this.elevatorsArray = JSON.parse(localStorage.getItem('elevatorsState'))
    } else {
      for (let i = 1; i <= this.elevatorsCount; i++) {
        this.elevatorsArray.push(this.createElevator(i))
      }
    }
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: Arial, Helvetica, sans-serif;
}

#app {
  display: flex;
  flex-direction: column-reverse;
  width: 100vw;
}

ul {
  list-style: none;
}
</style>
