<template>
	<div>
		<el-breadcrumb separator-class="el-icon-arrow-right">
			<el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
			<el-breadcrumb-item>权限管理</el-breadcrumb-item>
			<el-breadcrumb-item>权限列表</el-breadcrumb-item>
		</el-breadcrumb>
		<el-card class="box-card">
            <!-- authName: "商品管理"
            id: 101
            level: "0"
            path: "goods"
            pid: 0 -->
			<el-table border stripe :data="rightsList" style="width: 100%">
				<el-table-column type="index" width="180"></el-table-column>
                <el-table-column prop="authName" label="权限名称" width="180"></el-table-column>
				<el-table-column prop="path" label="路径" width="180"></el-table-column>
				<el-table-column prop="level" label="权限等级">
                    <template slot-scope="scope">
                        <el-tag v-if="scope.row.level==0">一级</el-tag>
                        <el-tag v-else-if="scope.row.level==1" type="success">二级</el-tag>
                        <el-tag v-else type="warning">三级</el-tag>
                    </template>
                </el-table-column>
    
    
            </el-table>
		</el-card>
	</div>
</template>

<script>
	export default {
        name: 'Rights',
		data() {
			return {
				rightsList: [],
			};
		},
		created() {
			this.getRightsList();
		},
		methods: {
			// 获取权限列表
			async getRightsList() {
				const { data: res } = await this.$axios.get("rights/list");
				if (res.meta.status != 200) return this.$message(res.meta.msg);
				this.rightsList = res.data;
			},
		},
	};
</script>

<style lang="less" scoped>
</style>