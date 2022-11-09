<template>
    <div
        class="form-input-container"
    >
        <div class="form-input-label"><span>{{ elementData.label[0].text }}：</span></div>

        <div class="form-input-label" v-for="(element,index) in elementData.label">
            <span>{{ element.text }}：</span>
        </div>
    </div>
</template>
<script>
export default {
    name: 'LabelText',
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