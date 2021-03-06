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
    <div class="">
        <div class="tab-content" v-if="getHostSnapshot">
            <div class="attribute-list clearfix">
                <div class="title clearfix">
                    <h3 class="fl">基本值</h3>
                    <div class="content fr clearfix">
                        <div class="info-title">最近更新时间：</div>
                        <div class="info-detail">{{getHostSnapshot.upTime}}</div>
                    </div>
                </div>
                <ul class="info-list">
                    <li class="attr-item clearfix">
                        <div class="item-title">loadavg：</div>
                        <div class="item-detail">{{getHostSnapshot.loadavg}}</div>
                    </li>
                    <li class="attr-item clearfix">
                        <div class="item-title">本地时间：</div>
                        <div class="item-detail">{{localTime}}</div>
                    </li>
                    <li class="attr-item clearfix">
                        <div class="item-title">总流入量：</div>
                        <div class="item-detail">{{getHostSnapshot.rcvRate}}Mb/s</div>
                    </li>
                    <li class="attr-item clearfix">
                        <div class="item-title">启动时间：</div>
                        <div class="item-detail">{{getHostSnapshot.bootTime}}</div>
                    </li>
                    <li class="attr-item clearfix">
                        <div class="item-title">总流出量：</div>
                        <div class="item-detail">Mb/s</div>
                    </li>
                    <li class="attr-item clearfix">
                        <div class="item-title">磁盘使用：</div>
                        <div class="item-detail">{{getHostSnapshot.Disk}}GB</div>
                    </li>
                    <li class="attr-item clearfix">
                        <div class="item-title">内存使用：</div>
                        <div class="item-detail">{{(getHostSnapshot.Mem / 1024).toFixed(2)}}GB</div>
                    </li>
                </ul>
            </div>
            <div class="chart-wrapper">
                <div class="chart-box">
                    <div id="chart1" class="chart-item"></div>
                </div>
                <div class="chart-box">
                    <div id="chart2" class="chart-item"></div>
                </div>
                <div class="chart-box">
                    <div id="chart3" class="chart-item"></div>
                </div>
            </div>
        </div>
        <div class="tab-content" v-else>
            <div class="box-content">
                <div class="box">
                    <div class="box-light"></div>
                    <div class="box-circle">
                        <div class="circle">
                            <img src="../../../common/images/box-circle.png" alt="">
                        </div>
                    </div>
                </div>
            </div>
            <p class="box-text">当前主机没有安装 Agent 或者 Agent 已经离线</p>
        </div>
    </div>
</template>

<script>
    import echarts from 'echarts'
    import {mapGetters} from 'vuex'
    import moment from 'moment'
    export default {
        data () {
            return {
                localTime: ''
            }
        },
        props: {
            isShow: {
                default: false,
                type: Boolean
            }
        },
        computed: {
            ...mapGetters([
                'getHostSnapshot'
            ])
        },
        watch: {
            isShow (val) {
                if (val) {
                    this.$nextTick(() => {
                        this.initChart()
                        this.getDate()
                    })
                }
            }
        },
        methods: {
            /*
                获取当前时间
            */
            getDate () {
                this.localTime = this.$formatTime(moment())
            },
            /*
                初始化图表
            */
            initChart () {
                let chart1 = echarts.init(document.getElementById('chart1'))
                let chart2 = echarts.init(document.getElementById('chart2'))
                let chart3 = echarts.init(document.getElementById('chart3'))
                chart1.setOption({
                    title: {
                        text: '总CPU使用率',
                        textStyle: {
                            color: '#bec6de'
                        },
                        x: 'center',
                        y: 'bottom'
                    },
                    series: [{
                        name: '',
                        type: 'gauge',
                        radius: '80%',
                        detail: {
                            formatter: '{value}%',
                            fontSize: 26,
                            offsetCenter: [0, '60%']
                        },
                        data: [{
                            value: this.getHostSnapshot.cpuUsage / 100,
                            name: ''
                        }]
                    }]
                })
                chart2.setOption({
                    title: {
                        text: '总内存使用率',
                        textStyle: {
                            color: '#bec6de'
                        },
                        x: 'center',
                        y: 'bottom'
                    },
                    series: [{
                        name: '',
                        type: 'gauge',
                        radius: '80%',
                        detail: {
                            formatter: '{value}%',
                            fontSize: 26,
                            offsetCenter: [0, '60%']
                        },
                        data: [{
                            value: this.getHostSnapshot.memUsage / 100,
                            name: ''
                        }]
                    }]
                })
                chart3.setOption({
                    title: {
                        text: '磁盘使用情况',
                        textStyle: {
                            color: '#bec6de'
                        },
                        x: 'center',
                        y: 'bottom'
                    },
                    series: [{
                        name: '',
                        type: 'gauge',
                        radius: '80%',
                        detail: {
                            formatter: '{value}%',
                            fontSize: 26,
                            offsetCenter: [0, '60%']
                        },
                        data: [{
                            value: this.getHostSnapshot.diskUsage / 100,
                            name: ''
                        }]
                    }]
                })
            }
        }
    }
