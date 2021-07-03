<template>
  <th>
    <slot></slot>
    <slot name="actions" v-if="renderActionsTemplate"></slot>
  </th>
</template>

<script>
var $ = window.$;

export default {
  name: 'DtColumn',
  props: {
    data: [String, Function],
    render: [String, Function],
    name: {
      type: String,
      default: undefined
    },
    searchable: {
      type: Boolean,
      default: true
    },
    orderable: {
      type: Boolean,
      default: true
    },
    visible: {
      type: Boolean,
      default: true
    },
    width: {
      type: String,
      default: undefined
    },
    defaultContent: String,
    className: String,
    last: {
      type: Boolean,
      default: false
    }
  },
  data(){
    return {
      // A temporary hack to create the actual DOM of the action buttons if injected to its slot by the user.
      // Which will be used in the actions column for each fetched datatable row.
      renderActionsTemplate: true
    };
  },
  methods: {
    renderActions(row){
      var actions = [], element, idKey;
      this.$slots.actions().forEach(function(action){
        element = $(action.el);
        idKey = element.attr('data-id');
        element.attr('data-id', row[(!idKey)? 'id' : idKey]);
        actions.push($('<div/>').append(element).html());
      });
      return actions.join(' ');
    }
  },
  mounted(){
    var column = {};
    var props = ['data', 'render', 'name', 'searchable', 'orderable', 'visible', 'defaultContent', 'className', 'width'];
    for(var i in props)
      if(this.$props[props[i]] !== undefined)
        column[props[i]] = this.$props[props[i]];
    if(this.$slots.actions !== undefined)
      column.data = this.renderActions;
    this.$parent.columns.push(column);
    // Removes the action column elements template form DOM after it's rendered.
    this.renderActionsTemplate = false;
    if(this.last)
      this.$parent.init();
  }
}
</script>
