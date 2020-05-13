<template>
    <div class="sidebar">
        <el-menu
                class="sidebar-el-menu"
                :default-active="onRoutes"
                :collapse="collapse"
                background-color="#324157"
                text-color="#bfcbd9"
                active-text-color="#20a0ff"
                unique-opened
                router
        >
            <template v-for="item in items">
                <template v-if="item.subs">
                    <el-submenu :index="item.index" :key="item.index">
                        <template slot="title">
                            <i :class="item.icon"></i>
                            <span slot="title">{{ item.title }}</span>
                        </template>
                        <template v-for="subItem in item.subs">
                            <el-submenu
                                    v-if="subItem.subs"
                                    :index="subItem.index"
                                    :key="subItem.index"
                            >
                                <template slot="title">{{ subItem.title }}</template>
                                <el-menu-item
                                        v-for="(threeItem,i) in subItem.subs"
                                        :key="i"
                                        :index="threeItem.index"
                                >{{ threeItem.title }}
                                </el-menu-item>
                            </el-submenu>
                            <el-menu-item
                                    v-else
                                    :index="subItem.index"
                                    :key="subItem.index"
                            >{{ subItem.title }}
                            </el-menu-item>
                        </template>
                    </el-submenu>
                </template>
                <template v-else>
                    <el-menu-item :index="item.index" :key="item.index">
                        <i :class="item.icon"></i>
                        <span slot="title">{{ item.title }}</span>
                    </el-menu-item>
                </template>
            </template>
        </el-menu>
    </div>
</template>

<script>
    import bus from '../common/bus';

    export default {
        data() {
            return {
                collapse: false,
                items: [
                    {
                        icon: 'el-icon-lx-home',
                        index: 'dashboard',
                        title: '系统首页'
                    },
                    {
                        icon: 'el-icon-lx-global',
                        index: 'infra',
                        title: '基础设施'
                    },
                    {
                        icon: 'el-icon-lx-settings',
                        index: 'tabs',
                        title: '运维',
                        subs: [
                            {
                                index: 'containerManager',
                                title: '容器管理',
                            },
                            {
                                index: 'imageManager',
                                title: '镜像管理',
                            },
                            {
                                index: 'dockerHelper',
                                title: '监控配置',
                            },
                            {
                                index: 'dockerHelper',
                                title: '告警配置',
                            },
                            {
                                index: 'dockerHelper',
                                title: '定时任务',
                            },
                        ]
                    },
                    {
                        icon: 'el-icon-lx-profile',
                        index: '3',
                        title: '交付',
                        subs: [
                            {
                                index: 'namespaces',
                                title: '命令空间',
                            },
                            {
                                index: 'pods',
                                title: 'POD管理',
                            },
                            {
                                index: 'apps',
                                title: '应用管理',
                            },
                            {
                                index: 'upload',
                                title: '配置管理',
                            },
                            {
                                index: 'upload',
                                title: '模板管理',
                                // subs: [
                                //     {
                                //         index: 'editor',
                                //         title: '查看'
                                //     },
                                //     {
                                //         index: 'markdown',
                                //         title: '变更'
                                //     }
                                // ]
                            }
                        ]
                    },
                    {
                        icon: 'el-icon-lx-notice',
                        index: '4',
                        title: '监控',
                        subs: [
                            {
                                index: 'monitor',
                                title: '集群概览',
                            },
                        ]
                    }
                ]
            };
        },
        computed: {
            onRoutes() {
                return this.$route.path.replace('/', '');
            }
        },
        created() {
            // 通过 Event Bus 进行组件间通信，来折叠侧边栏
            bus.$on('collapse', msg => {
                this.collapse = msg;
                bus.$emit('collapse-content', msg);
            });
        }
    };
</script>

<style scoped>
    .sidebar {
        display: block;
        position: absolute;
        left: 0;
        top: 60px;
        bottom: 0;
        overflow-y: scroll;
    }

    .sidebar::-webkit-scrollbar {
        width: 0;
    }

    .sidebar-el-menu:not(.el-menu--collapse) {
        width: 200px;
    }

    .sidebar > ul {
        height: 100%;
    }
</style>
