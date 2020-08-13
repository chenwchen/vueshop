<template>
	<el-container class="home-container">
		<!-- 头部区域 -->
		<el-header>
			<div>
				<img src="../assets/logo.png" alt />
				<span>望城后台管理系统</span>
			</div>
			<el-button type="info" @click="logout">退出</el-button>
		</el-header>
		<!-- 页面主题区域 -->
		<el-container>
			<!-- 侧边栏 -->
			<el-aside :width="isCollapse ? '64px' : '200px'">
                <div class="btn-toggle" @click="toggleCollapse">|||</div>
				<el-menu
					background-color="#333744"
					text-color="#fff"
					activ   e-text-color="#409bff"
                    :unique-opened="true"
                    :collapse="isCollapse"
                    :collapse-transition="false"
                    :router="true"
                    :default-active="activePath">
					<el-submenu :index="item.id + ''" v-for="item in menuList" :key="item.id">
						<template slot="title">
							<i :class="iconList[item.id]"></i>
							<span>{{ item.authName }}</span>
						</template>
						<el-menu-item :index="'/' + subitem.path" v-for="subitem in item.children" 
                        :key="subitem.id"
                        @click="saveNavState('/' + subitem.path)">
                            <template slot="title">
                                <i class="el-icon-menu"></i>
                                <span>{{ subitem.authName }}</span>
						    </template>
                        </el-menu-item>
					</el-submenu>
				</el-menu>
			</el-aside>
			<!-- 右侧主体 -->
			<el-main>
                <router-view></router-view>
            </el-main>
		</el-container>
	</el-container>
</template>
<script>
	export default {
        name: "Home",
        data(){
            return {
                menuList: [],
                iconList: {
                    '125': 'iconfont iconyonghuguanli',
                    '101': 'iconfont iconshangpinguanli',
                    '102': 'iconfont icondingdanguanli',
                    '145': 'iconfont iconshujutongji',
                    '103': 'iconfont iconquanxianguanli'
                },
                isCollapse: false,
                activePath: ''
            }
        },
        created() {
            this.getMenuItems();
            this.activePath = window.sessionStorage.getItem('activePath')
        },
		methods: {
			logout() {
				window.sessionStorage.clear();
				this.$router.push("/login");
            },
            // 获取所有的菜单
            async getMenuItems() {
                const {data: res} = await this.$axios.get('/menus')
                if(res.meta.status != 200) return this.$message.error('res.meta.msg')
                this.menuList = res.data
            },
            // 点击按钮实现菜单的折叠和展开
            toggleCollapse() {
                this.isCollapse = !this.isCollapse
            },
            //保存链接的激活状态
            saveNavState(activePath) {
                window.sessionStorage.setItem('activePath', activePath)
                this.activePath = activePath
            }
		}
	};
</script>

<style lang="less" scoped>
	.home-container {
		height: 100%;
	}
	.el-header {
		background: #373d41;
		padding-left: 0;
		display: flex;
		justify-content: space-between;
		align-items: center;
		color: #fff;
		font-size: 20px;
		> div {
			display: flex;
			align-items: center;
			img {
				width: 40px;
			}
			span {
				margin-left: 15px;
			}
		}
	}
	.el-aside {
        background: #333744;
        .el-menu {
            border-right: 0px;
        }
        .btn-toggle {
            background: #4a5064;
            text-align: center;
            color: #fff;
            font-size: 10px;
            line-height: 24px;
            letter-spacing: 0.2em;
            cursor: pointer;
        }
	}
	.el-main {
		background: #eaedf1;
    }
    .iconfont {
        margin-right: 10px;
    }
</style>