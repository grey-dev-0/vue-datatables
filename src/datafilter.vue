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
export default {
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
    methods: {
        filter: function(parameters){
            this.filters[parameters.field] = parameters.value;
            this.$root.dataTable.column(parameters.field + ':name').search(parameters.value).draw();
        },
        clear: function(){
            this.$children.forEach(function(filter){
                filter.clear();
            });
            this.$root.dataTable.columns().search('').draw();
        }
    }
}
</script>