<template>
  <div class="item">
    <InlineSvg :src="imgPath"></InlineSvg>
    <p class="item__text">{{ text }}</p>
  </div>
</template>

<script>
import Vue from 'vue';
import InlineSvg from 'vue-inline-svg';
export default Vue.extend({
  name: 'Item',
  components: {
    InlineSvg
  },
  props: {
    imgPath: {
      type: String,
      required: true
    },
    text: {
      type: String,
      required: true
    }
  }
});
</script>

<style lang="scss" scoped>
.item {
  background-color: #f6f9fe;
  min-width: 100px;
  padding: 15px;
  margin: 10px;
}
</style>
