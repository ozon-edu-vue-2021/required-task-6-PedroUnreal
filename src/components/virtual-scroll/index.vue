<template>
  <div v-if="false">
    <RecycleScroller
      v-if="rendered"
      :class="$style.scroller"
      :items="rows"
      :item-size="55"
      :buffer="2000"
      page-mode
      key-field="id"
      v-slot="{ item }"
    >
      <Item :item="item" />
    </RecycleScroller>

    <button @click="rendered = true">Render</button>
  </div>
  <div v-else>
    <template v-if="rendered">
      <Item v-for="item in rows" :key="item.id" :item="item" />
    </template>

    <button @click="rendered = true">Render</button>
  </div>
</template>

<script>
import Item from './item';

export default {
  name: 'VirtalScrollWrapper',
  components: { Item },
  async created() {
    const res = await fetch(`https://jsonplaceholder.typicode.com/comments`);
    this.rows = await res.json();
  },
  data() {
    return {
      rows: [],
      rendered: false
    };
  }

};
</script>

<style module>
  .scroller {
    text-align: left;
    margin: 24px;
  }
</style>
