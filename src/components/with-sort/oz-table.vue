<script lang="jsx">
import { orderBy } from 'lodash/collection';
import FilterDropdown from './filter-dropdown';

export default {
  name: 'oz-table',
  props: {
    rows: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      sortProp: '',
      sortDirection: '',
      sortProps: [],
      sortDirections: [],
      allSelectedFilters: [],
      currentProp: ""
    };
  },
  computed: {
    sortedRows() {
      let res;

      if (this.sortProps.length===0) {
        res =  this.rows;
      }

      res = orderBy(this.rows, this.sortProps, this.sortDirections);

      if( res) {
        // res.forEach(row => (row[this.filterProp]) = String(row[this.filterProp]))
        // res = res.filter(row => row[this.filterProp].search(this.filterText) > -1)
        for (let index = 0; index < this.allSelectedFilters.length; index++) {
          const selectedFilter = this.allSelectedFilters[index];
          
          res = res.filter(
            row => hasSubstring(row[selectedFilter.prop], selectedFilter.text)
          );
        }
      }

      function hasSubstring(str, substr) {
        return `${str}`.search(substr) > -1 ;
      }

      console.log(res);

      return res;
    },
  },
  methods: {
    toggleSort(prop) {
      this.sortProp = prop;
      let foundIndex = this.sortProps.indexOf(this.sortProp);
      if(foundIndex === -1) {this.sortProps.push(this.sortProp)
      this.sortDirection = 'desc' 
      this.sortDirections.push(this.sortDirection)
       
      } else {this.sortDirections[foundIndex] === 'desc' ? this.sortDirections.splice([foundIndex],1, 'asc') : this.sortDirections.splice([foundIndex],1, 'desc')}
    },

    toDefaultSort(prop){
      console.log(prop);
      let index =this.sortProps.findIndex(item => item === prop)
      console.log(index);
      this.sortProps.splice(index,1)
      this.sortDirections.splice(index,1)
    },
    openFilterTooltip(prop = '') {
      
      if (this.allSelectedFilters.findIndex((item) => item.prop === prop) ===-1) { 
        let obj = {prop: prop, text: ""}
       this.allSelectedFilters.push(obj)
      }
      this.currentProp = prop;
    },
    setCurrentProp(prop){
      this.currentProp = prop;
    },
    closeFilterTooltip(prop){
      console.log(this.allSelectedFilters.findIndex((item) => item.prop === prop))
      if (this.allSelectedFilters.findIndex((item) => item.prop === prop) > -1) { 
      
       this.allSelectedFilters.splice(this.allSelectedFilters.findIndex((item) => item.prop === prop), 1)
      }

    },
    getAllSelectedFilters(e){
      let text = e.target.value;
      let prop = this.currentProp;
    
      if (this.allSelectedFilters.findIndex((item) => item.prop === prop) ===-1) { 
        let obj = {prop: prop, text: text}
       this.allSelectedFilters.push(obj)
      }else {
        let index = this.allSelectedFilters.findIndex(item => item.prop === prop)
        this.allSelectedFilters[index].text=text
        }
       
    },
    renderHead(h, columnsOptions) {
      const { $style, sortProps, sortDirections, allSelectedFilters, } = this;

      return columnsOptions.map((column) => {
        const renderedTitle = column.scopedSlots.title ? column.scopedSlots.title() : column.title;
        let sortIcon = 'sort';

        if (sortProps.findIndex(item => item === column.prop) > -1) {
          sortIcon = sortDirections[sortProps.findIndex(item => item === column.prop)] === 'asc' ? 'sort-amount-down' : 'sort-amount-up';
        }
        let isHidden
        if (this.sortProps.findIndex(item => item === column.prop ) === -1){
         isHidden = true} else { isHidden = false}

        return (
          <th key={column.prop} class={$style.headerCell}>
            <div class={$style.headerCellContent}>
              <span>{renderedTitle}</span>
              <font-awesome-icon
                class={$style.sortIcon}
                icon={sortIcon}
                on={{ click: () => this.toggleSort(column.prop) }}
              />
              <div class="close-button" hidden={isHidden}>
              <font-awesome-icon
                icon="times"
               
               on={{ click: () => this.toDefaultSort(column.prop) }}
              />
              </div>
              <FilterDropdown
                columnProp={column.prop}
                shown={allSelectedFilters.findIndex(item => item.prop === column.prop ) !== -1 }
                getAllSelectedFilters={this.getAllSelectedFilters}
                setCurrentProp={this.setCurrentProp}

                on={{
                  openFilterTooltip: () => this.openFilterTooltip(column.prop),
                  closeFilterTooltip: () => this.closeFilterTooltip(column.prop),
                  setCurrentProp: () => this.setCurrentProp(column.prop),
                  getAllSelectedFilters: this.getAllSelectedFilters,
                }}
              />
            </div>
          </th>
        );
      });
    },
    renderRows(h, columnsOptions) {
      return this.sortedRows.map((row, index) => {
        return <tr key={row.id || index}>{...this.renderColumns(h, row, columnsOptions)}</tr>;
      });
    },
    renderColumns(h, row, columnsOptions) {
      return columnsOptions.map((column) => {
        return (
          <td key={column.prop} class={this.$style.cell}>
            {column.scopedSlots.body ? column.scopedSlots.body({ row }) : row[column.prop]}
          </td>
        );
      });
    },
    getColumnOptions() {
      return this.$slots.default.
        filter(item => item.componentOptions && item.componentOptions.tag === 'oz-table-column').
        map(column =>
          Object.assign({}, column.componentOptions.propsData, {
              scopedSlots: column.data.scopedSlots || {}
            }
          )
        );
    }
  },
  render(h) {
    const columnsOptions = this.getColumnOptions();
    const columnsHead = this.renderHead(h, columnsOptions);
    const rows = this.renderRows(h, columnsOptions);

    return (
      <table class={this.$style.table}>
        <thead>{...columnsHead}</thead>
        <tbody>{...rows}</tbody>
      </table>
    );
  }
};
</script>

<style module>
.table {
  border-spacing: 0;
  margin: 8px;
  width: 100%;
}

.cell {
  text-align: left;
  border-bottom: 1px solid #c8cacc;
  padding: 1rem 1rem;
}

.headerCell {
  composes: cell;
  background: #c7cbcb;
}

.headerCellContent {
  display: flex;
  align-items: center;
}

.sortIcon {
  margin-left: 8px;
  margin-right: 24px;
}

.sortIcon:hover {
  cursor: pointer;
}
.close-button {
  margin-left: -18px
}
</style>
