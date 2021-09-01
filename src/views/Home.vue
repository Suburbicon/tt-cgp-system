<template>
  <div class="home">
    <div class="home__header-wrap">
      <h2 class="home__header">HTML EDITOR</h2>
    </div>
    <div class="home__content">
      <div class="home__content__col-1 drop-zone">
        <template v-for="(item, index) in items">
          <Item
            draggable="true"
            :key="index"
            @dragstart.native="startDrag($event, item)"
            :imgPath="item.imgPath"
            :text="item.text"
          />
        </template>
      </div>
      <div
        class="home__content__col-2 drop-zone"
        @drop="onDrop($event)"
        @dragenter.prevent
        @dragover.prevent
      >
        <template v-for="(item, index) in mutableItems">
          <MutableItem
            :imgPath="item.imgPath"
            :text="item.text"
            :mutable="item.mutable"
            :key="index"
            :id="item.id"
            @outputData="(val) => renderData(val, item, index)"
            @deleteItem="deleteMutableItem"
            @copyItem="copyMutableItem"
            @copyText="copyTextFromBuffer(index)"
            @moveUp="(val) => moveItemUp(val, index)"
            @moveDown="(val) => moveItemDown(val, index)"
          />
        </template>
      </div>
      <div class="home__content__col-3">
        <template v-for="(item, index) in mutableItems">
          <template v-if="item.type === 'img'">
            <component :is="item.type" :src="item.value" class="home__render-item" :key="index">
            </component>
          </template>
          <template v-else>
            <div class="home__wrap-render-item" :key="index">
              <component :is="item.type" class="home__render-item">
                {{ item.value }}
              </component>
            </div>
          </template>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue';
import Item from '../components/Item.vue';
import MutableItem from '../components/MutableItem.vue';
import vClickOutside from 'v-click-outside';

Vue.use(vClickOutside);

export default Vue.extend({
  name: 'Home',
  components: {
    Item,
    MutableItem
  },
  data() {
    return {
      tmp: '',
      items: [
        {
          imgPath: './Headline.svg',
          text: 'Headline',
          type: 'h1'
        },
        {
          imgPath: './text-align-left.svg',
          text: 'Paragraph',
          type: 'p'
        },
        {
          imgPath: './image.svg',
          text: 'Button',
          type: 'button'
        },
        {
          imgPath: './image.svg',
          text: 'Image',
          type: 'img'
        }
      ],
      mutableItems: []
    };
  },

  methods: {
    startDrag(event, item) {
      event.dataTransfer.dropEffect = 'move';
      event.dataTransfer.effectAllowed = 'move';
      event.dataTransfer.setData('text', item.text);
    },

    onDrop(event) {
      const itemText = event.dataTransfer.getData('text');
      const item = { ...this.items.find((item) => itemText == item.text) };
      item.mutable = false;
      item.value = '';
      item.id = Math.random();
      this.mutableItems.push(item);
    },

    renderData(outputData, item, index) {
      let arr = [...this.mutableItems];
      arr[index].value = outputData;
      this.mutableItems = arr;
    },

    deleteMutableItem(id) {
      this.mutableItems = this.mutableItems.filter((item) => item.id !== id);
    },

    copyMutableItem(id) {
      let arr = [...this.mutableItems];
      let item = { ...this.mutableItems.find((item) => item.id === id) };
      item.id = Math.random();
      arr.push(item);
      this.mutableItems = arr;
    },
    copyTextFromBuffer(index) {
      let arr = [...this.mutableItems];
      navigator.clipboard.readText().then((text) => {
        arr[index].value = text;
      });
      this.mutableItems = arr;
    },
    moveItemDown(id, index) {
      if (this.mutableItems.length > 1 && this.mutableItems.length > index + 1) {
        let arr = [...this.mutableItems];
        let a = arr[index];
        arr[index] = arr[index + 1];
        arr[index + 1] = a;
        this.mutableItems = arr;
      }
    },
    moveItemUp(id, index) {
      if (this.mutableItems.length > 1 && index - 1 > -1) {
        let arr = [...this.mutableItems];
        let a = arr[index];
        arr[index] = arr[index - 1];
        arr[index - 1] = a;
        this.mutableItems = arr;
      }
    }
  }
});
</script>

<style lang="scss" scoped>
img {
  width: 100%;
}
.home {
  height: 100%;
  &__header-wrap {
    display: flex;
    justify-content: flex-start;
    margin-bottom: 20px;
  }

  &__content {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    height: 100%;
  }

  &__content__col-1 {
    display: flex;
    flex-wrap: wrap;
    width: 25%;
  }
  &__content__col-2 {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 40%;
    background-color: #f5f5fc;
    height: 100%;
    box-shadow: inset 0px 4px 20px #ccc;
    padding-top: 20px;
  }
  &__content__col-3 {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 33%;
  }

  &__wrap-render-item {
    width: 100%;
  }

  &__render-item {
    margin-bottom: 30px;
    word-wrap: break-word;
  }
}
</style>
