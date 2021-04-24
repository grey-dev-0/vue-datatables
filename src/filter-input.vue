<template>
  <div class="col">
    <label :for="cssId" class="control-label">{{ label }}</label>
    <select :id="cssId" class="form-control" v-if="type == 'select' || type == 'select2'" v-model="input" @change="filter(name, input)">
      <option value="">-- {{ label }} --</option>
      <option v-for="(value, id) in values" :value="id" :key="id">{{ value }}</option>
    </select>
    <input autocomplete="off" type="text" class="form-control" v-else :id="cssId" v-model="input" @keyup="debouncedFilter(name, input)" :placeholder="label">
  </div>
</template>

<script>
let $ = window.$;
let _ = window._;

export default {
  mounted: function(){
    this.$nextTick(function() {
      var component = this;
      this.$parent.filterValues[this.name] = this.input;
      if(this.type == 'select2'){
        var select = $('#' + this.cssId);
        select.select2().on('change', function(){
          component.filter(component.name, select.val())
        });
      } else if(this.type == 'date'){
        var opens = (this.options !== undefined)? (this.options.opens || 'right') : 'right';
        $('#' + this.cssId).daterangepicker({
          showDropdowns: true,
          autoUpdateInput: false,
          opens: opens,
          locale: {
            cancelLabel: 'Clear'
          }
        }).on('apply.daterangepicker', function(e, picker) {
          var startDate = picker.startDate.format('YYYY-MM-DD');
          var endDate = picker.endDate.format('YYYY-MM-DD');
          $(this).val((startDate == endDate)? startDate : (startDate + ' to ' + endDate));
          component.filter(component.name, $(this).val())
        }).on('cancel.daterangepicker', function() {
          $(this).val('');
          component.filter(component.name, $(this).val())
        });
      }
    });
  },
  props: {
    name: {
      type: String,
      required: true
    },
    label: {
      type: String,
      required: true
    },
    type: {
      type: String,
      required: true
    },
    values: {
      type: Object
    },
    options: {
      type: Object
    }
  },
  data: function(){
    return {
      input: ''
    };
  },
  computed: {
    cssId: function(){
      return this.name.replace('.', '-') + '-filter-' + this.type;
    }
  },
  methods: {
    debouncedFilter: _.debounce(function(field, value){
      this.filter(field, value);
    }, 200),
    filter: function(field, value){
      this.$emit('filter', {
        field: field,
        value: value
      });
    },
    clear: function(){
      this.input = '';
      if(this.type == 'date' || this.type == 'select2')
        $('#' + this.cssId).val('');
      if(this.type == 'select2')
        $('#' + this.cssId).select2('destroy').select2();
      this.$parent.filterValues[this.name] = '';
    }
  }
}
</script>