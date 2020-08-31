<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
			<el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
			<el-breadcrumb-item>权限管理</el-breadcrumb-item>
			<el-breadcrumb-item>权限列表</el-breadcrumb-item>
		</el-breadcrumb>
        <el-card class="box-card">
            <el-row>
                <el-col>
                    <el-button type="primary">添加角色</el-button>
                </el-col>
            </el-row>
            <!-- 角色列表区域 -->
            <el-table :data="rolesList" border  stripe>
                <el-table-column type="expand">
                    <template slot-scope="scope">
                        <el-row :gutter="24" v-for="(item1, i1) in scope.row.children" 
                        :key="item1.id" :class="['bdbottom', i1 === 0 ? 'bdtop' : '', 'vcenter']">
                            <!-- 一级权限 -->
                            <el-col :span="5">
                                <el-tag closable> {{ item1.authName }}</el-tag>
                                <i class="el-icon-caret-right"></i>
                            </el-col>
                            <!-- 二级和三级权限 -->
                            <el-col :span="19">
                                <el-row :gutter="24" v-for="(item2, i2) in item1.children"
                                :key="item2.id" :class="[i2 !== 0 ? 'bdtop' : '', 'vcenter']">
                                    <el-col :span="6">
                                        <el-tag type="success" closable> {{ item2.authName }}</el-tag>
                                        <i class="el-icon-caret-right"></i>
                                    </el-col>
                                    <el-col :span="18">
                                        <el-tag type="warning" closable v-for="(item3, i3) in item2.children" :key="item3.id"> 
                                            {{ item3.authName }}
                                        </el-tag>
                                    </el-col>
                                </el-row>
                            </el-col>
                        </el-row>

                    </template>
                </el-table-column>
                <el-table-column type="index">#</el-table-column>
                <el-table-column prop="roleName" label="角色名称"></el-table-column>
                <el-table-column prop="roleDesc" label="角色描述"></el-table-column>
                <el-table-column label="操作" width="300px">
                    <template slot-scope="scope">
                        <el-button size="mini" type="primary" icon="el-icon-edit">编辑</el-button>
                        <el-button size="mini" type="danger" icon="el-icon-delete">删除</el-button>
                        <el-button size="mini" type="warning" icon="el-icon-setting">分配权限</el-button>
                    </template>
                </el-table-column>
            </el-table>
        </el-card>
    </div>
</template>

<script>
    export default {
        name: 'Roles',
        data() {
            return {
                // 所有角色列表数据
                rolesList: []
            }
        },
        created() {
            this.getRolesList()
        },
        methods: {
            async getRolesList() {
                const {data: res} = await this.$axios.get('/roles')
                if(res.meta.status != 200) return this.$message.error(res.meta.msg)
                // this.$message.success(res.meta.msg)
                this.rolesList = res.data
                console.log(this.rolesList)
            }
        }
    }
</script>

<style lang="less" scoped>
    .el-tag {
        margin: 7px;
    }
    .bdtop {
        border-top: 1px solid #eee;
    }
    .bdbottom {
        border-bottom: 1px solid #eee;
    }
    .vcenter {
        display: flex;
        align-items: center;
    }
</style>