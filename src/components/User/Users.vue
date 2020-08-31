<template>
	<div>
		<el-breadcrumb separator-class="el-icon-arrow-right">
			<el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
			<el-breadcrumb-item>用户管理</el-breadcrumb-item>
			<el-breadcrumb-item>用户列表</el-breadcrumb-item>
		</el-breadcrumb>
		<el-card class="box-card">
			<el-row :gutter="20">
				<el-col :span="7">
					<el-input placeholder="请输入内容" v-model="queryInfo.query" clearable @clear="getUserList">
						<el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
					</el-input>
				</el-col>
				<el-col :span="4">
					<el-button type="primary" @click="addDialogVisible = true">添加用户</el-button>
				</el-col>
				<el-table :data="userList" style="width: 100%" border stripe>
					<el-table-column type="index" label="#" width="180"></el-table-column>
					<el-table-column prop="username" label="用户名" width="180"></el-table-column>
					<el-table-column prop="role_name" label="角色" width="180"></el-table-column>
					<el-table-column prop="email" label="邮箱"></el-table-column>
					<el-table-column prop="mobile" label="电话号码"></el-table-column>
					<el-table-column align="center" label="状态">
						<template slot-scope="scope">
							<el-switch @change="userStateChanged(scope.row)" v-model="scope.row.mg_state"></el-switch>
						</template>
					</el-table-column>
					<el-table-column label="操作">
						<template slot-scope="scope">
							<el-tooltip :enterable="false" class="item" effect="dark" content="编辑" placement="top">
								<el-button
									@click="showEditDialog(scope.row)"
									type="primary"
									size="mini"
									icon="el-icon-edit"
									circle
								></el-button>
							</el-tooltip>
							<el-tooltip :enterable="false" class="item" effect="dark" content="分配角色" placement="top">
								<el-button type="success" size="mini" icon="el-icon-check" circle></el-button>
							</el-tooltip>
							<el-tooltip :enterable="false" class="item" effect="dark" content="删除" placement="top">
								<el-button
									type="danger"
									size="mini"
									icon="el-icon-delete"
									@click="removeUserById(scope.row.id)"
									circle
								></el-button>
							</el-tooltip>
						</template>
					</el-table-column>
				</el-table>
			</el-row>
			<el-pagination
				@size-change="handleSizeChange"
				@current-change="handleCurrentChange"
				:current-page="queryInfo.pagenum"
				:page-sizes="[1, 2, 5]"
				:page-size="queryInfo.pagesize"
				layout="total, sizes, prev, pager, next, jumper"
				:total="total"
			></el-pagination>
		</el-card>

		<!-- 添加用户的对话框 -->
		<el-dialog title="添加用户" :visible.sync="addDialogVisible" width="30%" @close="addDialogClose">
			<el-form
				ref="addFormRef"
				:model="addForm"
				:rules="addFormRule"
				label-width="100px"
				class="demo-ruleForm"
			>
				<el-form-item label="用户名" prop="username">
					<el-input v-model="addForm.username"></el-input>
				</el-form-item>
				<el-form-item label="密码" prop="password">
					<el-input v-model="addForm.password"></el-input>
				</el-form-item>
				<el-form-item label="邮箱" prop="email">
					<el-input v-model="addForm.email"></el-input>
				</el-form-item>
				<el-form-item label="手机" prop="mobile">
					<el-input v-model="addForm.mobile"></el-input>
				</el-form-item>
			</el-form>
			<span slot="footer">
				<el-button @click="addDialogVisible = false">取 消</el-button>
				<el-button type="primary" @click="addUser">确 定</el-button>
			</span>
		</el-dialog>
		<!-- 修改用户对话框 -->
		<el-dialog title="修改用户" :visible.sync="editDialogVisible" width="30%" @close="editDialogClose">
			<el-form ref="editFormRef" :model="editForm" :rules="addFormRule" label-width="100px">
				<el-form-item label="用户名" prop="username">
					<el-input :disabled="true" v-model="editForm.username"></el-input>
				</el-form-item>
				<el-form-item label="邮箱" prop="email">
					<el-input v-model="editForm.email"></el-input>
				</el-form-item>
				<el-form-item label="手机" prop="mobile2">
					<el-input v-model="editForm.mobile"></el-input>
				</el-form-item>
			</el-form>
			<span slot="footer">
				<el-button @click="editDialogVisible = false">取 消</el-button>
				<el-button type="primary" @click="editUser">确 定</el-button>
			</span>
		</el-dialog>
	</div>
</template>

