<template>
    <div class="container">
        <div class="handle-box">
            <el-input class="handle-input mr10"  placeholder="搜索"></el-input>
             <!--<el-button type="primary" icon="el-icon-search" @click="handersearch">搜索</el-button>-->
             <el-button type="success" icon="el-icon-video-play" @click="handersearch">Start</el-button>
             <el-button type="info" icon="el-icon-video-pause" @click="handersearch">Stop</el-button>
             <el-button type="warning" icon="el-icon-refresh" @click="handersearch">Restart</el-button>
             <el-button type="danger" icon="el-icon-delete" @click="handersearch">Remove</el-button>
             <el-button type="primary" icon="el-icon-plus" @click="handersearch">ADD container</el-button>
        </div>
        <el-table :data="containerList.slice((query.pageIndex-1)*query.pageSize,query.pageIndex*query.pageSize)"
                  border class="table" ref="multipleTable"
                  header-cell-class-name="table-header">
            <el-table-column type="selection" width="55" align="center"></el-table-column>
            <!--<el-table-column prop="id" label="ID" width="55" align="center"></el-table-column>-->
            <el-table-column prop="name" label="容器名称" align="center">
                <template slot-scope="scope">{{scope.row.Names[0]}}</template>
            </el-table-column>
            <el-table-column label="状态" align="center">
                <template slot-scope="scope">
                    <el-tag
                            :type="scope.row.State==='running'?'success':(scope.row.status!='running'?'danger':'')"
                        > {{scope.row.State}}</el-tag>
                   </template>
            </el-table-column>
            <el-table-column label="快捷菜单" align="center">
                <template slot-scope="scope">
                    <el-button type="text" icon="el-icon-lx-infofill" @click="handleView(scope.$index, scope.row)">
                    </el-button>
                    <el-button type="text" icon="el-icon-lx-rank" @click="handleView(scope.$index, scope.row)">
                    </el-button>
                    <el-button type="text" icon="el-icon-lx-text" @click="handleView(scope.$index, scope.row)">
                    </el-button>
                    <el-button type="text" icon="el-icon-lx-right" @click="handleView(scope.$index, scope.row)">
                    </el-button>
                </template>
            </el-table-column>
            <el-table-column label="镜像名称" align="center">
                <template slot-scope="scope">{{scope.row.Image}}</template>
            </el-table-column>
            <el-table-column label="创建时间" align="center">
                <template slot-scope="scope">{{scope.row.Created}}</template>
            </el-table-column>
            <el-table-column label="网络信息" align="center">
                <!--<template slot-scope="scope" v-if="scope.row.NetworkSettings.Networks!='{}'">{{scope.row.NetworkSettings.Networks}}</template>-->
            </el-table-column>
            <el-table-column label="映射端口" align="center">
                <template style="font-family: 'Arial Rounded MT Bold';" slot-scope="scope" v-if="scope.row.Ports.length>0">{{scope.row.Ports[0].IP+":"+scope.row.Ports[0].PublicPort+" -> "+scope.row.Ports[0].PrivatePort}}</template>
            </el-table-column>
            <el-table-column label="所属者" align="center">
                <template slot-scope="scope">{{scope.row.money}}</template>
            </el-table-column>

        </el-table>

        <el-dialog title="下载信息" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="70px">
                <el-form-item label="镜像名称">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="版本">
                    <el-input v-model="form.version"></el-input>
                </el-form-item>
                <el-form-item label="主机名">
                    <el-input v-model="form.node_name"></el-input>
                </el-form-item>
                <el-form-item label="保存名称">
                    <el-input v-model="downloadName"></el-input>
                </el-form-item>
                <el-form-item :visible.sync="this.process_visible" label="下载进度">
                    <el-progress :text-inside="true" :stroke-width="26" :percentage=this.process_value></el-progress>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button type="primary" @click="confim_download">下载</el-button>
                <el-button type="primary" @click="process_bar_get">查看进度</el-button>
                <el-button @click="editVisible = false">取消</el-button>
                <el-button type="primary" @click="saveEdit">后台下载</el-button>
            </span>
        </el-dialog>
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
        name: "Containers",
        data() {
            return {
                query: {
                    name: '',
                    node_name: '',
                    pageIndex: 1,
                    pageSize: 6,
                },
                containerList: [],
                showList: [],
                multipleSelection: [],
                nodeList: [],
                delList: [],
                result: 0,
                editVisible: false,
                pageTotal: 0,
                form: {},
                downpath: "D:\\images\\",
                downloadName: '',
                process_value: 0,
                process_visible: false,
                downbtnVisible: false,
                idx: -1,
                id: -1
            }
        },
        created() {

            this.get_containers()
        }
        ,
        activated() {
            this.get_containers()
        },
        methods: {
            get_containers() {
                axios.get('/api/containers').then(reg => {
                    this.containerList = reg.data.containers;
                    this.pageTotal = reg.data.total;
                    console.log(reg.data)
                });
            },
            table_refesh() {

            },
            // async hander_search() {
            //     var name = this.images_query.name
            //     var nodename = this.images_query.node_name
            //     let data = new FormData();
            //     data.append("name", name);
            //     data.append("nodename", nodename);
            //     await axios.post('/api/images/filter', data).then(req => {
            //         this.imagesList = req.data.images;
            //         this.nodeList = req.data.node_list;
            //         this.pageTotal = req.data.total;
            //     });
            // },
            // get_data(list, index, count) {
            //     list.slice(index, count)
            // },
            handlePageChange(val) {
                this.$set(this.query, 'pageIndex', val);
            },
            // handleDownload(row) {
            //     this.process_value=0;
            //     this.form = row;
            //     this.downloadName = this.form.name.replace(/\//g, "_") + ".tar"
            //     this.editVisible = true;
            // },
            // async confim_download() {
            //     this.downbtnVisible=false;
            //     var nodeName = this.form.node_name
            //     var name = this.form.name + ":" + this.form.version
            //     var tar_name = this.downloadName
            //     var save_name = "/tmp/" + tar_name
            //     let data = new FormData();
            //     data.append("name", name);
            //     data.append("node_name", nodeName);
            //     data.append("save_path", save_name);
            //     data.append("tar_name", tar_name);
            //     await axios.post("/api/images/download/start", data).then(req => {
            //         console.log(req.data)
            //     });
            // },
            // process_bar_get(){
            //     var tar_name = this.downloadName
            //     let data = new FormData();
            //     data.append("tar_name", tar_name);
            //     axios.post("/api/images/download/status", data).then(req => {
            //         this.process_value=req.data.percent;
            //         console.log(req.data)
            //     });
            //
            // }

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