<template>
    <div class="login-container">
        <div class="login-box">
            <div class="avatar-box">
                <img src="../assets/logo.png" alt="">
            </div>
            <!-- 登录区域 -->
            <el-form class="login-form" :model="loginForm" :rules="loginRules" ref="loginFormRef">
                <!--  账号 -->
                <el-form-item   prop="username">
                    <el-input prefix-icon="iconfont iconuser_login" v-model="loginForm.username"></el-input>
                </el-form-item>
                <!-- 密码 -->
                <el-form-item prop="password">
                    <el-input prefix-icon="iconfont iconpassword" v-model="loginForm.password" type="password"  ></el-input>
                </el-form-item>
                <!-- 按钮 -->
                <el-form-item class="btns">
                    <el-button type="primary" @click="login">登录</el-button>
                    <el-button type="info" @click="resetLoginForm">重置</el-button>
                </el-form-item>
            </el-form>
        </div>
    </div>
</template>
<script>
    export default {
        name: 'Login',
        data: () => {
            return {
                loginForm: {
                    username: 'admin',
                    password: '123456'
                },
                loginRules: {
                    // 验证用户名是否合法
                    username: [
                        { required: true, message: '请输入账号!', trigger: 'blur' },
                        { min: 3, max: 10, message: '长度在 3 到 10个字符', trigger: 'blur' }
                    ],
                    // 验证密码是否合法
                    password: [
                        { required: true, message: '请输入密码!', trigger: 'blur' },
                        { min: 6, max: 15, message: '长度在6到15个字符', trigger: 'blur' }
                    ]
                }
            }
        },
        methods: {
            // 点击重置表单
            resetLoginForm () {
                this.$refs.loginFormRef.resetFields()
            },
            login() {
                this.$refs.loginFormRef.validate(async valid => {
                    if(!valid) return
                    const result = await this.$axios.post('/login', this.loginForm)
                    if(result.data.meta.status != 200)
                        this.$message.error('登录失败')
                    this.$message.success('登录成功')
                    window.sessionStorage.setItem('token', result.data.data.token)
                    this.$router.push('/home')
                })
            }
        }
    }
</script>
<style lang="less" scoped>
    .login-container {
        background: #2b4b6b;
        height: 100%;
    }
    .login-box {
        width: 450px;
        height: 300px;
        background: #fff;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        border-radius: 3px;
    }
    .avatar-box {
        height: 130px;
        width: 130px;
        background: #fff;
        border-radius: 50%;
        border: #eee solid 1px;
        padding: 10px;
        box-shadow: 0px 0px 10px #ddd;
        position: absolute;
        left: 50%;
        transform: translate(-50%, -50%);
        img {
            width: 100%;
            height: 100%;
            background: #eee;
            border-radius: 50%;
        }
    }
    .login-form {
        width: 100%;
        position: absolute;
        bottom: 0;
        padding: 0 10px;
        box-sizing: border-box;
    }
    .btns {
        display: flex;
        justify-content: flex-end;
    }
</style>
