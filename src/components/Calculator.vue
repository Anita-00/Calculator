<template>
  <!-- Happy Coding -->
  <div class="p-3 rounded" style="max-width: 400px; margin: 50px auto; background: #234">
    
    <!-- Calculator Result -->
    <div class="w-full rounded m-1 p-3 text-right lead font-weight-bold text-white bg-vue-dark">
      {{ calc || 0 }}
    </div>

    <!-- Calculator buttons -->
    <div class="row no-gutters">
      <div class="col-3" v-for="n in buttons" :key="n" >
        <div :id="n" class="lead text-white text-center m-1 py-3 bg-vue-dark rounded hover-class" 
          :class="{'bg-vue-green': ['C','*','/','-','+','%','='].includes(n)}" @click="calculate(n)">
          {{n}}
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import {evaluate} from "mathjs"
export default {
  name: 'Calculator',
  props: {
    msg: String,
  },

  data() {
    return {
      calc: '0',
      num: '',
      previous_num: '',
      buttons: ['C','*','/','-',7,8,9,'+',4,5,6,'%',1,2,3,'=',0,'.'],
      operator: '',
      previous_id: '',
      previous_color: '',
      original_color: ''
    }
  },
  methods: {
    calculate(n) {
      // change the button color to red
      let button_id = document.getElementById(n); 
      this.original_color = button_id.style.backgroundColor;
      button_id.style.backgroundColor = 'red';
    
      if (typeof n === 'number') {
        // store current number being entered
        let str_num = n.toString();
        this.num += str_num;
        this.calc = this.num

      } else if (n === '.') {
        // at decimal to number entered
        this.num += n;
      } else if (['*', '/','-','+','%'].includes(n)) {
        if (this.previous_num.length > 0 && this.num.length>0) {
          // checks to see if already an operation on two numbers
          this.calc = evaluate(this.previous_num + this.operator + this.num).toString();
          this.num = this.calc;
          this.previous_num = '';
        } 
        this.previous_num = this.num;
        this.num = ''; // clear num for next input number
        this.operator = n;
          
      } else if ('=' === n) {
        // calculate the value
        this.calc = evaluate(this.previous_num + this.operator + this.num).toString();
        this.num = this.calc;
        this.previous_num = '';
      } else if (n==='C') {
        // clears everything
        this.num = '';
        this.calc = 0;
        this.previous_num = '';
      } 
      this.storeColors(button_id);
    },
    storeColors(button_id) {
      // records the original button colour and changes the previous button clicked to its original color
      if (this.previous_id) {
        
        this.previous_id.style.backgroundColor = this.previous_color;
      }
      this.previous_id = button_id;
      this.previous_color = this.original_color;
    }
  }
}
</script>

<style scoped>
  .bg-vue-dark {
    background-color: #31475e;  
}
.hover-class:hover {
  cursor: pointer;
  background-color: red;
}
.bg-vue-green {
    background-color: #3fb984;
  }

</style>
