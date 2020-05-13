<template>
    <div  class="container">
         <el-table
                :data="podList.slice((query.pageIndex-1)*query.pageSize,query.pageIndex*query.pageSize)"
                border class="table" ref="multipleTable"
                header-cell-class-name="table-header"
            >
             <!--<el-table-column type="selection" width="55" align="center"></el-table-column>-->
             <el-table-column label="命令空间" align="center">
                 <template slot-scope="scope">{{scope.row.namespace}}</template>
             </el-table-column>
             <el-table-column label="名称" align="center">
                    <template slot-scope="scope">{{scope.row.name}}</template>
                </el-table-column>
             <el-table-column label="创建时间" align="center">
                     <template slot-scope="scope">{{scope.row.start_time}}</template>
                </el-table-column>
             <el-table-column label="状态" align="center">
                    <template slot-scope="scope">
                        <el-tag
                            :type="scope.row.status==='Running'?'success':(scope.row.status==='失败'?'danger':'')"
                        >{{scope.row.status}}</el-tag>
                    </template>
                </el-table-column>
             <el-table-column label="所属主机" align="center">
                    <template slot-scope="scope">{{scope.row.node_name}}</template>
                </el-table-column>
             <el-table-column label="IP" align="center">
                    <template slot-scope="scope">{{scope.row.node_ip}}</template>
                </el-table-column>
             <el-table-column label="PodIP" align="center">
                    <template slot-scope="scope">{{scope.row.pod_ip}}</template>
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
        name: "pods",
        data() {
            return {
                query: {
                name: '',
                pageIndex: 1,
                pageSize: 8
            },
                podList: [],
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
            this.get_pods();
        },
        methods:{
        get_pods(){
            axios.get('/api/pods').then(reg=>{
                this.podList=reg.data.pods;
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
handle-box {
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