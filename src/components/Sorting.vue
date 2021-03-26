<template>
  <div class="container">
    <div class="side-bar">
      <button v-on:click="generateArray(300, 5, 1000)">
        Generate new Array
      </button>
      <button v-on:click="bubbleSort(arr)">Bubble Sort</button>
      <button v-on:click="startMergeSort(arr)">Merge Sort</button>
      <button v-on:click="heap(arr)">Heap Sort</button>
      <button v-on:click="startQuickSort(arr)">Quick Sort</button>
      <button v-on:click="startInsertionSort(arr)">Insertion Sort</button>
    </div>
    <div class="content">
      <div
        class="arr-bar"
        v-for="(item, index) in arr"
        :key="index"
        :style="{
          height: (item / maxNum) * 95 + '%',
        }"
      ></div>
    </div>
  </div>
</template>

<script>
const BASE_COLOR = "whitesmoke";
const CHECKING_COLOR = "red";
const PRIMARY_COLOR = "turquoise";
const CLEARANCE_COLOR = "#12f320";
const SWAPING_COLOR = "aqua";
const LIME_COLOR = "#c2f083";
const BLUE_COLOR = "#82bdff";
const PINK_COLOR = "#ff8fc8";
const ORANGE_COLOR = "#ffc186";
const PIVOT_COLOR = "#9400ff";

