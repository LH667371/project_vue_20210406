<template>
    <div id="wrap">
        <div id="top_content">
            <div id="header">
                <div id="rightheader">
                    <p>
                        {{ date }} {{ time }}
                        <br/>
                    </p>
                </div>
                <div id="topheader">
                    <h1 id="title">
                        <a href="#">Main</a>
                    </h1>
                </div>
                <div id="navigation">
                </div>
            </div>
            <div id="content">
                <p id="whereami">
                </p>
                <h1>
                    登录
                </h1>
                <table cellpadding="0" cellspacing="0" border="0"
                       class="form_table" align="center">
                    <tr>
                        <td valign="middle">
                            账号:
                        </td>
                        <td valign="middle" align="left">
                            <el-input style="width: 200px" v-model="username" size="mini" @blur="msg1=username === ''"
                                      placeholder="请输入账号"></el-input>
                            <!--                            <input type="text" class="inputgri" v-model="username" @blur="msg1=username === ''"/>-->
                            <span style="color: red" v-show="msg1">*必填</span>
                        </td>
                    </tr>
                    <tr>
                        <td valign="middle" align="right">
                            密码:
                        </td>
                        <td valign="middle" align="left">
                            <el-input style="width: 200px" size="mini" placeholder="请输入密码" v-model="password" @blur="msg2=password === ''"
                                      show-password></el-input>
                            <!--                            <input type="password" class="inputgri" v-model="password" @blur="msg2=password === ''"/>-->
                            <span style="color: red" v-show="msg2">*必填</span>
                        </td>
                    </tr>
                    <!--                    <tr>-->
                    <!--                        <td valign="middle" align="right">-->
                    <!--                            验证码:-->
                    <!--                        </td>-->
                    <!--                        <td valign="middle" align="left">-->
                    <!--                            <input type="text" class="inputgri" v-model="code"/>-->
                    <!--                            <span class="span1" style="color:red;" v-show="msg3">*必填</span>-->
                    <!--                            <img id="num" class="img" :src="src" title="看不清?点击换一张！"-->
                    <!--                                 @click=""/>-->
                    <!--                        </td>-->
                    <!--                    </tr>-->
                </table>
                <p align="center">
                    <el-button @click="login" size="small" round>提 交</el-button>
                    <!--                    <input type="submit" class="button" value="提 交" @click="login"/>-->
                    &nbsp;&nbsp
                    <router-link to="register/">注册</router-link>
                </p>
            </div>
        </div>
        <div id="footer">
            <div id="footer_bg">
                ABC@126.com
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "login",
    data() {
        return {
            date: new Date().toLocaleDateString(),
            time: new Date().toLocaleTimeString('chinese', {hour12: false}),
            username: "",
            msg1: false,
            password: "",
            msg2: false,
            src: "",
            captcha: '',
            code: '暂时不使用，可不填',
            msg3: false,
        }
    },
    created() {
        if (sessionStorage.token) {
            this.$router.push('/index');
            this.$message({
                showClose: true,
                message: '退出登录，才能进入登录页面!',
                type: 'error'
            });
        }
    },
    methods: {
        login() {
            this.msg1 = this.username === '';
            this.msg2 = this.password === '';
            this.msg3 = this.code === '';
            if (!this.msg1 && !this.msg2)
                this.$axios({
                    url: 'http://127.0.0.1:8000/user/',
                    method: 'post',
                    data: {
                        account: this.username,
                        pwd: this.password
                    }
                }).then(res => {
                    // console.log(res);
                    if (res.data['status'] === 401)
                        this.$message({
                            showClose: true,
                            message: '用户名输入错误!',
                            type: 'error'
                        });
                    else if (res.data['status'] === 402)
                        this.$message({
                            showClose: true,
                            message: '密码输入错误!',
                            type: 'error'
                        });
                    else {
                        // console.log(res);
                        sessionStorage.name = res.data['name'];
                        sessionStorage.token = res.data['token'];
                        this.$message({
                            showClose: true,
                            message: '登录成功!',
                            type: 'success'
                        });
                        this.$router.push('/index');
                    }
                }).catch(error => {
                    console.log(error);
                })
        },
    },
    mounted() {
        this.timer = setInterval(() => {
            this.date = new Date().toLocaleDateString(); // 修改数据date
            this.time = new Date().toLocaleTimeString('chinese', {hour12: false});
        }, 1000)
    },
    beforeDestroy() {
        if (this.timer) {
            clearInterval(this.timer); // 在Vue实例销毁前，清除我们的定时器
        }
    },
}
</script>

<style scoped>

</style>
