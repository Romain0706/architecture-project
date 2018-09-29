<template>
  <div class="home-container">
    <div class="settings-container">
      <label for="customers">Customers :</label>
      <input id="customers" type="number" v-model="customersAmount" :disabled="!firstRun">
      <label for="sleep-time">Delay (ms) :</label>
      <input id="sleep-time" type="number" v-model="sleepTime">
      <p class="time-unit">Time Unit : {{ timeUnit }}</p>
      <label for="logs">Show logs :</label>
      <input id="logs" type="checkbox" v-model="showLogs"><br/>
      <div class="log-container" v-if="showLogs">
        <p v-for="(log, logIndex) in logs" :key="logIndex + 'log'">{{ log }}</p>
      </div>
      <button @click="start()" v-if="!isRunning">START</button>
      <button @click="stop()" v-if="isRunning">STOP</button>
      <button @click="reset()">RESET</button>
    </div>
    <div class="app-container">
      <div class="shop-container">
        <div class="shop-column" v-for="(column, columnIndex) in shop" :key="columnIndex + 'column'">
          <div class="shop-row" v-for="(row, rowIndex) in column" :key="rowIndex + 'row'">
            <div class="zone" :style="{ backgroundColor: row.color }"></div>
          </div>
        </div>
        <div class="robot" v-for="(robot, robotIndex) in robots" :style="getPosition(robotIndex)" :key="robotIndex + 'robot'"></div>
      </div>
    </div>
  </div>
</template>

<style>
.robot {
  position: absolute;
  width: 10px;
  height: 10px;
  background-color: black;
  border-radius: 100%;
}

.time-unit {
  margin: 10px 0;
}

label {
  margin-right: 5px;
}

button {
  margin-top: 5px;
  margin-right: 5px;
}

.log-container {
  margin-top: 5px;
  height: 200px;
  overflow: scroll;
  border: 1px solid black;
  overflow-x: hidden;
}

.settings-container {
  padding: 10px;
}

.app-container {
  position: relative;
  margin-left: 80px;
  margin-top: 80px;
}

.shop-container {
  box-sizing: border-box;
  margin: 5px;
}

.shop-column {
  display: flex;
}

.zone {
  box-sizing: border-box;
  width: 100px;
  height: 100px;
  margin: 5px;
}
</style>

