<template>
  <th v-html="label"></th>
</template>

<script>
export default {
  props: {
    label: {
      type: String,
      required: true
    },
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
    className: String
  },
  mounted(){
    var column = {};
    var props = ['data', 'render', 'name', 'searchable', 'orderable', 'visible', 'defaultContent', 'className', 'width'];
    for(var i in props)
      if(this.$props[props[i]] !== undefined)
        column[props[i]] = this.$props[props[i]];
    this.$parent.columns.push(column);
    this.$parent.setup();
  }
}
</script>