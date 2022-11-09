<template>
    <div
        class="form-input-container"
    >
        <div class="form-input-content">
            <Table
                :type="elementData.type"
                v-bind="$attrs"
                v-model="computedValue"
                :columns="elementData.column"

                placeholder="请输入"
            />
        </div>
    </div>
</template>
<script>
export default {
    name: 'InputComponent',
    props: ["elementData", "formData"],
    data() {
        return {
            key: this.elementData.key
        }
    },
    computed: {
        computedValue: {
            get() {
                return this.formData[this.key];
            },
            set(val) {
                let o = {
                    key: this.key,
                    value: val
                }
                this.$emit("input", o);
            }
        },
    }
};
</script>
<style lang='scss' scoped>
.form-input-container {
    .form-input-label {
        span {
            overflow: hidden;
            text-overflow:ellipsis;
            white-space: nowrap;
            font-size: 14px;
        }
    }
    .form-input-content {
        margin-top: 4px;
    }
}
</style>