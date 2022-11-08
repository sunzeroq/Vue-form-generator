<template>
    <div class="custom-form-container">
        <div class="custom-form-select">
            <draggable
                class="form-drag-list"
                :list="list"
                :clone="handleClone"
                :group="{ name: 'people', pull: 'clone', put: false }"
            >
                <div
                    class="form-drag-item"
                    v-for="element in list"
                    :key="element.key"
                >
                    {{ element.name }}
                </div>
            </draggable>
        </div>
        <div class="custom-form-content" ref="form">
            <draggable
                class="flow-form-container"
                :list="formConfig"
                group="people"
                @choose="select"
            >
                <component
                    v-for="(item, index) in formConfig"
                    v-bind:key="index"
                    :ref="item.id"
                    v-show="handleShow(item.step, item.key)"
                    :is="item.key"
                    :elementData="item.config"
                    :formData="tableData"
                    :style="{'grid-column-start': item.width}"
                    @buttonEvent="handleBtnEvent"
                    @next="handleNext"
                ></component>
            </draggable>
        </div>
        <div class="custom-form-setting">
            <Input v-model="formName"></Input>
            <Button @click="getJson">GetJson</Button>
            <Tabs value="name1" style="height: 100%;">
                <TabPane label="字段属性" name="name1">
                    <div v-if="formConfig[index]">
                        <label>
                            Key
                            <Input v-model="formConfig[index].config.key" ></Input>
                        </label>
                        <div v-if="formConfig[index].key === 'InputComponent'">
                            <label>
                                标签
                                <Input v-model="formConfig[index].config.label" ></Input>
                            </label>
                            <label>
                                类型
                                <Select v-model="formConfig[index].config.type" >
                                    <Option value="text" label="文本"></Option>
                                    <Option value="textarea" label="文本框"></Option>
                                    <Option value="number" label="数字"></Option>
                                    <Option value="password" label="密码"></Option>
                                </Select>
                            </label>
                            <Checkbox v-model="formConfig[index].config.disable">Disable</Checkbox>
                        </div>
                        <div v-if="formConfig[index].key === 'SingleSelect'">
                            <label>
                                标签
                                <Input v-model="formConfig[index].config.label" ></Input>
                            </label>
                            <Checkbox v-model="formConfig[index].config.multiple">多选</Checkbox>
                            <div
                                v-for="(item, i) in formConfig[index].config.list"
                                class="form-edit-select-array"
                            >
                                <label>
                                    Label
                                    <Input v-model="item.label"></Input>
                                </label>
                                <label>
                                    Value
                                    <Input v-model="item.value"></Input>
                                </label>
                                <Button type="error" size="small" @click="remove(index, i, 'SingleSelect')">删除</Button>
                            </div>
                            <Button @click="add(index, 'SingleSelect')">Add</Button>
                        </div>
                        <div v-if="formConfig[index].key ==='DataPickerCom'">
                            <label>
                                标签
                                <Input v-model="formConfig[index].config.label" ></Input>
                            </label>
                            <Checkbox v-model="formConfig[index].config.disable">Disable</Checkbox>
                        </div>
                        <div v-if="formConfig[index].key === 'LabelText'">
                            <label>
                                字体大小
                                <Input v-model="formConfig[index].config.fontSize" type="number"></Input>
                            </label>
                            <div
                                v-for="(item, i) in formConfig[index].config.label"
                                class="form-edit-select-array"
                            >
                                <label>
                                    文本内容
                                    <Input v-model="item.text"></Input>
                                </label>
                                <label>
                                    类型
                                    <Select v-model="item.type" >
                                        <Option value="text" label="文本"></Option>
                                        <Option value="title" label="标题"></Option>
                                    </Select>
                                </label>
                                <Button @click="remove(index, i, 'LabelText')" type="error">删除</Button>
                            </div>
                            <Button @click="add(index, 'LabelText')">Add</Button>
                        </div>
                        <div v-if="formConfig[index].key ==='TextMixInput'">
                            <div
                                v-for="(item, i) in formConfig[index].config.text"
                            >
                                <label>
                                    前缀
                                    <Input v-model="item.preLabel"></Input>
                                </label>
                                <label>
                                    后缀
                                    <Input v-model="item.afterLabel"></Input>
                                </label>
                                <label>
                                    Key
                                    <Input v-model="item.key"></Input>
                                </label>
                                <label>
                                    是否可编辑
                                    <checkbox v-model="item.disable"></checkbox>
                                </label>
                                <Button @click="remove(index, i, 'TextMixInput')" type="error">删除</Button>
                            </div>
                            <Button @click="add(index, 'TextMixInput')">新增</Button>
                        </div>
                        <div v-if="formConfig[index].key === 'CascaderSelect'">
                            <label>
                                标签
                                <Input v-model="formConfig[index].config.label"></Input>
                            </label>
                        </div>
                        <Input
                            type="textarea"
                            :autosize="true"
                            :value="JSON.stringify(formConfig[index].config)"
                            @input="handleData"
                        ></Input>
                    </div>
                </TabPane>
                <TabPane label="表单属性" name="name2">
                    <div v-if="formConfig[index]">
                        <!--                        {{ formConfig[index] }}-->
                        <label>
                            Width:
                            <Select v-model="formConfig[index].width">
                                <Option value="span 1" label="25%"></Option>
                                <Option value="span 2" label="50%"></Option>
                                <Option value="span 3" label="75%"></Option>
                                <Option value="span 4" label="100%"></Option>
                            </Select>
                        </label>
                        Step:
                        <Input v-model="formConfig[index].step" disabled></Input>
                        <Button @click="removeElement(index)" type="error">删除</Button>
                    </div>
                </TabPane>
            </Tabs>
        </div>
    </div>
