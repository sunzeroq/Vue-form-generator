<template>
    <div class="flow-form-container">
        <component
                    v-for="(item, index) in formConfig"
                    v-bind:key="index"
                    v-show="handleShow(item.step, item.key)"
                    :is="item.key"
                    :elementData="item.config"
                    :formData="tableData"
                    :style="{'grid-column-start': item.width}"
                    @buttonEvent="handleBtnEvent"
                    @input="changeData"
                    @next="handleNext"
            ></component>
    </div>
</template>
<script>
    export default {
        components: {
        },
        model: {
            prop: 'tableData',
            event: 'change'
        },
        props: {
            tableData: Object,
            config: Array
        },
        data: function() {
            return {
                step: 0,
            };
        },
        methods: {
            handleBtnEvent(e) {
                if (e === 'save') {
                    console.log(this.tableData);
                    this.$emit('save', this.tableData);
                } else if (e === 'cancel') {
                    this.$emit('cancel');
                }
            },
            changeData(e) {
                console.log('1213', e);
                //组件元素数据 响应式绑定到tableData
                this.$set(this.tableData, e.key, e.value);
            },
            handleNext(e) {
                this.step = e
            },
            handleShow(e, key) {
                return key === "RowTimeLine" || this.step === e;
            }
        },
        computed: {
            formConfig: function () {
                return this.config
            }
        }
    };
</script>
<style scoped lang="scss">
.flow-form-container {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-row-gap: 24px;
    grid-column-gap: 48px;
}
</style>