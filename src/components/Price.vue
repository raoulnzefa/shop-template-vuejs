<template>
    <div>
        <span>{{ this.prefix + Number.parseFloat(this.value * this.conversion).toFixed(this.precision) }}</span>
    </div>
</template>

<script>
    export default {
        name: "Price",
        data() {
            return {
            }
        },
        props: {
            value: Number,
            prefix: {
                type: String,
                default: '$'
            },
            precision: {
                type: Number,
                default: 2
            },
            conversion: {
                type: Number,
                default: 2
            }
        }
    }
</script>

<style scoped>

</style>