</script>

<style lang="scss" scoped>
    .tab-content{
        padding-top: 62px!important;
        font-size: 14px;
        padding-left:40px;
        height: 100%;
        overflow: auto;
        &::-webkit-scrollbar{
            width: 6px;
            height: 5px;
        }
        &::-webkit-scrollbar-thumb{
            border-radius: 20px;
            background: #a5a5a5;
        }
        .attribute-list{
            margin-bottom:40px;
            .title{
                h3{
                    color:#bec6de;
                    font-size:14px;
                    font-weight: bold;
                    line-height:1;
                    margin: 0;
                    margin-bottom: 20px;
                }
                .content{
                    font-size: 12px;
                    padding-right: 30px;
                    .info-title{
                        float: left;
                        color: #bec6de;
                    }
                    .info-detail{
                        float: left;
                        color: #6b7baa;
                    }
                }
            }
            ul{
                li.attr-item{
                    font-size: 14px;
                    width: 50%;
                    float: left;
                    line-height:1;
                    color:#6b7baa;
                    margin-bottom: 20px;
                    padding-right: 20px;
                    .item-title{
                        float: left;
                    }
                    .item-detail{
                        float: left;
                        color: #4d597d;
                    }
                }
            }
        }
        .chart-wrapper{
            display: flex;
            padding-right: 20px;
            .chart-box{
                flex: 1;
                .chart-item{
                    // width: 100%;
                    height: 250px;
                }
            }
        }
        .box-content{
            width: 127px;
            height: 153px;
            margin: 40px auto;
            text-align: center;
            .box{
                width: 127px;
                height: 153px;
                background: url(../../../common/images/box.png) no-repeat;
                position: relative;
                .box-light{
                    position: absolute;
                    width: 127px;
                    left: 0;
                    background: url(../../../common/images/box-light.png) no-repeat;
                    -webkit-transform-origin: center bottom;
                    -moz-transform-origin: center bottom;
                    -o-transform-origin: center bottom;
                    -ms-transform-origin: center bottom;
                    transform-origin: center bottom;
                    -webkit-animation: light 2s ease-in-out .5s both;
                    -moz-animation: light 2s ease-in-out .5s both;
                    -ms-animation: light 2s ease-in-out .5s both;
                    -o-animation: light 2s ease-in-out .5s both;
                    animation: light 2s ease-in-out .5s both;
                }
                .box-circle{
                    perspective: 1200px;
                    position: relative;
                    .circle{
                        position: absolute;
                        top: -10px;
                        left: 26px;
                        width: 75px;
                        height: 75px;
                        transform-style: preserve-3d;
                        img{
                            position: absolute;
                            left: 0;
                            top: 0;
                            width: 100%;
                            height: 100%;
                        }
                    }
                }
            }
        }
        .box-text{
            width: 100%;
            text-align: center;
        }
    }
    @-webkit-keyframes light {
        from {
            height: 0;
            bottom: 70px;
        }
        to {
            height: 153px;
            top: 0;
            bottom: auto;
        }
    }
    @-moz-keyframes light {
        from {
            height: 0;
            bottom: 70px;
        }
        to {
            height: 153px;
            top: 0;
            bottom: auto;
        }
    }
    @-ms-keyframes light {
        from {
            height: 0;
            bottom: 70px;
        }
        to {
            height: 153px;
            top: 0;
            bottom: auto;
        }
    }
    @-o-keyframes light {
        from {
            height: 0;
            bottom: 70px;
        }
        to {
            height: 153px;
            top: 0;
            bottom: auto;
        }
    }
    @keyframes light {
        from {
            height: 0;
            bottom: 70px;
        }
        to {
            height: 153px;
            top: 0;
            bottom: auto;
        }
    }
</style>
