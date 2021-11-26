<script>
export default {
  name: 'filter-dropdown',
  props: {
    columnProp: {
      type: String,
      default: ''
    },
    shown: {
      type: Boolean,
      default: false
    },
    filterText: {
      type: String,
      default: ''
    },
    getAllSelectedFilters: {
      type: Function
    },
    setCurrentProp: {
      type: Function
    }
  },
  data() {
    return {
      text: ""
    }
  },
  methods: {
    clearInput() {
      let input = document.querySelector(`.${this.columnProp}`)
      input.value = ""
    },
  },
  render() {
    const { $style, shown, $listeners } = this;
    const { openFilterTooltip, getAllSelectedFilters, closeFilterTooltip, setCurrentProp } = $listeners;

    return (
      <v-dropdown
        class={$style.filterIcon}
        triggers={[]}
        autoHide={false}
        shown={shown}
      >
        <font-awesome-icon
          icon="filter"
          on={{ click: openFilterTooltip }}
        />

        <div slot="popper">
          <input
            type="text"
            class={this.columnProp}
            on={{ input: getAllSelectedFilters }}
            on={{ input: setCurrentProp }}
            
          />

          <font-awesome-icon
            icon="times"
            class={$style.closeIcon}
            on={{ click: closeFilterTooltip }}
            on={{ click: this.clearInput }}
          />
        </div>
      </v-dropdown>
    );
  }
};
</script>

<style module>
  .filterIcon {
    margin-left: auto;
    margin-right: 8px;
  }

  .filterIcon:hover {
    cursor: pointer;
  }

  .closeIcon {
    position: absolute;
    top: 6px;
    right: 6px;
  }

  .closeIcon:hover {
    cursor: pointer;
  }
</style>
