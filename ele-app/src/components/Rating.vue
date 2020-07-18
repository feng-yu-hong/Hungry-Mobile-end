<template>
<div class="Rating-gray">
    <i v-for="(item,index) in itemClasses" :key="index" class="fa" :class="item"></i>
</div>
</template>


<script>
const LENGTH = 5; //星星长度 最长为5分5颗星
const CLS_ON = "fa-star"; //星星的类型 （全星）
const CLS_HALF = "fa-star-half-alt"; //(半星)
const CLS_OFF = "fa-star-o"; //填充

export default {
  name: "Rating",
  props: {
    rating: Number
  },
  computed: {
    itemClasses() {
      //4.8 四个全星一个半星
      //定义一个数组装有几个全星几个半星
      let result = [];

      //对分数进行处理,想下取0.5  例子：4.8*2=9.6 向下为9 除2为4.5四个全星一个半星
      let score = Math.floor(this.rating * 2) / 2;

      //控制半星
      let hasDecimal = score % 1 !== 0;

      //全星
      let integer = Math.floor(score);

      //全星放到数组中
      for (let i = 0; i < integer; i++) {
        result.push(CLS_ON);
      }

      //半星
      if (hasDecimal) {
        result.push(CLS_HALF);
      }

      //补齐
      while (result.length < LENGTH) {
        result.push(CLS_OFF);
      }
      return result;
    }
  }
};
</script>


<style scoped>
.Rating-gray {
  margin-right: 1.066667vw;
  color: #ffbe00;
  display: inline-block;
}
</style>
