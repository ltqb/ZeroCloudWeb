<template>
    <div>

        <div style="float: left;width: 50%;margin-bottom: 20px">
            <el-card class="box-card" style="font-family: Consolas;font-size: 15px;">
                <div slot="header" class="clearfix">
                    <span>容器基础信息</span>
                    <!--<el-button style="float: right; padding: 3px 0" type="text">操作按钮</el-button>-->
                </div>
                <div >容器类型 ：Docker
                </div>
                <div class="text item">创建时间 ：2020-03-20 10:00:00
                </div>
                <div class="text item">容器版本 ：18.09.7
                </div>
                <div class="text item">Go版本 ：go1.10.8
                </div>
                <div class="text item">KernelVersion ：3.10.0-1062.el7.x86_64
                </div>

            </el-card>
        </div>
        <div style="float: right; width: 49%">
            <el-card class="box-card" style="font-family: Consolas;font-size: 15px">
                <div slot="header" class="clearfix">
                    <span>集群基础信息</span>
                </div>
                <div class="text item">容器管理 ：Kubernetes
                </div>
                <div class="text item">创建时间 ：2020-03-20 10:00:00
                </div>
                <div class="text item">云服务 ：私有云
                </div>
                <div class="text item">Go版本 ：go1.10.8
                </div>
                <div class="text item">Kubernetes版本 ：v1.15.3
                </div>
                <div class="text item">
                </div>
            </el-card>
        </div>
        <div style="margin-top: 10px"></div>
        <el-row>
            <el-col>
                <el-card style="float: left; width:30%;height: 170px">
                    <el-progress type="circle" :percentage="20" align="center" style="float: left"></el-progress>
                    <div style="height: 30px"></div>
                    <div style="font-size: 18px;font-family: 微软雅黑" align="center"> 剩余CPU : 3000.00</div>
                    <div style="font-size: 18px;font-family: 微软雅黑" align="center"> CPU总数 : 4000.00</div>
                </el-card>
                <div style="float: left;width:10px; visibility:hidden">5</div>
                <el-card style="float: left;width:30%; height: 170px">
                    <el-progress type="circle" :percentage="52" align="center" style="float: left"></el-progress>
                    <div style="height: 30px"></div>
                    <div style="font-size: 18px;font-family: 微软雅黑" align="center">剩余内存 : 122200.00</div>
                    <div style="font-size: 18px;font-family: 微软雅黑" align="center">内存总数 : 1233330.00</div>
                </el-card>
                <div style="float: left;width:10px; visibility:hidden">5</div>
                <el-card style="float: left; width:12%;height: 170px" align="center">
                    <div>
                        <el-button type="text" icon="el-icon-monitor" style="font-size: 40px"></el-button>
                    </div>
                    <div style="font-size: 40px; font-family: 微软雅黑 ;">{{this.pageTotal}}</div>
                    <div style="font-size: 14px;font-family: 微软雅黑">主机总数</div>
                </el-card>
                <div style="float: left;width:3px; visibility:hidden">5</div>
                <el-card style="float: left; width:12%;height: 170px" align="center">
                    <div>
                        <el-button type="text" icon="el-icon-suitcase" style="font-size: 40px"></el-button>
                    </div>
                    <div style="font-size: 40px; font-family: 微软雅黑 ;">12</div>
                    <div style="font-size: 14px;font-family: 微软雅黑">服务总数</div>
                </el-card>
                <div style="float: left;width:3px; visibility:hidden">14</div>
                <el-card style="float: left; width:12%;height: 170px" align="center">
                    <div>
                        <el-button type="text" icon="el-icon-files" style="font-size: 40px"></el-button>
                    </div>
                    <div style="font-size: 40px; font-family: 微软雅黑 ;">{{this.podTotal}}</div>
                    <div style="font-size: 14px;font-family: 微软雅黑">POD总数</div>
                </el-card>

            </el-col>
        </el-row>
        <div style="margin-top: 10px"></div>
        <div class="handle-box">
            <el-input class="handle-input mr10" placeholder="搜索"></el-input>
            <el-button type="primary" icon="el-icon-plus" @click="handersearch">新增</el-button>
            <el-button type="primary" icon="el-icon-edit" @click="handersearch">修改</el-button>
            <el-button type="primary" icon="el-icon-refresh" @click="handersearch">刷新</el-button>

        </div>
        <div style="margin-top: 10px"></div>
        <el-table
                :data="infraList"
                border class="table" ref="multipleTable"
                header-cell-class-name="table-header"
        >
            <el-table-column label="主机名">
                <template slot-scope="scope">{{scope.row.hostname}}</template>
            </el-table-column>
            <el-table-column label="IP地址">
                <template slot-scope="scope">{{scope.row.ip_address}}</template>
            </el-table-column>
            <el-table-column label="MAC地址">
                <template slot-scope="scope">{{scope.row.mac_address}}</template>
            </el-table-column>
            <el-table-column label="创建时间">
                <template slot-scope="scope">{{scope.row.create_time}}</template>
            </el-table-column>
            <el-table-column label="状态">
                <template slot-scope="scope">
                      <el-tag
                            :type="scope.row.device_status==='Running'?'success':(scope.row.device_status==='Error'?'danger':'')"
                        >{{scope.row.device_status}}</el-tag>
                </template>
            </el-table-column>

        </el-table>
    </div>

</template>
<script>
    import axios from "axios";

    export default {
        name: "infra",
        data() {
            return {
                query: {
                name: '',
                pageIndex: 1,
                pageSize: 8
            },
                infraList: [],
                result:0,
                editVisible: false,
                pageTotal: 0,
                podTotal: 0,
                form: {},
                idx: -1,
                id: -1
            }
        },
        mounted() {
            this.created();
        },
        methods: {
            created() {
                axios.get('/api/alldeviceinfo').then(response => {
                    this.infraList = response.data.devices;
                    this.pageTotal=response.data.device_total;
                    this.podTotal=response.data.podTotal;
                }, response => {
                    console.log("error");
                });
            }
        }
    }
</script>

<style scoped>
    handle-box {
        margin-top: 20px;
        margin-bottom: 20px;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
    }

    .table {
        width: 100%;
        font-size: 14px;
        font-family: Verdana;
    }

    .red {
        color: #ff0000;
    }

    .mr10 {
        margin-right: 10px;
    }

    .table-td-thumb {
        display: block;
        margin: auto;
        width: 40px;
        height: 40px;
    }
</style>