</template>
<script>
import draggable from "vuedraggable";
import FlowForm from "@/components/FlowForm";
import {InputComponent} from "@/components"
// import { RowTimeLine, NumberTitle, CascaderSelect, SingleSelect, InputComponent, TextMixInput, TableComponent, OpinionComponent,  LabelText, DataPickerCom, UploadPage } from '@/components'
export default {
    components: {
        FlowForm, draggable,
        // RowTimeLine,
        // NumberTitle,
        // CascaderSelect,
        // SingleSelect,
        InputComponent,
        // TextMixInput,
        // TableComponent,
        // OpinionComponent,
        // LabelText,
        // DataPickerCom,
        // UploadPage
    },
    data: function() {
        return {
            formName: '',
            step: 0,
            tableData: {
                projects: [
                    {}
                ]
            },
            formConfig: [],
            list: [
                {
                    name: '输入框',
                    key: 'InputComponent',
                    width: 'span 4',
                    step: 0,
                    config: {
                        label: '默认输入文本',
                        key: 'licenseNumber',
                        type: 'text',
                        disable: false,
                    },
                },
                {
                    name: '下拉单选框',
                    key: 'SingleSelect',
                    width: 'span 2',
                    step: 0,
                    config: {
                        key: 'prjType',
                        label: '默认下拉文本',
                        multiple: false,
                        list: [
                            {
                                value: '1',
                                label: '下拉默认选项1'
                            },
                            {
                                value: '2',
                                label: '下拉默认选项2'
                            }
                        ],
                    }
                },
                {
                    name: '日期选择',
                    key: 'DataPickerCom',
                    width: 'span 2',
                    step: 0,
                    disable: false,
                    config: {
                        label: '默认文本',
                        key: 'endTime',
                    }
                },
                {
                    name: '纯文本',
                    key: 'LabelText',
                    width: 'span 4',
                    step: 0,
                    config: {
                        fontSize: 14,
                        label: [
                            {
                                type: 'text',
                                text: '单行纯文本'
                            }
                        ]
                    }
                },
                {
                    name: '表格',
                    key: 'TableComponent',
                    width: 'span 4',
                    step: 0,
                    config: {
                        key: 'projects',
                        column: [
                            {
                                name: "项目名称",
                                value: "prjName"
                            },
                            {
                                name: "总面积（平方米）",
                                value: "totalConsArea",
                            },
                            {
                                name: "单位造价（万元)",
                                value: "averageCost",
                            },
                            {
                                name: "总造价（万元）",
                                value: "prjCost",
                            }
                        ],
                        regulation: ['prjCost', 'averageCost', 'totalConsArea']
                    }
                },
                {
                    name: '文本输入',
                    key: 'TextMixInput',
                    width: 'span 4',
                    step: 0,
                    config: {
                        text: [
                            {
                                preLabel: '(受理编号为：',
                                afterLabel: '）',
                                key: 'acceptNumber',
                                disable: false
                            }
                        ]
                    }
                },
                {
                    name: '级联选择',
                    key: 'CascaderSelect',
                    width: 'span 2',
                    step: 0,
                    config: {
                        key: 'handleType',
                        label: '默认级联文本',
                        handlerTypeList: [
                            {
                                label: '规划相关',
                                value: 'A',
                                children: [
                                    {
                                        label: '详细规划报批（AX）',
                                        value: 'XXGHBPAX'
                                    }
                                ]
                            }
                        ]
                    }
                }
            ],
            index: 0,
            state: false
        };
    },
    methods: {
        //选中改变样式、右侧属性列
        select(e) {
            this.index = e.oldIndex;
            let arr = this.formConfig;
            for (let i = 0; i < arr.length; i++) {
                let uuid = arr[i].id;
                this.selectAllRef(uuid, (e) => {
                    e.classList.remove('form-item-selected')
                })
            }
            e.item.classList.add('form-item-selected')
        },
        //全部元素选择器
        selectAllRef(key, callback) {
            let arr = this.$refs[key]
            for (let i = 0; i < arr.length; i++) {
                callback(arr[i].$el);
            }
        },
        handleClone(e) {
            return  {
                id: this.generateUUID(),
                key: e.key,
                width: e.width,
                step: e.step,
                config: JSON.parse(JSON.stringify(e.config))
            }
        },
        generateUUID(){
            let d = new Date().getTime();
            const uuid = 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
                const r = (d + Math.random() * 16) % 16 | 0;
                d = Math.floor(d / 16);
                return (c === 'x' ? r : (r & 0x7 | 0x8)).toString(16);
            });
            return uuid;
        },
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
        },
        getJson() {
            this.$Modal.confirm({
                content: JSON.stringify(this.formConfig, null, 4),
                onOk: () => {
                    console.log(this.formName)
                    console.log(this.formConfig)
                }
            })
        },
        getTableData() {
            this.$Modal.confirm({
                content: JSON.stringify(this.tableData, null, 4)
            })
        },
        handleData(e) {
            console.log(e)
            this.formConfig[this.index].config = JSON.parse(e)
        },
        add(index, key) {
            if (key === 'SingleSelect') {
                let arr = this.formConfig[index].config.list;
                arr.push({
                    label: '新增选项',
                    value: this.generateUUID(),
                })
            } else if (key === 'LabelText') {
                let arr = this.formConfig[index].config.label;
                arr.push({
                    type: 'text',
                    text: '新增单行文本' + this.generateUUID(),
                })
            } else if (key === 'TextMixInput') {
                let arr = this.formConfig[index].config.text;
                arr.push({
                    preLabel: '前缀',
                    afterLabel: '后缀',
                    key: this.generateUUID(),
                    disable: false
                })
            }
        },
        remove(index, i, key) {
            if (key === 'SingleSelect') {
                let arr = this.formConfig[index].config.list;
                arr.splice(i, 1);
            } else if (key === 'LabelText') {
                let arr = this.formConfig[index].config.label;
                arr.splice(i, 1);
            } else if (key === 'TextMixInput') {
                let arr = this.formConfig[index].config.text;
                arr.splice(i, 1);
            }
        },
        removeElement(index) {
            console.log('test')
            this.formConfig.splice(index, 1)
        }
    }
};
</script>
<style scoped lang="scss">
.custom-form-container {
    display: flex;
    margin: 16px;
    height: calc(100% - 64px);
    .custom-form-select {
        width: 250px;
        border-right: solid 1px #e9e9e9;
    }
    .custom-form-setting {
        width: 350px;
        padding-left: 24px;
        border-left: solid 1px #e9e9e9;
    }
    .custom-form-content {
        flex: 1;
        margin: 0 32px;
    }
}
.form-drag-list {
    display: flex;
    display: -webkit-flex;
    flex-wrap: wrap;
    .form-drag-item {
        height: 32px;
        padding: 4px 8px;
        margin-right: 8px;
        margin-bottom: 24px;
        background-color: #e5f4ff;
        font-size: 14px;
        border: #e5f4ff solid 1px;
        border-radius: 4px;
        cursor: move;
    }
}
.form-item-selected {
    background-color: rgba(236, 245, 255, 1);
    outline: 2px solid #409EFF;
}
.flow-form-container {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-template-rows: auto 1fr; /* NEW */
    grid-row-gap: 24px;
    grid-column-gap: 48px;
}
.form-edit-select-array {
    display: flex;
    display: -webkit-flex;
    button {
        margin: auto auto 0 auto;
        height: 32px;
    }
}
</style>