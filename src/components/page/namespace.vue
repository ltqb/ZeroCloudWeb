<template>
    <div  class="container">
        <div class="handle-box">
            <el-input  v-model="query.name" placeholder="搜索" class="handle-input mr10" @change="handersearch"></el-input>
            <!--<el-button type="primary" icon="el-icon-search" @click="handersearch">搜索</el-button>-->
            <el-button type="primary" icon="el-icon-lx-roundaddfill" class="handle-del mr10" @click="namespace_create">
                新建命名空间</el-button>
            <el-button type="primary" icon="el-icon-lx-refresh" @click="get_data">刷新</el-button>
        </div>
         <el-table
                :data="itemList"
                border class="table" ref="multipleTable"
                header-cell-class-name="table-header"
            >
             <!--<el-table-column type="selection" width="55" align="center"></el-table-column>-->
                <el-table-column label="名称" align="center">
                    <template slot-scope="scope">{{scope.row.name}}</template>
                </el-table-column>
                <el-table-column label="创建时间" align="center">
                     <template slot-scope="scope">{{scope.row.create_time}}</template>
                </el-table-column>
             <el-table-column label="状态" align="center">
                    <template slot-scope="scope">
                        <el-tag
                            :type="scope.row.status==='Active'?'success':(scope.row.status==='失败'?'danger':'')"
                        >{{scope.row.status}}</el-tag>
                    </template>
                </el-table-column>
               <el-table-column label="操作" align="center">
                     <template slot-scope="scope" v-if="scope.row.name != 'kube-system' && scope.row.name != 'kube-public' && scope.row.name != 'default'">
                        <el-button
                            type="text"
                            icon="el-icon-lx-attentionfill"
                            @click="handleView(scope.$index, scope.row)"
                        >详情</el-button>
                          <el-button
                            type="text"
                            icon="el-icon-delete"
                            class="red"
                            @click="handleDelete(scope.row)"
                        >删除</el-button>
                     </template>
                     <template slot-scope="scope" v-else="scope.row.name === 'kube-system' ">
                        <el-button
                            type="text"
                            icon="el-icon-lx-attentionfill"
                            @click="handleView(scope.$index, scope.row)"
                        >详情</el-button>
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
        <div>
         <el-dialog title="创建命名空间" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="70px">
                <el-form-item label="名称">
                     <el-input v-model="form.name"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEditer">确 定</el-button>
            </span>
        </el-dialog>
        </div>
    </div>
</template>
<script>
import axios from "axios";
export default {
        name: "testData",
        data() {
            return {
                query: {
                name: '',
                pageIndex: 1,
                pageSize: 10
            },
                itemList: [],
                multipleSelection: [],
                delList: [],
                result:0,
                //namespace_name:'',
                editVisible: false,
                pageTotal: 0,
                form: {},
                idx: -1,
                id: -1
            }
        },
        mounted() {
            this.get_data();
        },
        methods: {
            async get_data() {
                axios.get('/api/ns').then(response => {
                    console.log(response.data);
                    this.itemList = response.data.namespaces;
                    this.pageTotal = response.data.total || 10;
                }, response => {
                    console.log("error");
                });
            },
            namespace_create(){
                this.editVisible=true;
            },
             async saveEditer() {
                this.editVisible = false;
                name = this.form.name
                let data = new FormData();
                data.append("name", name)
                axios.post('/api/ns/' + name, data).then(res => {
                    this.result = res.data.result;
                    if (this.result == 0) {
                        this.$message.warning(name + " alrealy exists")
                    }
                    if (this.result == 1) {
                        this.$message.success(name + " has been created")
                    }
                    ;
                    console.log(res.data.result);
                }).then(this.get_data());

            },

            handlePageChange(val) {
            this.$set(this.query, 'pageIndex', val);
            this.getData();
        },
            async handleDelete(row) {
            // 二次确认删除
            this.$confirm('确定要删除吗？', '提示', {
                type: 'warning'
            })
                .then(() => {
                    let data = new FormData();
                    data.append("name",row.name)
                    axios.post("/api/ns/del/"+row.name,data).then(reg=>{
                        var result=reg.data.result;
                        if (result==0)
                        {
                            this.$message.warning(row.name+" 不存在")
                        }
                        if (result==1)
                        {
                            this.$message.success(row.name + " 删除成功")
                        };
                        console.log(result)
                    });
                    //this.tableData.splice(index, 1);
                })
                .catch(() => {});
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