export default {
  name: "Sorting",
  beforeMount: function () {
    this.generateRandomArray(300, 5, 1000);
  },
  mounted: function () {
    this.coloringAllBar();
  },
  data: function () {
    return {
      arr: [],
      maxNum: 0,
      baseColor: BASE_COLOR,
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
      let len = arr.length - 1;
      const animations = [];
      while (!isSorted) {
        isSorted = true;
        for (let i = 0; i < len; i++) {
          animations.push([i, i + 1, false, CHECKING_COLOR]);
          if (arr[i] > arr[i + 1]) {
            animations.push([i, arr[i + 1], true, SWAPING_COLOR]);
            animations.push([i + 1, arr[i], true, SWAPING_COLOR]);
            this.swap(arr, i, i + 1);
            isSorted = false;
          }
          animations.push([i, i + 1, false, BASE_COLOR]);
        }
        animations.push([len, arr[len], true, CLEARANCE_COLOR]);
        if (len + 1 < arr.length)
          animations.push([len + 1, arr[len + 1], true, BASE_COLOR]);
        len--;
      }

      this.coloringSortedArray(animations)

      this.animationSorting(animations);
    },
    /**************** END BUBLE SORT SECTION ***************/

    /**************** START MERGE SORT SECTION ***************/
    startMergeSort: function (arr) {
      const animations = [];
      if (arr.length < 1) return;
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
        animations.push([left, right, false, CHECKING_COLOR]);
        animations.push([left, right, false, BASE_COLOR]);
        if (tempArr[left] <= tempArr[right]) {
          anims.push([index, tempArr[left], true, SWAPING_COLOR]);
          arr[index] = tempArr[left];
          left++;
        } else {
          anims.push([index, tempArr[right], true, SWAPING_COLOR]);
          arr[index] = tempArr[right];
          right++;
        }
        index++;
      }

      while (left <= leftEnd) {
        animations.push([left, left, false, CHECKING_COLOR]);
        animations.push([left, left, false, BASE_COLOR]);
        anims.push([index, tempArr[left], true, SWAPING_COLOR]);
        arr[index] = tempArr[left];
        left++;
        index++;
      }

      while (right <= endIndex) {
        animations.push([right, right, false, CHECKING_COLOR]);
        animations.push([right, right, false, BASE_COLOR]);
        anims.push([index, tempArr[right], true, SWAPING_COLOR]);
        arr[index] = tempArr[right];
        right++;
        index++;
      }

      for (let i = 0; i < anims.length; i++) animations.push(anims[i]);
    },
    /**************** END MERGE SORT SECTION ***************/

    /**************** START HEAP SORT SECTION ***************/
    heap: function (arr) {
      const animations = [];
      this.createMaxHeap(arr, animations);
      this.heapSort(arr, animations);
      console.log(arr);
      this.animationSorting(animations);
    },

    /**
     * Main function for sorting array using Heap Sort Algorithm
     * Swap root item with last item and heapify down last item until reach good position
     * @param arr|Array
     * @param animations|Array
     */
    heapSort: function (arr, animations) {
      let i = arr.length - 1;
      while (i >= 0) {
        animations.push([i, 0, false, CHECKING_COLOR]);
        animations.push([i, 0, false, BASE_COLOR]);

        const color = this.getColorInRange(i);

        animations.push([i, arr[0], true, color]);
        animations.push([0, arr[i], true, color]);
        this.swap(arr, 0, i);
        this.heapifyDown(arr, 0, animations, i);
        i--;
      }

      this.coloringSortedArray(animations)
    },

    /**
     * Reorder item in heap when position was change
     * @param arr|Array
     * @param index|int
     * @param animations|Array
     * @param len|int
     */
    heapifyDown: function (arr, index, animations, len = null) {
      let leftIndex = index * 2 + 1;
      let rightIndex = index * 2 + 2;

      if (len == null) len = arr.length;

      while (leftIndex < len) {
        const color = this.getColorInRange(index);

        let swapIndex = leftIndex;
        animations.push([index, swapIndex, false, CHECKING_COLOR]);
        animations.push([index, swapIndex, false, BASE_COLOR]);
        if (rightIndex < len && arr[rightIndex] > arr[leftIndex]) {
          swapIndex = rightIndex;
          animations.push([index, swapIndex, false, CHECKING_COLOR]);
          animations.push([index, swapIndex, false, BASE_COLOR]);
        }

        const color2 = this.getColorInRange(swapIndex);

        if (arr[index] > arr[swapIndex]) break;

        animations.push([index, arr[swapIndex], true, color]);
        animations.push([swapIndex, arr[index], true, color2]);
        this.swap(arr, index, swapIndex);
        index = swapIndex;
        leftIndex = index * 2 + 1;
        rightIndex = index * 2 + 2;
      }
    },

    /**
     * Main function for create Max Heap
     * create max heap because we want to sort the array ASC
     * @param arr|Array
     * @param animations|Array
     */
    createMaxHeap: function (arr, animations) {
      const middleIndex = Math.floor((arr.length - 1) / 2);
      for (let i = middleIndex; i >= 0; i--) {
        this.heapifyDown(arr, i, animations);
      }
    },
    /**************** END HEAP SORT SECTION ***************/

    /**************** START QUICK SORT SECTION ***************/
    startQuickSort: function (arr) {
      const animations = [];
      this.quickSort(arr, 0, arr.length - 1, animations);
      for (let i = 0; i < arr.length; i++) {
        animations.push([i, arr[i], true, CHECKING_COLOR]);
        if (i > 0) animations.push([i - 1, arr[i - 1], true, CLEARANCE_COLOR]);

        if (i == arr.length - 1)
          animations.push([i, arr[i], true, CLEARANCE_COLOR]);
      }

      for (let i = 0; i < arr.length; i++)
        animations.push([i, arr[i], true, PRIMARY_COLOR]);

      this.animationSorting(animations);
      console.log(arr);
    },

    /**
     * Main function Quick Sort Algortihm
     * Choosing pivot index and split arr into left side pivot and right side pivot
     * call recursivly until left boundary less than right boundary
     * @param arr|Array
     * @param left|int
     * @param right|int
     * @param animations|Array
     */
    quickSort: function (arr, left, right, animations) {
      if (left < right) {
        const pivot = this.partition(arr, left, right, animations);
        this.quickSort(arr, left, pivot - 1, animations);
        this.quickSort(arr, pivot + 1, right, animations);
      }
    },

    /**
     * Main function for find pivot index
     * @param arr|Array
     * @param left|int
     * @param right|int
     * @param animations|Array
     */
    partition: function (arr, left, right, animations) {
      let pivot = arr[right];
      animations.push([right, pivot, true, PIVOT_COLOR]);

      let j = left - 1;
      for (let i = left; i < right; i++) {
        animations.push([i, right, false, CHECKING_COLOR]);
        animations.push([i, right, false, BASE_COLOR]);
        if (arr[i] <= pivot) {
          j++;

          animations.push([i, arr[j], true, ORANGE_COLOR]);
          animations.push([j, arr[i], true, ORANGE_COLOR]);
          this.swap(arr, i, j);
        }
      }

      animations.push([j + 1, pivot, true, PIVOT_COLOR]);
      animations.push([right, arr[j + 1], true, BASE_COLOR]);
      this.swap(arr, j + 1, right);

      for (let i = left; i < j; i++)
        animations.push([i, arr[i], true, PINK_COLOR]);
      for (let i = j + 2; i <= right; i++)
        animations.push([i, arr[i], true, LIME_COLOR]);

      return j + 1;
    },
    /**************** END QUICK SORT SECTION ***************/

    /**************** START INSERTION SORT SECTION ***************/
    startInsertionSort: function(arr) {
      const animations = []
      this.insertionSort(arr, animations)
      this.animationSorting(animations)
    },

    /**
     * Main function Insertion Sorting Algorithm
     * @param arr|Array
     * @param animations|Array
     */
    insertionSort: function(arr, animations) {
      for(let i = 1; i < arr.length; i++)
      {
        let index = i;
        const temp = arr[index]
        let j = index - 1
        while(temp < arr[j])
        {
          animations.push([index, j, false, CHECKING_COLOR])
          arr[j + 1] = arr[j]
          animations.push([j + 1, arr[j], true, (j + 1 == i) ? PIVOT_COLOR : this.getColorInRange(j + 1)])
          animations.push([index, j, false, this.getColorInRange(j)])
          index--
          j--

          if(j < 0) {
            break
          }
        }

        animations.push([j + 1, temp, true, this.getColorInRange(j + 1)])
        arr[j + 1] = temp
      }

      this.coloringSortedArray(animations)
    },
    /**************** END INSERTION SORT SECTION ***************/

    /**************** START HELPER SECTION ***************/
    generateArray: function (length, min, max) {
      this.generateRandomArray(length, min, max);
      this.coloringAllBar();
    },
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
    animationSorting: function (animations, duration = 5) {
      for (let i = 0; i < animations.length; i++) {
        const sortingBar = document.getElementsByClassName("arr-bar");
        const [a, b, c, d] = animations[i];
        if (!c) {
          const barAStyle = sortingBar[a].style;
          const barBStyle = sortingBar[b].style;
          setTimeout(() => {
            barAStyle.backgroundColor = d;
            barBStyle.backgroundColor = d;
          }, i * duration);
        } else {
          setTimeout(() => {
            const barAStyle = sortingBar[a].style;
            (barAStyle.height = `${(b / this.maxNum) * 95}%`),
              (barAStyle.backgroundColor = d);
          }, i * duration);
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

    getColorInRange: function (number) {
      const divider = Math.floor((this.arr.length - 1) / 4);
      if (number >= 0 && number < divider * 1) return ORANGE_COLOR;

      if (number >= divider * 1 && number < divider * 2) return BLUE_COLOR;

      if (number >= divider * 2 && number < divider * 3) return PINK_COLOR;

      if (number >= divider * 3 && number < this.arr.length) return LIME_COLOR;
    },

    coloringAllBar: function () {
      const bar = document.getElementsByClassName("arr-bar");
      for (let i = 0; i < bar.length; i++) {
        bar[i].style.backgroundColor = BASE_COLOR;
      }
    },

    coloringSortedArray: function(animations) {
      for (let i = 0; i < this.arr.length; i++) {
        animations.push([i, this.arr[i], true, CHECKING_COLOR]);
        if (i > 0) animations.push([i - 1, this.arr[i - 1], true, CLEARANCE_COLOR]);

        if (i == this.arr.length - 1)
          animations.push([i, this.arr[i], true, CLEARANCE_COLOR]);
      }

      for (let i = 0; i < this.arr.length; i++)
        animations.push([i, this.arr[i], true, PRIMARY_COLOR]);
    },
    /**************** END HELPER SECTION ***************/
  },
};
</script>

<style>
.container {
  display: grid;
  background: black;
}
.side-bar {
  /* width: calc(100vw - 80%); */
  width: 100vw;
  height: 100%;
}
.content {
  background: inherit;
  display: grid;
  justify-content: center;
  gap: 1px;
  grid-auto-flow: column;
  position: absolute;
  top: 23px;
  bottom: 0;
  left: 0;
  right: 0;
}
.arr-bar {
  width: 3px;
  display: inline-block;
}
</style>