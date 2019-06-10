<template>
  <div class="calculate">
    <p>{{a}}+{{b}}={{sum}}</p>
    <input type="text" v-model="a" style="width:30px;text-align:center" @blur="sumFunc"/>
    <span class="symbol">+</span>
    <input type="text" v-model="b" style="width:30px;text-align:center" @blur="sumFunc"/>
    <span class="symbol" @click="sumFunc"> = </span>
    <span class="item">{{sum}}</span>
  </div>
</template>
<script>
  export default({
    name:'addFunc',
    props:['num1','num2'],
    data() {
      return{
        a: this.num1 ? this.num1 : 0,
        b: this.num2 ? this.num2 : 0,
        sum: 0,
      }
    },
    mounted() {
      this.sumFunc();
    },
    methods:{
      sumFunc() {
        
        let a = Number(this.a);
        let b = Number(this.b);
        if(isNaN(a) || isNaN(b)) {
          
          return;
        }else{
          this.sum = a + b;
          this.$emit('getSumFromChild',this.sum);
        }
      }
    }
  })
</script>
<style>
.calculate{
  width: 100%;
  line-height: 26px;
    .item{
      
    }
    .symbol{
      
    }
}
  
</style>