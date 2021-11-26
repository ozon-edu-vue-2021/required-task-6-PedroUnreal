<script>
export default {
  name: 'item',
  props: {
    item: {
      type: Object,
      default: () => ({})
    }
  },
  render() {
    const { item, $style } = this;

    return (
      <div class={$style.item}>
        <img class={$style.field} src={`https://via.placeholder.com/100x55?text=${item.id}`} alt="" />
        <span class={$style.field}>{item.id}</span>
        <span class={$style.field}>{item.postId}</span>
        <span class={$style.field}>{item.name}</span>
      </div>
    );
  }
};
</script>

<style module>
  .item {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    height: 55px;
  }

  .field {
    margin-right: 16px;
  }
</style>