<script>
	export default {
		name: "Users",
		created() {
			this.getUserList();
		},
		data() {
			var checkEmail = (rule, value, cb) => {
				const reg = /^[A-Za-z\d]+([-_.][A-Za-z\d]+)*@([A-Za-z\d]+[-.])+[A-Za-z\d]{2,4}$/;
				if (reg.test(value)) return cb();
				cb(new Error("请输入合法邮箱"));
			};
			var checkMobile = (rule, value, cb) => {
				const reg = /^[1][3,4,5,7,8][0-9]{9}$/;
				if (reg.test(value)) return cb();
				cb(new Error("请输入合法手机号"));
			};
			return {
				queryInfo: {
					query: "",
					pagenum: 1,
					pagesize: 2,
				},
				addForm: {},
				editForm: {
					id: "",
					username: "",
					email: "",
					mobile: "",
				},
				addFormRule: {
					username: [
						{
							required: true,
							message: "请输入用户名",
							trigger: "blur",
						},
						{
							min: 3,
							max: 10,
							message: "用户名长度3 到10 之间",
							trigger: "blur",
						},
					],
					password: [
						{ required: true, message: "请输入密码", trigger: "blur" },
						{
							min: 6,
							max: 15,
							message: "密码长度6 到15 之间",
							trigger: "blur",
						},
					],
					email: [
						{ required: true, message: "请输入邮箱", trigger: "blur" },
						{ validator: checkEmail, trigger: "blur" },
					],
					mobile: [
						{ require: true, message: "请输入手机号", trigger: "blur" },
						{ validator: checkMobile, trigger: "blur" },
					],
					mobile2: [
						{ require: true, message: "请输入手机号", trigger: "blur" },
						{ validator: checkMobile, trigger: "blur" },
					],
				},
				userList: [],
				total: 0,
				addDialogVisible: false,
				editDialogVisible: false,
			};
		},
		methods: {
			async getUserList() {
				const { data: res } = await this.$axios.get("/users", {
					params: this.queryInfo,
				});
				if (res.meta.status !== 200)
					return this.$message.error(res.meta.msg);
				this.userList = res.data.users;
				this.total = res.data.total;
			},
			// 监听最新的pagesize
			handleSizeChange(newSize) {
				this.queryInfo.pagesize = newSize;
				this.getUserList();
			},
			// 监听最新的页码值改变
			handleCurrentChange(newPage) {
				this.queryInfo.pagenum = newPage;
				this.getUserList();
			},
			// 监听switch开关的状态改变
			async userStateChanged(userInfo) {
				const { data: res } = await this.$axios.put(
					"users/" + userInfo.id + "/state/" + userInfo.mg_state
				);
				if (res.meta.status !== 200)
					return this.$message.error(res.meta.msg);
				this.$message.success(res.meta.msg);
			},
			addDialogClose() {
				this.$refs.addFormRef.resetFields();
			},
			editDialogClose() {
				this.$refs.editFormRef.resetFields();
			},
			addUser() {
				this.$refs.addFormRef.validate(async (vaild) => {
					if (!vaild) return;
					const { data: res } = await this.$axios.post(
						"/users",
						this.addForm
					);
					console.log(res);
					if (res.meta.status !== 201)
						return this.$message.error(res.meta.msg);

					// 隐藏添加数据对话框
					this.addDialogVisible = false;
					// 重新获取数据
					this.getUserList();
					this.$message.success(res.meta.msg);
				});
			},
			// 显示修改对话框
			showEditDialog(userInfo) {
				this.editForm = userInfo;
				this.editDialogVisible = true;
			},
			// 编辑用户提交, 有问题
			editUser() {
				this.$refs.editFormRef.validate(async (vaild) => {
					if (!vaild) return;
					const { data: res } = await this.$axios.put(
						"/users/" + this.editForm.id,
						this.editForm
					);
					if (res.meta.status !== 200)
						return this.$message.error(res.meta.msg);
					this.editDialogVisible = false;
					this.$message.success(res.meta.msg);
				});
            },
            // 通过id删除用户
			removeUserById(id) {
				this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
					confirmButtonText: "确定",
					cancelButtonText: "取消",
					type: "warning",
					center: true,
				}).then(() => {
					this.$message({
						type: "success",
						message: "删除成功!",
					});
				}).catch(() => {
					this.$message({
						type: "info",
						message: "已取消删除",
					})
				})
			},
		},
	};
</script>

<style lang="less" scoped>
	.el-table {
		margin-top: 70px;
	}
	.el-pagination {
		margin-top: 12px;
	}
</style>