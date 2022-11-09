<template>
    <div
        class="form-select-container"
    >
        <div class="form-select-label"><span>{{ elementData.label }}：</span></div>
        <div class="form-select-content">
            <label>
                <Select
                    :type="elementData.type"
                    v-bind="$attrs"
                    v-model="computedValue"
                    :multiple="elementData.multiple"
                    placeholder="请输入"
                >
                    <Option v-for="item in elementData.list" :value="item.value" :key="item">{{ item.label }}</Option>
                </Select>
            </label>

        </div>
    </div>
</template>
<script>
export default {
    name: 'SingleSelect',
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
.form-select-container {
    .form-select-label {
        span {
            overflow: hidden;
            text-overflow:ellipsis;
            white-space: nowrap;
            font-size: 14px;
        }
    }
    .form-select-content {
        margin-top: 4px;
    }
}
</style>