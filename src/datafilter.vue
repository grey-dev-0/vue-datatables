<template>
  <div class="list-group list-group-flush">
    <div class="list-group-item bg-blue-10">
      <div class="row" v-for="(row, i) in filterRows" :key="i">
        <template v-for="filter in row" :key="filter.name">
          <filter-input v-if="!filter.ui" :label="filter.label" :name="filter.name" :type="filter.type"
                        :values="filter.values" :options="filter.options" @filter="addFilter"></filter-input>
          <div class="col" v-else-if="filter.ui == 'filler-col'"></div>
          <div class="col" v-else-if="filter.ui == 'clear-btn'">
            <label for="clear-filter"><br/></label><br/>
            <div id="clear-filter" @click="clearFilter" class="btn btn-outline-info btn-block"><i class="material-icons" style="margin-top:-5px">clear_all</i> Clear Filters</div>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
import FilterInput from "./filter-input.vue";
let _ = window._;

export default {
  components : {
    FilterInput
  },
  data: function(){
    return {
      filterValues: {}
    };
  },
  props: {
    filters: {
      type: Array,
      required: true
    }
  },
  computed: {
    filterRows: function(){
      var rows = _.chunk(this.filters, 5), i;
      var lastRowCount = rows[rows.length - 1].length;
      if(lastRowCount < 5){
        for(i = 0; i < 4 - lastRowCount; i++)
          rows[rows.length - 1].push({ ui: 'filler-col' });
      } else{
        var extraRow = [];
        for(i = 0; i < 4; i++)
          extraRow.push({ ui: 'filler-col' });
        rows.push(extraRow);
      }
      rows[rows.length - 1].push({ ui: 'clear-btn' });
      return rows;
    }
  },
  methods: {
    addFilter: function(parameters){
      this.filterValues[parameters.field] = parameters.value;
      this.$root.dataTable.column(parameters.field + ':name').search(parameters.value).draw();
    },
    clearFilter: function(){
      this.$children.forEach(function(filter){
        filter.clear();
      });
      this.$root.dataTable.columns().search('').draw();
    }
  }
}
</script>