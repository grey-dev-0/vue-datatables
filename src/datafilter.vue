<template>
    <div class="list-group list-group-flush">
        <div :class="'list-group-item pb-0 bg-' + color">
            <div class="row">
                <slot></slot>
            </div>
        </div>
    </div>
</template>

<script>
import emitter from 'mitt';

export default {
    name: 'VueDatafilter',
    data: function(){
        return {
            filters: {}
        };
    },
    props: {
        cols: {
            type: Number,
            default: 5
        },
        color: {
            type: String,
            default: 'grey-10'
        }
    },
    computed: {
        count: function(){
            return Object.keys(this.filters).length;
        }
    },
    mounted: function(){
        this.$root.emitter = emitter();
    },
    methods: {
        filter: function(field, value){
            this.filters[field] = value;
            this.$root.dataTable.column(field + ':name').search(value).draw();
        },
        clear: function(){
            this.$root.emitter.emit('clear');
            this.$root.dataTable.columns().search('').draw();
        }
    }
}
</script>