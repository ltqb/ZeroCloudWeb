<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-lx-calendar"></i> 命令空间</el-breadcrumb-item>
                <el-breadcrumb-item>查看</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div>
            <el-table :data="nameSpace" style="width: 100%">
                <el-table-column prop="name" label="名称" width="200"></el-table-column>
                <el-table-column prop="time" label="创建时间" width="200"></el-table-column>
                <el-table-column prop="status" label="状态" width="200"></el-table-column>
                <el-table-column prop="status" label="所属集群" width="200"></el-table-column>
            </el-table>
        </div>
    </div>
</template>

<script>

</script>
<style scoped>
    .editor-btn{
        margin-top: 20px;
    }
</style>