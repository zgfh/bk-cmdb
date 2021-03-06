/*
 * Tencent is pleased to support the open source community by making 蓝鲸 available.
 * Copyright (C) 2017-2018 THL A29 Limited, a Tencent company. All rights reserved.
 * Licensed under the MIT License (the "License"); you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at http://opensource.org/licenses/MIT
 * Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and limitations under the License.
 */

<template>
    <ul class="tree-wrapper" :class="treeType">
        <tree-item class="root" :key='index' v-for="(item, index) in treeDataSource"
        :model.sync="item"
        :hasCheckbox="hasCheckbox"
        :trees.sync="treeDataSource"
        :callback="callback"
        :expand="expand"
        :treeType="treeType"
        :pageTurning="pageTurning"
        :pageSize="pageSize"
        ></tree-item>
    </ul>
</template>

<script type="text/javascript">
    import treeItem from './treeItem'
    import $ from 'jquery'
    export default {
        props: {
            pageSize: {
                type: Function
            },
            pageTurning: {
                type: Function
            },
            // 树形图类型 默认为空 可选值 list
            treeType: {
                type: String,
                default: ''
            },
            // 点击展开回调
            expand: {
                type: Function
            },
            // 点击节点回调
            callback: {
                type: Function
            },
            list: {
                type: Array
            },
            isOpen: {
                default: false,
                type: Boolean
            },
            /*
                是否包含有checkbox
            */
            hasCheckbox: {
                default: false,
                type: Boolean
            }
        },
        data () {
            return {
                treeDataSource: [],
                level: 0
            }
        },
        watch: {
            'list': {
                handler: function () {
                    this.initTreeData()
                },
                deep: true
            }
        },
        methods: {
            getTree () {
                return this.treeDataSource
            },
            initTreeData () {
                var tempList = JSON.parse(JSON.stringify(this.list))
                // 递归操作，增加删除一些属性。比如: 展开/收起
                var recurrenceFunc = (data, level) => {
                    data.forEach(m => {
                        if (this.hasCheckbox) {
                            if (!m.hasOwnProperty('isChecked')) {
                                m.isChecked = false
                            }
                        }
                        if (!m.hasOwnProperty('clickNode')) {
                            // this.$set(m, 'clickNode', false)
                            // m.clickNode = false
                            m.clickNode = m.hasOwnProperty('clickNode') ? m.clickNode : false
                        }
                        m.children = m.children || []

                        if (!m.hasOwnProperty('isFolder')) {
                            m.isFolder = m.hasOwnProperty('open') ? m.open : this.isOpen
                        }
                        if (!m.hasOwnProperty('isExpand')) {
                            m.isExpand = m.hasOwnProperty('open') ? m.open : this.isOpen
                        }
                        m.loadNode = 0

                        m.level = level
                        let tempLevel = level
                        recurrenceFunc(m.children, ++tempLevel)
                    })
                }
                recurrenceFunc(tempList, 1)

                this.treeDataSource = tempList
            }
        },
        update () {
            this.initTreeData()
        },
        mounted () {
            this.$nextTick(() => {
                this.initTreeData()
            })
        },
        components: {
            treeItem
        }
    }
</script>

<style media="screen" lang="scss" scoped>
    ul {
        line-height: 1.5em;
        list-style-type: dot;
    }
    .root{
        &:before{
            width: 0 !important;
            height: 0 !important;
        }
    }
    .tree-wrapper.list{
        border: 1px solid #bec6de;
        border-bottom: 0;
        .tree-item-wrapper{
            border-bottom: 1px solid #bec6de;
        }
    }
</style>

<style lang="scss">
    $borderColor: #bec6de;
    .tree-wrapper.list{
        .tree-item-wrapper{
            color: #6b7baa;
            padding: 0;
            &:before{
                width: 0;
                height: 0;
            }
            &:after{
                width: 0;
                height: 0;
            }
            .item-active{
                background: #f1f7ff;
                .item-name{
                    color: #6b7baa;
                    &.active{
                        color: #3c96ff;
                        .icon{
                            color: #3c96ff;
                        }
                        .icon-none{
                            background: #3c96ff;
                        }
                    }
                }
            }
        }
        .root{
            >.item{
                &.open{
                    border-bottom: 1px solid $borderColor;
                }
                .tree-icon{
                    left: 0;
                }
            }
            .item{
                height: 36px;
                line-height: 36px;
                paddint-top: 15px;
                &:hover{
                    background: #f1f7ff;
                }
                &:before{
                    padding: 0;
                    width: 0;
                    height: 0;
                }
                .item-name{
                    border-right-width: 13px;
                    .icon{
                        margin-right: 10px;
                        vertical-align: text-bottom;
                        font-size: 14px;
                        color: #ffb400;
                    }
                    .icon-none{
                        display: inline-block;
                        margin-top: -2px;
                        margin-right: 8px;
                        border-radius: 2px;
                        width: 10px;
                        height: 10px;
                        background: #c3cdd7;
                    }
                    .count{
                        float: right;
                        display: inline-block;
                        vertical-align: text-bottom;
                        margin-top: 11px;
                        margin-left: 10px;
                        color: #ffb80f;
                        background: #fff7e5;
                        border-radius: 7px;
                        height: 14px;
                        font-size: 12px;
                        line-height: 14px;
                        padding: 0 8px;
                    }
                }
                .tree-icon{
                    top: 10px;
                    font-size: 14px;
                    color: #498fe0;
                    background: none;
                    transition: all .2s;
                }
                .tree-icon-open{
                    transform: rotate(-135deg);
                }
                .tree-icon-close{
                    color: #bec6de;
                    transform: rotate(-180deg);
                }
                .item-loading{
                    top: 10px;
                }
            }
        }
    }
</style>

