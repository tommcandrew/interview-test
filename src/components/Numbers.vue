<template>
  <div>
    <div class="number" :id="'number-'+number" v-for="number in getNumbers()" :key="number" @mouseover="hov(number)" @mouseout="reset">
      {{number}}
    </div>
  </div>
</template>

<script>
export default {
  data()
  {
    return {
      limit: this.$parent.limit,
      numbers: []
    }
  },
  // it seems unnecessary to have a local variable for limit when you can just access $parent.limit but it could be a Vue convention I'm not aware of
  watch: {
    ['$parent.limit'](newLimit)
    {
      // converting the input value here to number so can use when generating array
      this.limit = Number(newLimit);
    }
  },
  methods: {
    // renaming from n() so a bit clearer
    getNumbers()
    {
      // a for loop is O(n) whereas (I assume) Array.from is O(1) - UI performance is improved anyway
      const numbers = Array.from(Array(this.limit).keys())
      // as far as I know, Math.random is not considered reliable for shuffling so have used Fischer-Yates (copied from SO!)
      return this.shuffle(numbers).sort(() => Math.random() - 0.5);
    },
    // renaming number to selectedNumber for clarity
    hov(selectedNumber)
    {
      // renaming nums to numberNodes for clarity
      const numberNodes = document.querySelectorAll('.number');

      for(let i = 0; i < numberNodes.length; i++)
      {
        // also converting the text content here to number to avoid implicit type coercion on line with remainder operator
        const numberNodeVal = Number(numberNodes[i].textContent.trim());
        // checking values aren't same so hovered number isn't highlighted
        if(selectedNumber % numberNodeVal === 0 && selectedNumber !== numberNodeVal)
        {
          numberNodes[i].classList.add('active')
          console.log('divisor', numberNodeVal)
        }
      }
    },
    reset()
    {
      const nums = document.querySelectorAll('.number');
      nums.forEach(num => num.classList.remove('active'))
    },
    shuffle(array)
    {
      let currentIndex = array.length
      let randomIndex;
      while (currentIndex != 0) {
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex--;
        [array[currentIndex], array[randomIndex]] = [
          array[randomIndex], array[currentIndex]];
      }
      return array;
    }
  }
}
</script>

<style scoped>
	.number {
		display: inline-block;
		padding: 5px;
		background-color: lightgrey;
		margin: 5px;
    cursor: pointer;
	}

  .number:hover {
		background-color: grey;
  }

	.active {
		background-color: red;
	}
</style>
