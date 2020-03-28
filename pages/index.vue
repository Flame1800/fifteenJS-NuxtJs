<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.14.1/lodash.min.js"></script>
<template>
  <section class="container">
    <div class="view">
      <h1 class="title">Fifteen</h1>
      <div class="menu">
        <div class="block">
          Ходов
          <br />
          <div class="num">{{ counter }}</div>
        </div>
        <div class="block">
          Время
          <br />
          <div class="num">{{ time }}</div>
        </div>
      </div>
    </div>
    <div class="game-box" ref="box">
      <div
        class="cell"
        v-for="(cell, index) in cells"
        :key="index"
        @click="move"
        ref="nums"
      >{{ cell }}</div>
    </div>
  </section>
</template>

<script>
import _ from "lodash";

export default {
  data() {
    return {
      model: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 0],
      cells: _.shuffle([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 0]),
      counter: 0,
      time: 0,
      activeCeil: ""
    };
  },
  mounted() {
    this.findZero();
    this.timeCounter();
  },
  methods: {
    timeCounter() {
      let m = 0;
      let s = 0;
      setInterval(() => {
        this.time = `0${m}:0${s}`;
        s++;
        if (s >= 10) this.time = `0${m}:${s}`;
        if (m >= 10) this.time = `${m}:${s}`;
        if (s === 60) {
          s = 0;
          m++;
          if (m >= 10) this.time = `${m}:0${s}`;
        }
      }, 1000);
    },
    findZero() {
      const nums = this.$refs.nums;
      nums.forEach(num => {
        if (num.textContent == 0) {
          num.classList.add("zero");
          this.activeCeil = num;
        }
      });
    },
    findCell(curr) {
      const nums = this.$refs.nums;
      nums.forEach((num, i) => {
        if (num === curr) {
          this.upCell = nums[i - 4];
          this.downCell = nums[i + 4];
          this.leftCell = nums[i - 1];
          this.rightCell = nums[i + 1];
        }
      });
    },
    checkOrderWin() {
      const nums = this.$refs.nums;
      const parent = nums;
      let counter = 0;
      nums.forEach((num, i) => {
        const currItem = num.textContent;
        const modelItem = this.model[i];
        if (currItem == modelItem) counter++;
      });
      if (counter === 16) {
        this.win();
      }
    },
    win() {
      const gameBox = this.$refs.box;
      gameBox.innerHTML = `<h1>You Win!</h1> <br>
      <a href='' class='restart'>Restart</a>`;
      gameBox.classList.add("win");
    },
    move(e) {
      const activeCeil = this.activeCeil;
      const currCeil = e.target;
      this.findCell(currCeil);

      const changeCeil = ceil => {
        ceil.classList.remove("zero");
        ceil.innerHTML = currCeil.textContent;
        currCeil.innerHTML = 0;
        currCeil.classList.add("zero");
        this.counter++;
      };

      const left = this.leftCell;
      const rigth = this.rightCell;
      const down = this.upCell;
      const up = this.downCell;
      const exceptionRight =
        this.$refs.nums[3] == currCeil ||
        this.$refs.nums[7] == currCeil ||
        this.$refs.nums[11] == currCeil;

      const exceptionLeft =
        this.$refs.nums[4] == currCeil ||
        this.$refs.nums[8] == currCeil ||
        this.$refs.nums[12] == currCeil;

      if (left === activeCeil && exceptionLeft === false) changeCeil(left);
      if (rigth === activeCeil && exceptionRight === false) changeCeil(rigth);
      if (down === activeCeil) changeCeil(down);
      if (up === activeCeil) changeCeil(up);
      this.findZero();
      this.checkOrderWin();
    }
  }
};
</script>

<style>
.container {
  justify-content: center;
  text-align: center;
  flex-wrap: wrap;
}

.win {
  background: #869cca;
  opacity: 0.8;
  height: 480px;
  font-size: 45px;
  color: #3a64c1;
  font-weight: 800;
  flex-direction: column;
  justify-content: center;
  text-decoration: none;
  flex-wrap: wrap;
}

.win .restart {
  margin: 0 auto;
  color: #3a64c1;
  background: #b5c8f1;
  font-size: 25px;
  text-decoration: none;
  font-weight: 300;
  padding: 20px;
  border-radius: 10px;
}

.cell {
  display: flex;
  border-radius: 30%;
  background: #53a1c5;
  height: 100px;
  width: 100px;
  text-decoration: none;
  justify-content: center;
  align-items: center;
  margin: 10px;
  box-shadow: 0 4px 4px rgba(0, 0, 0, 0.25), 0 5px 5px rgba(0, 0, 0, 0.22);
  font-size: 35px;
  font-weight: 400;
  color: #394865;
  cursor: pointer;
  user-select: none;
  transition: 0.5s opacity;
}

.zero {
  opacity: 0;
  color: #53a1c5;
  transition: none;
}
.view {
  width: 100%;
  margin-bottom: 15px;
}

.game-box {
  width: 520px;
  display: flex;
  flex-wrap: wrap;
  margin: 0 auto;
  background: #d4def3;
  padding: 20px;
  box-shadow: inset 0 14px 28px rgba(0, 0, 0, 0.25),
    0 10px 10px rgba(0, 0, 0, 0.22);
  border-radius: 20px;
}

.title {
  font-family: "Quicksand", "Source Sans Pro", -apple-system, BlinkMacSystemFont,
    "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif; /* 1 */
  display: block;
  font-weight: 500;
  font-size: 70px;
  color: #35495e;
  letter-spacing: 1px;
  margin: 10px;
}
.menu {
  display: flex;
  justify-content: center;

  border-radius: 30px;
}

.block {
  margin: 20px 10px;
  box-shadow: inset 0 10px 10px rgba(0, 0, 0, 0.22);
  padding: 15px;
  border-radius: 10%;
  font-weight: 500;
  font-size: 25px;
  color: #7789ae;
  background: #d4def3;
}

.num {
  color: #264380;
  margin-top: 10px;
  font-weight: 600;
}
</style>