<script>
export default {
  name: 'Home',
  data () {
    return {
      showLogs: true,
      isRunning: false,
      firstRun: true,
      customersAmount: 250,
      customersInitialAmount: 0,
      customersWaiting: 0,
      sleepTime: 250,
      timeUnit: 0,
      logs: [],
      colors: [
        { name: '#86aace', positions: [{ x: 0.5, y: 0.5 }, { x: 3.5, y: 3.5 }] },
        { name: '#f09f61', positions: [{ x: 1.5, y: 0.5 }, { x: 2.5, y: 3.5 }] },
        { name: '#35bf4d', positions: [{ x: 2.5, y: 0.5 }, { x: 1.5, y: 3.5 }] },
        { name: '#ff7373', positions: [{ x: 3.5, y: 0.5 }, { x: 0.5, y: 3.5 }] },
        { name: '#f8f9a2', positions: [{ x: 0.5, y: 1.5 }, { x: 3.5, y: 2.5 }] },
        { name: '#003366', positions: [{ x: 1.5, y: 1.5 }, { x: 2.5, y: 2.5 }] },
        { name: '#ded9df', positions: [{ x: 2.5, y: 1.5 }, { x: 1.5, y: 2.5 }] },
        { name: '#c6d8f1', positions: [{ x: 3.5, y: 1.5 }, { x: 0.5, y: 2.5 }] }
      ],
      shop: [
        [
          // first row
          { color: '#86aace', references: 100000 },
          { color: '#f09f61', references: 100000 },
          { color: '#35bf4d', references: 100000 },
          { color: '#ff7373', references: 100000 }
        ],
        [
          // second row
          { color: '#f8f9a2', references: 100000 },
          { color: '#003366', references: 100000 },
          { color: '#ded9df', references: 100000 },
          { color: '#c6d8f1', references: 100000 }
        ],
        [
          // third row
          { color: '#c6d8f1', references: 100000 },
          { color: '#ded9df', references: 100000 },
          { color: '#003366', references: 100000 },
          { color: '#f8f9a2', references: 100000 }
        ],
        [
          // first row
          { color: '#ff7373', references: 100000 },
          { color: '#35bf4d', references: 100000 },
          { color: '#f09f61', references: 100000 },
          { color: '#86aace', references: 100000 }
        ]
      ],
      robots: [
        // top
        { startPosition: { x: 0, y: -0.5 }, actualPosition: { x: 0, y: -0.5 } },
        { startPosition: { x: 1, y: -0.5 }, actualPosition: { x: 1, y: -0.5 } },
        { startPosition: { x: 2, y: -0.5 }, actualPosition: { x: 2, y: -0.5 } },
        { startPosition: { x: 3, y: -0.5 }, actualPosition: { x: 3, y: -0.5 } },
        { startPosition: { x: 4, y: -0.5 }, actualPosition: { x: 4, y: -0.5 } },
        // bottom
        { startPosition: { x: 0, y: 4.5 }, actualPosition: { x: 0, y: 4.5 } },
        { startPosition: { x: 1, y: 4.5 }, actualPosition: { x: 1, y: 4.5 } },
        { startPosition: { x: 2, y: 4.5 }, actualPosition: { x: 2, y: 4.5 } },
        { startPosition: { x: 3, y: 4.5 }, actualPosition: { x: 3, y: 4.5 } },
        { startPosition: { x: 4, y: 4.5 }, actualPosition: { x: 4, y: 4.5 } },
        // left
        { startPosition: { x: -0.5, y: 0 }, actualPosition: { x: -0.5, y: 0 } },
        { startPosition: { x: -0.5, y: 1 }, actualPosition: { x: -0.5, y: 1 } },
        { startPosition: { x: -0.5, y: 2 }, actualPosition: { x: -0.5, y: 2 } },
        { startPosition: { x: -0.5, y: 3 }, actualPosition: { x: -0.5, y: 3 } },
        { startPosition: { x: -0.5, y: 4 }, actualPosition: { x: -0.5, y: 4 } },
        // right
        { startPosition: { x: 4.5, y: 0 }, actualPosition: { x: 4.5, y: 0 } },
        { startPosition: { x: 4.5, y: 1 }, actualPosition: { x: 4.5, y: 1 } },
        { startPosition: { x: 4.5, y: 2 }, actualPosition: { x: 4.5, y: 2 } },
        { startPosition: { x: 4.5, y: 3 }, actualPosition: { x: 4.5, y: 3 } },
        { startPosition: { x: 4.5, y: 4 }, actualPosition: { x: 4.5, y: 4 } }
      ]
    }
  },
  methods: {
    checkCanMoveX (robot) {
      let nextMoveX = robot.actualPosition.x < robot.goalPosition.x ? robot.actualPosition.x + 0.5 : robot.actualPosition.x - 0.5
      if (nextMoveX === robot.goalPosition.x) {
        if (robot.actualPosition.y === robot.goalPosition.y) {
          return true
        } else {
          return false
        }
      } else if ((robot.actualPosition.y % 1) === 0) {
        return true
      } else {
        return false
      }
    },
    checkCanMoveY (robot) {
      let nextMoveX = robot.actualPosition.x < robot.goalPosition.x ? robot.actualPosition.x + 0.5 : robot.actualPosition.x - 0.5
      if (nextMoveX === robot.goalPosition.x) {
        return true
      } else if ((robot.actualPosition.x % 0.5) === 0) {
        return true
      } else {
        return false
      }
    },
    moveRobots () {
      let vm = this
      vm.robots.forEach(function (robot, robotIndex) {
        if ('request' in robot) {
          let hasMoved = false
          if (!((robot.actualPosition.x === robot.goalPosition.x) && (robot.actualPosition.y === robot.goalPosition.y))) {
            if (vm.checkCanMoveX(robot)) {
              robot.actualPosition.x = robot.actualPosition.x < robot.goalPosition.x ? robot.actualPosition.x + 0.5 : robot.actualPosition.x - 0.5
              vm.logs.push('Robots ' + robotIndex + ': move to x:' + robot.actualPosition.x + ' y:' + robot.actualPosition.y)
              hasMoved = true
            }
            if (!hasMoved) {
              if (vm.checkCanMoveY(robot)) {
                robot.actualPosition.y = robot.actualPosition.y < robot.goalPosition.y ? robot.actualPosition.y + 0.5 : robot.actualPosition.y - 0.5
                vm.logs.push('Robot ' + robotIndex + ': move to x:' + robot.actualPosition.x + ' y:' + robot.actualPosition.y)
                hasMoved = true
              }
            }
          } else {
            if ((robot.actualPosition.x === robot.startPosition.x) && (robot.actualPosition.y === robot.startPosition.y)) {
              delete robot.request
              delete robot.timeToFetch
              delete robot.goalPosition
              vm.customersAmount--
            } else {
              if (!('timeToFetch' in robot)) {
                robot.timeToFetch = 5
                vm.logs.push('Robot ' + robotIndex + 'starts to fetch')
              } else {
                if (robot.timeToFetch === 0) {
                  robot.goalPosition.x = robot.startPosition.x
                  robot.goalPosition.y = robot.startPosition.y
                } else {
                  robot.timeToFetch--
                  vm.logs.push('Robot ' + robotIndex + ' is fetching, ' + robot.timeToFetch + ' times left')
                }
              }
            }
          }
        }
      })
    },
    getPosition (robotIndex) {
      let top = this.robots[robotIndex].actualPosition.y * 110
      let left = this.robots[robotIndex].actualPosition.x * 110

      return 'top: ' + top + 'px; left: ' + left + 'px;'
    },
    setRequests () {
      let vm = this
      vm.robots.forEach(function (robot, robotIndex) {
        if (!('request' in robot)) {
          if (vm.customersWaiting < vm.customersInitialAmount) {
            let color = vm.colors[Math.floor(Math.random() * Math.floor(vm.colors.length))].name
            let references = Math.floor(Math.random() * Math.floor(1000))
            robot.request = { color: color, references: references }
            vm.logs.push('Setting request for robot ' + robotIndex + ' : color requested = ' + color + ', references requested = ' + references)
            vm.customersWaiting++
          }
        }
      })
    },
    setGoals () {
      let vm = this
      vm.robots.forEach(function (robot) {
        if (!('goalPosition' in robot) && ('request' in robot)) {
          let color = vm.colors.find(function (color) {
            return color.name === robot.request.color
          })
          let firstPosition = color.positions[0]
          let secondPosition = color.positions[1]
          let compareFirst = Math.abs(robot.actualPosition.x - firstPosition.x) + Math.abs(robot.actualPosition.y - firstPosition.y)
          let compareSecond = Math.abs(robot.actualPosition.x - secondPosition.x) + Math.abs(robot.actualPosition.y - secondPosition.y)
          robot.goalPosition = {}
          if (compareFirst > compareSecond) {
            robot.goalPosition.x = firstPosition.x
            robot.goalPosition.y = firstPosition.y
          } else {
            robot.goalPosition.x = secondPosition.x
            robot.goalPosition.y = secondPosition.y
          }
        }
      })
    },
    main () {
      let vm = this
      if (vm.isRunning) {
        vm.timeUnit++
        vm.setRequests()
        vm.setGoals()
        vm.moveRobots()
        setTimeout(function () {
          if (vm.customersAmount > 0) {
            vm.main()
          } else {
            vm.isRunning = false
            vm.firstRun = true
          }
        }, vm.sleepTime)
      }
    },
    start () {
      if (!this.isRunning && this.customersAmount > 0) {
        if (this.firstRun) {
          this.firstRun = false
          this.customersInitialAmount = this.customersAmount
        }
        this.isRunning = true
        this.main()
      }
    },
    stop () {
      this.isRunning = false
    },
    reset () {
      location.reload()
    }
  }
}
</script>
