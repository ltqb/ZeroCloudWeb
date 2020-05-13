<template>
    <div  class="container">
        <div class="handle-box">
            <el-input class="handle-input mr10"  placeholder="搜索"></el-input>
            <el-button type="primary" icon="el-icon-plus" @click="handlecreate">新建应用</el-button>
        </div>
        <el-table :data="appList.slice((query.pageIndex-1)*query.pageSize,query.pageIndex*query.pageSize)" border class="table" ref="multipleTable" header-cell-class-name="table-header">
             <el-table-column type="selection" width="55" align="center"></el-table-column>
             <el-table-column label="名称" align="center">
                 <template slot-scope="scope"><el-link>{{scope.row.name}}</el-link></template>
                </el-table-column>
             <el-table-column label="命令空间" align="center">
                 <template slot-scope="scope">{{scope.row.namespace}}</template>
             </el-table-column>
             <el-table-column label="状态" align="center" width="200px">
                    <template slot-scope="scope">
                        <el-tag class="mr10"
                            :type="scope.row.status==='Running'?'success':(scope.row.status==='Error'?'danger':'')"
                          mgr>{{scope.row.status}}</el-tag>

                        <el-tag :type="scope.row.ready_replicas===scope.row.replicas?'success':(scope.row.status==='Error'?'danger':'')">
                            {{scope.row.ready_replicas+"/"+scope.row.replicas}}
                        </el-tag>

                    </template>
                </el-table-column>
             <el-table-column label="镜像" align="center">
                    <template slot-scope="scope">{{scope.row.images}}</template>
                </el-table-column>
             <!--<el-table-column label="类型" align="center">-->
                    <!--<template slot-scope="scope">{{scope.row.sourcetype}}</template>-->
                <!--</el-table-column>-->
             <el-table-column label="大小" align="center" width="50px">
                    <template slot-scope="scope"></template>
                </el-table-column>
             <el-table-column label="操作" align="center">
                    <template slot-scope="scope">
                        <el-button icon="el-icon-video-pause" type="text primary" ></el-button>
                        <el-button icon="el-icon-edit" type="text primary"></el-button>
                        <el-button icon="el-icon-delete" type="text primary"></el-button>
                    </template>
                </el-table-column>
        </el-table>


        <div class="pagination">
                <el-pagination
                    background
                    layout="total, prev, pager, next"
                    :current-page="query.pageIndex"
                    :page-size="query.pageSize"
                    :total="pageTotal"
                    @current-change="handlePageChange"
                ></el-pagination>
            </div>
    </div>
</template>

<script>
    import axios from "axios";
    export default {
        name: "apps",
        data() {
            return {
                query: {
                name: '',
                pageIndex: 1,
                pageSize: 6
            },
                appList: [],
                multipleSelection: [],
                delList: [],
                result:0,
                editVisible: false,
                pageTotal: 0,
                form: {},
                idx: -1,
                id: -1
            }
        },
        mounted() {
            this.get_apps();
        },
        methods:{
        get_apps(){
            axios.get('/api/apps').then(reg=>{
                this.appList=reg.data.apps;
                this.pageTotal=reg.data.total;
                console.log(reg.data)
            })
        },
        handlePageChange(val){
            this.$set(this.query, 'pageIndex', val);
        }
        }
    }

</script>

<style scoped>

      .handle-box {
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