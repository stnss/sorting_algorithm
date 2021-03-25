<template>
  <div class="container">
    <div class="side-bar">
      <button v-on:click="generateRandomArray(250, 5, 600)">
        Generate new Array
      </button>
      <button v-on:click="bubbleSort(arr)">Bubble Sort</button>
      <button v-on:click="startMergeSort(arr)">Merge Sort</button>
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
const CHECKING_COLOR = "red";
const PRIMARY_COLOR = "turquoise";
const CLEARANCE_COLOR = "#12f320";

export default {
  name: "Sorting",
  created: function () {
    this.generateRandomArray(250, 5, 600);
  },
  data: function () {
    return {
      arr: [],
      maxNum: 0,
    };
  },
  methods: {
    /**************** START BUBBLE SORT SECTION ***************/
    /**
     * Main function Bubble Sort Algorithm
     * Tracking all progress and put into animation
     * Run animation after sorting was done
     * @param arr|Array
     */
    bubbleSort: function (arr) {
      let isSorted = false;
      let len = this.arr.length - 1;
      const animations = [];
      while (!isSorted) {
        isSorted = true;
        for (let i = 0; i < len; i++) {
          animations.push([i, i + 1, false]);
          if (arr[i] > arr[i + 1]) {
            animations.push([i, arr[i + 1], undefined]);
            animations.push([i + 1, arr[i], undefined]);
            this.swap(arr, i, i + 1);
            isSorted = false;
          }
          animations.push([i, i + 1, true]);
        }
        len--;
      }

      for(let i = 0; i < this.arr.length; i++) animations.push([i, this.arr[i], undefined])

      this.animationSorting(animations);
    },
    /**************** END BUBLE SORT SECTION ***************/

    /**************** START MERGE SORT SECTION ***************/
    startMergeSort: function (arr) {
      const animations = [];
      if (arr.length < 1) return;
      const jsArr = arr.slice().sort((a, b) => a - b);
      const tempArr = arr.slice();
      this.mergeSort(arr, tempArr, 0, arr.length - 1, animations);
      this.animationSorting(animations);
    },
    /**
     * Main function Insertion Sort Algorithm
     * Sort half of given array
     * store as tempArr
     * @param arr|Array
     * @param tempArr|Array
     * @param startIndex|int
     * @param endIndex|int
     * @param animations|Array
     */
    mergeSort: function (
      arr,
      tempArr = [],
      startIndex = null,
      endIndex = null,
      animations = []
    ) {
      if (startIndex >= endIndex) return;

      const middleIndex = Math.floor((startIndex + endIndex) / 2);
      this.mergeSort(tempArr, arr, startIndex, middleIndex, animations);
      this.mergeSort(tempArr, arr, middleIndex + 1, endIndex, animations);
      this.mergeHalves(arr, tempArr, startIndex, endIndex, animations);
    },

    /**
     * Do Merge between 2 tempArray
     * Tracking all progress and put into animation
     * Run animation after sorting was done
     * @param arr|Array
     * @param tempArr|Array
     * @param startIndex|int
     * @param endIndex|int
     * @param animations|Array
     */
    mergeHalves: function (arr, tempArr, startIndex, endIndex, animations) {
      const leftEnd = Math.floor((startIndex + endIndex) / 2);
      const rightStart = leftEnd + 1;

      let anims = [];

      let left = startIndex;
      let right = rightStart;
      let index = startIndex;

      while (left <= leftEnd && right <= endIndex) {
        animations.push([left, right, false]);
        animations.push([left, right, true]);
        if (tempArr[left] <= tempArr[right]) {
          anims.push([index, tempArr[left], undefined]);
          arr[index] = tempArr[left];
          left++;
        } else {
          anims.push([index, tempArr[right], undefined]);
          arr[index] = tempArr[right];
          right++;
        }
        index++;
      }

      while (left <= leftEnd) {
        animations.push([left, left, false]);
        animations.push([left, left, true]);
        anims.push([index, tempArr[left], undefined]);
        arr[index] = tempArr[left];
        left++;
        index++;
      }

      while (right <= endIndex) {
        animations.push([right, right, false]);
        animations.push([right, right, true]);
        anims.push([index, tempArr[right], undefined]);
        arr[index] = tempArr[right];
        right++;
        index++;
      }

      for (let i = 0; i < anims.length; i++) animations.push(anims[i]);
    },
    /**************** END MERGE SORT SECTION ***************/

    /**
     * Generate Random Integer Array
     * @param length|int
     * @param min|int
     * @param max|int
     */
    generateRandomArray: function (length, min, max) {
      this.arr = [];
      this.maxNum = -Infinity;
      for (let i = 0; i < length; i++) {
        let temp = this.randNumber(min, max);
        this.arr.push(temp);
        this.maxNum = Math.max(this.maxNum, temp);
      }
    },

    /**
     * Generate Random Integer
     * @param min|int
     * @param max|int
     *
     * source: https://stackoverflow.com/questions/4959975/generate-random-number-between-two-numbers-in-javascript
     */
    randNumber: function (min, max) {
      return Math.floor(Math.random() * (max - min + 1) + min);
    },

    /**
     * Base function for swaping item in given array
     * @param arr|Array
     * @param indexA|int
     * @param indexB|int
     */
    swap: function (arr, indexA, indexB) {
      let temp = arr[indexA];
      arr[indexA] = arr[indexB];
      arr[indexB] = temp;
    },

    /**
     * Base function for animation, coloring and change height bar using given animations
     * from sorting algorithm function
     * @param animations|Array
     */
    animationSorting: function (animations) {
      for (let i = 0; i < animations.length; i++) {
        const sortingBar = document.getElementsByClassName("arr-bar");
        const [a, b, c] = animations[i];
        if (c !== undefined) {
          const barAStyle = sortingBar[a].style;
          const barBStyle = sortingBar[b].style;
          const color = !c ? CHECKING_COLOR : CLEARANCE_COLOR;
          setTimeout(() => {
            barAStyle.backgroundColor = color;
            barBStyle.backgroundColor = color;
          }, i * 5);
        } else {
          setTimeout(() => {
            const barAStyle = sortingBar[a].style;
            barAStyle.height = `${b}px`;
            barAStyle.backgroundColor = PRIMARY_COLOR;
          }, i * 5);
        }
      }
    },

    /**
     * checking function for result after sorting
     * check with build-in sorting function from javascript
     * @param ownSortedArray|Array
     * @param javascriptSortedArray|Array
     */
    arrayAreEqual: function (ownSortedArray, javascriptSortedArray) {
      if (ownSortedArray.length !== javascriptSortedArray.length) return false;
      for (let i = 0; i < ownSortedArray.length; i++) {
        if (ownSortedArray[i] !== javascriptSortedArray[i]) {
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
  /* width: calc(100vw - 80%); */
  width: 100vw;
  height: 100%;
}
.content {
  height: 95vh;
  margin-top: 10px;
  border: 1px solid red;
  display: grid;
  justify-content: center;
  gap: 1px;
  grid-auto-flow: column;
}
.arr-bar {
  width: 4px;
  height: 100px;
  background: rgb(121, 121, 121);
  display: inline-block;
}
</style>