<template>
  <div class="container">
    <div class="side-bar">
      <button v-on:click="generateArray(100, 5, 600)">
        Generate new Array
      </button>
      <button v-on:click="bubbleSort(arr)">Bubble Sort</button>
    </div>
    <div class="content">
      <div
        class="arr-bar"
        v-for="(item, index) in arr"
        :key="index"
        v-bind:style="{ height: (item / maxNum) * 99 + '%' }"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Sorting",
  created: function () {
    this.generateArray(100, 5, 600);
  },
  data: function () {
    return {
      arr: [],
      maxNum: 0,
    };
  },
  methods: {
    generateArray: function (length, min, max) {
      this.arr = [];
      this.maxNum = -Infinity;
      for (let i = 0; i < length; i++) {
        let temp = this.randNumber(min, max);
        this.arr.push(temp);
        this.maxNum = Math.max(this.maxNum, temp);
      }
    },

    // From https://stackoverflow.com/questions/4959975/generate-random-number-between-two-numbers-in-javascript
    randNumber: function (min, max) {
      return Math.floor(Math.random() * (max - min + 1) + min);
    },

    swap: function (arr, indexA, indexB) {
      let temp = arr[indexA];
      arr[indexA] = arr[indexB];
      arr[indexB] = temp;
    },

    bubbleSort: function (arr) {
      let isSorted = false;
      let len = this.arr.length - 1;
      const animations = [];
      while (!isSorted) {
        isSorted = true;
        for (let i = 0; i < len; i++) {
          animations.push([i, i + 1, false])
          if (arr[i] > arr[i + 1]) {
            animations.push([i, arr[i + 1], undefined])
            animations.push([i + 1, arr[i], undefined])
            this.swap(arr, i, i + 1);
            isSorted = false;
          }
          animations.push([i, i + 1, true])
        }
        len--;
      }

      this.animationSorting(animations);
    },

    animationSorting: function (animations) {
      for (let i = 0; i < animations.length; i++) {
        const sortingBar = document.getElementsByClassName("arr-bar");
        const [a, b, c] = animations[i];
        if (c !== undefined) {
          const barAStyle = sortingBar[a].style;
          const barBStyle = sortingBar[b].style;
          const color = !c ? "red" : "turquoise";
          setTimeout(() => {
            barAStyle.backgroundColor = color;
            barBStyle.backgroundColor = color;
          }, i * 5);
        } else {
          setTimeout(() => {
            const barAStyle = sortingBar[a].style;
            barAStyle.height = `${b}px`;
          }, i * 5);
        }
      }
    },

    /**
     * checking function for result after sorting
     * checking with build-in sorting function from javascript
     * @param arrayOne|Array
     * @param arrayTwo|Array
     */
    arrayAreEqual: function (arrayOne, arrayTwo) {
      if (arrayOne.length !== arrayTwo.length) return false;
      for (let i = 0; i < arrayOne.length; i++) {
        if (arrayOne[i] !== arrayTwo[i]) {
          return false;
        }
      }
      return true;
    },
  },
};
</script>

<style>
.container {
  display: grid;
}
.side-bar {
  width: calc(100vw - 80%);
  height: 100%;
  border: 1px solid black;
}
.content {
  height: 95vh;
  margin-top: 8px;
  border: 1px solid red;
  display: grid;
  justify-content: center;
  gap: 1px;
  grid-auto-flow: column;
}
.arr-bar {
  width: 10px;
  height: 100px;
  background: turquoise;
  display: inline-block;
}
</style>