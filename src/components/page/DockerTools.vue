<template>
    <div class="container">
        <div class="handle-box">
            <el-input v-model="images_query.name" placeholder="镜像名称" class="handle-input mr10"></el-input>
            <el-select v-model="images_query.node_name" placeholder="主机名" class="handle-select mr10">
                <el-option v-for="node in this.nodeList" :key=node :label=node :value=node>
                </el-option>
            </el-select>
            <el-button type="primary" icon="el-icon-search" @click="fetchImages">搜索</el-button>
            <el-button type="primary" icon="el-icon-lx-refresh" @click="get_images">显示全部</el-button>
            <el-button type="primary" icon="el-icon-lx-upload" @click="get_images">批量上传</el-button>
            <el-button type="primary" icon="el-icon-lx-down" @click="get_images">批量下载</el-button>
        </div>
        <div style="height: 10px;"></div>
        <el-table

                :data=" imagesList.slice((images_query.pageIndex-1)*images_query.pageSize,images_query.pageIndex*images_query.pageSize)"
                border class="table" ref="multipleTable"
                header-cell-class-name="table-header"
        >
            <el-table-column type="selection" width="55" align="center"></el-table-column>

            <el-table-column label="镜像名" align="center">
                <template slot-scope="scope">{{scope.row.name}}</template>
            </el-table-column>
            <el-table-column label="版本" align="center">
                <template slot-scope="scope">{{scope.row.version}}</template>
            </el-table-column>
            <el-table-column label="镜像大小" align="center">
                <template slot-scope="scope">{{scope.row.size}}</template>
            </el-table-column>
            <el-table-column label="主机名" align="center">
                <template slot-scope="scope">{{scope.row.node_name}}</template>
            </el-table-column>
            <el-table-column label="操作" align="center">
                <template slot-scope="scope">
                    <el-button
                            type="text"
                            icon="el-icon-lx-attentionfill"
                            @click="handleView(scope.$index, scope.row)"
                    >
                    </el-button>
                    <el-button
                            type="text"
                            icon="el-icon-lx-upload"
                            @click="handleView(scope.$index, scope.row)"
                    >
                    </el-button>
                    <el-button
                            type="text"
                            icon="el-icon-lx-down"
                            @click="handleDownload(scope.row)"
                    >
                    </el-button>
                    <el-button
                            type="text"
                            icon="el-icon-delete"
                            class="red"
                            @click="handleDelete(scope.row)"
                    >
                    </el-button>
                </template>
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
                <el-button type="primary" @click="confim_download" >下载</el-button>
                <el-button type="primary" @click="process_bar_get">查看进度</el-button>
                <el-button @click="editVisible = false">取消</el-button>
                <el-button type="primary" @click="saveEdit">后台下载</el-button>
            </span>
        </el-dialog>
        <div class="pagination">
            <el-pagination
                    background
                    layout="total, prev, pager, next"
                    :current-page="images_query.pageIndex"
                    :page-size="images_query.pageSize"
                    :total="pageTotal"
                    @current-change="handlePageChange"
            ></el-pagination>
        </div>
    </div>
</template>

<script>
    import axios from "axios";

    export default {
        name: "ImagesManager",
        data() {
            return {
                images_query: {
                    name: '',
                    node_name: '',
                    pageIndex: 1,
                    pageSize: 6,
                },
                imagesList: [],
                showList: [],
                multipleSelection: [],
                nodeList: [],
                delList: [],
                result: 0,
                editVisible: false,
                pageTotal: 0,
                form: {},
                downpath: "D:\\images\\",
                downloadName:'',
                process_value: 0,
                process_visible: false,
                downbtnVisible:false,
                idx: -1,
                id: -1
            }
        },
        created() {

            this.fetchImages(this.images_query)
        }
        ,
        activated() {
            this.fetchImages(this.images_query)
        },
        methods: {

            fetchImages(query) {
                if (query.node_name != '') {
                    this.hander_search()
                } else {
                    this.get_images()
                }

            },
            get_images() {
                axios.get('/api/images').then(reg => {
                    this.imagesList = reg.data.images;
                    this.nodeList = reg.data.node_list;
                    this.pageTotal = reg.data.total;
                    console.log(reg.data)
                });
            },
            table_refesh() {

            },
            async hander_search() {
                var name = this.images_query.name
                var nodename = this.images_query.node_name
                let data = new FormData();
                data.append("name", name);
                data.append("nodename", nodename);
                await axios.post('/api/images/filter', data).then(req => {
                    this.imagesList = req.data.images;
                    this.nodeList = req.data.node_list;
                    this.pageTotal = req.data.total;
                });
            },
            get_data(list, index, count) {
                list.slice(index, count)
            },

            handlePageChange(val) {
                this.$set(this.images_query, 'pageIndex', val);
            },
            handleDownload(row) {
                this.process_value=0;
                this.form = row;
                this.downloadName = this.form.name.replace(/\//g, "_") + ".tar"
                this.editVisible = true;
            },
            async confim_download() {
                this.downbtnVisible=false;
                var nodeName = this.form.node_name
                var name = this.form.name + ":" + this.form.version
                var tar_name = this.downloadName
                var save_name = "/tmp/" + tar_name
                let data = new FormData();
                data.append("name", name);
                data.append("node_name", nodeName);
                data.append("save_path", save_name);
                data.append("tar_name", tar_name);
                await axios.post("/api/images/download/start", data).then(req => {
                    console.log(req.data)
                });
            },
            process_bar_get(){
                var tar_name = this.downloadName
                let data = new FormData();
                data.append("tar_name", tar_name);
                axios.post("/api/images/download/status", data).then(req => {
                    this.process_value=req.data.percent;
                    console.log(req.data)
                });

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