<template>
  <div
    class="item"
    @click="isActive = true"
    :class="{ active: isActive }"
    v-click-outside="clearMutable"
  >
    <div class="item__col-1">
      <div v-if="isActive" class="item__toolbar">
        <div class="item__toolbar__col-1">
          <button @click="$emit('moveDown', id)" class="item__btn">
            <InlineSvg src="./arrow-top.svg"></InlineSvg>
          </button>
          <button @click="$emit('moveUp', id)" class="item__btn">
            <InlineSvg src="./arrow-top1.svg"></InlineSvg>
          </button>
        </div>
        <div class="item__toolbar__col-2">
          <button @click.stop="copyTextFromBuffer" class="item__btn">
            <InlineSvg src="./brush.svg"></InlineSvg>
          </button>
          <button @click.stop="$emit('copyItem', id)" class="item__btn">
            <InlineSvg src="./copy.svg"></InlineSvg>
          </button>
          <button @click.stop="$emit('deleteItem', id), (inputValue = '')" class="item__btn">
            <InlineSvg src="./trash.svg"></InlineSvg>
          </button>
        </div>
      </div>
      <InlineSvg :src="imgPath"></InlineSvg>
      <p class="item__text">{{ text }}</p>
    </div>
    <template v-if="isActive">
      <div class="item__col-2">
        <input
          class="item__input"
          v-model="inputValue"
          type="text"
          @input="$emit('outputData', inputValue)"
        />
      </div>
    </template>
  </div>
</template>

<script>
import Vue from 'vue';
import InlineSvg from 'vue-inline-svg';
import vClickOutside from 'v-click-outside';

Vue.use(vClickOutside);
export default Vue.extend({
  name: 'Item',
  components: {
    InlineSvg
  },
  data() {
    return {
      inputValue: '',
      isActive: this.mutable
    };
  },
  props: {
    imgPath: {
      type: String,
      required: true
    },
    text: {
      type: String,
      required: true
    },
    mutable: {
      type: Boolean,
      default: false
    },
    id: {
      type: Number,
      required: true
    }
  },
  methods: {
    clearMutable() {
      this.mutable = false;
      this.isActive = false;
    },
    copyTextFromBuffer() {
      navigator.clipboard.readText().then((text) => {
        this.inputValue = text;
      });
      this.$emit('copyText');
    }
  }
});
</script>

<style lang="scss" scoped>
.active {
  background-color: #d9e7ff !important;
}
.item {
  background-color: #f6f9fe;
  min-width: 100px;
  width: 90%;
  padding: 15px 0;
  margin-bottom: 10px;

  &__btn {
    border: none;
    background-color: #449cf4;
    display: flex;
    align-items: center;
    justify-self: center;
    border-radius: 4px;
    padding: 8px 8px;

    &:hover {
      background-color: darken(#449cf4, 5);
    }
  }

  &__toolbar {
    position: absolute;
    display: flex;
    right: 0;
    top: -15px;

    &__col-1 {
      margin-right: 10px;
      display: flex;
    }

    &__col-2 {
      display: flex;
    }
  }

  &__col-1 {
    position: relative;
  }

  &__col-2 {
    margin-top: 15px;
  }

  &__input {
    border: none;
    padding: 10px;
    width: 85%;
    border-radius: 10px;
  }
}
</style>
