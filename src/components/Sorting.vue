<template>
  <div class="container">
    <div class="side-bar">
      <button v-on:click="generateArray(425, 5, 600)">
        Generate new Array
      </button>
      <button v-on:click="bubbleSort(arr)">Bubble Sort</button>
    </div>
    <div class="content">
      <div
        class="arr-bar"
        v-for="(item, index) in arr"
        :key="index"
        v-bind:style="{ height: item + 'px' }"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Sorting",
  created: function () {
    this.generateArray(425, 5, 600);
  },
  data: function () {
    return {
      arr: [],
    };
  },
  methods: {
    generateArray: function (length, min, max) {
      this.arr = [];
      for (let i = 0; i < length; i++) {
        this.arr.push(this.randNumber(min, max));
      }

      console.log(this.arr);
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
      while (!isSorted) {
        isSorted = true;
        for (let i = 0; i < len; i++) {
          if (arr[i] > arr[i + 1]) {
            this.swap(arr, i, i + 1);
            isSorted = false;
          }
        }
        len--;
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
  width: 2px;
  height: 100px;
  background: turquoise;
  display: inline-block;
}
</style>