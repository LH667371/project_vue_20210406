<template>
    <div id="wrap">
        <div id="top_content">
            <div id="header">
                <div id="rightheader">
                    <p>
                        {{ date }} {{ time }}
                        <br/>
                        <!--                        <a href="javascript:void(0)" @click="logout">安全退出</a>-->
                        <span>
                            <el-link icon="el-icon-switch-button" :underline="false" @click="logout">安全退出</el-link>
                        </span>
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
                <h1 style="float: left">
                    {{ name }}，欢迎您的访问！
                </h1>
                <el-button style="float: right" size="mini" @click="search" plain>搜索</el-button>
                <el-input
                    style="float: right;width: 150px"
                    size="mini"
                    placeholder="请输入搜索内容"
                    prefix-icon="el-icon-search"
                    v-model="info">
                </el-input>
                <table class="table">
                    <tr class="table_header">
                        <td>ID</td>
                        <td>Name</td>
                        <td>Photo</td>
                        <td>Salary</td>
                        <td>Age</td>
                        <td>Gender</td>
                        <td>Birthday</td>
                        <td>Department</td>
                        <td>Operation</td>
                    </tr>
                    <tr v-for="(value, index) in emp_list" :key="index" :class="{row1:index%2===0, row2:index%2!==0}">
                        <td>{{ value['id'] }}</td>
                        <td>{{ value['name'] }}</td>
                        <td><img :src="value['head_pic']" style="height: 60px;"></td>
                        <td>{{ value['salary'] }}元</td>
                        <td>{{ value['age'] }}</td>
                        <td>{{ value['sex'] }}</td>
                        <td>{{ value['birthday'] }}</td>
                        <td>{{ value['depart_name'] }}</td>
                        <td>
                            <el-tooltip class="item" effect="light" content="删除" placement="left-start">
                                <el-button type="danger" icon="el-icon-delete" size="mini"
                                           @click="del_emp(value['id'], value['name'], index)" round></el-button>
                            </el-tooltip>
                            &nbsp;
                            <el-tooltip class="item" effect="light" content="修改" placement="right-start">
                                <el-button type="primary" icon="el-icon-edit" size="mini"
                                           @click="update_emp(value['id'])" round></el-button>
                            </el-tooltip>
                        </td>
                    </tr>
                </table>
                <p>
                    <el-pagination
                        align="center"
                        background
                        layout="prev, pager, next"
                        :page-size="5"
                        :pager-count="5"
                        :total="total"
                        :current-page.sync="page_now"
                        @next-click="page"
                        @prev-click="page"
                        @current-change="page">
                    </el-pagination>
                    <el-button type="primary" size="small" round @click="dialogFormVisible = true">添加员工</el-button>
                </p>
            </div>
        </div>
        <div id="footer">
            <div id="footer_bg">
                ABC@126.com
            </div>
        </div>


        <el-dialog title="添加员工" :visible.sync="dialogFormVisible" width="700px" @close="close_dialog(0)">
            <el-form :model="form">
                <el-form-item label="头像" :label-width="formLabelWidth">
                    <el-upload
                        class="avatar-uploader"
                        action=""
                        :auto-upload="false"
                        :show-file-list="false"
                        :on-change="changeUpload">
                        <img v-if="imageUrl" :src="imageUrl" class="avatar" alt="">
                        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                    </el-upload>
                </el-form-item>
                <el-form-item label="名字" :label-width="formLabelWidth">
                    <el-col :span="21">
                        <el-input v-model="form.name" autocomplete="off"></el-input>
                    </el-col>
                </el-form-item>
                <el-form-item label="工资" :label-width="formLabelWidth">
                    <el-col :span="21">
                        <el-input v-model="form.salary" autocomplete="off"></el-input>
                    </el-col>
                </el-form-item>
                <el-form-item label="年龄" :label-width="formLabelWidth">
                    <el-col :span="21">
                        <el-input v-model="form.age" autocomplete="off"></el-input>
                    </el-col>
                </el-form-item>
                <el-form-item label="性别" :label-width="formLabelWidth">
                    <el-radio v-model="form.gender" label="0">男</el-radio>
                    <el-radio v-model="form.gender" label="1">女</el-radio>
                </el-form-item>
                <el-form-item label="生日" :label-width="formLabelWidth">
                    <el-col :span="21">
                        <el-date-picker type="date" placeholder="请选择日期" format="yyyy年 MM月 dd日" v-model="form.birthday"
                                        value-format="yyyy-MM-dd"></el-date-picker>
                    </el-col>
                </el-form-item>
                <el-form-item label="部门" :label-width="formLabelWidth">
                    <el-select v-model="form.depart" placeholder="请选择部门">
                       <span v-for="(value, index) in department" :key="index">
                            <el-option :label="value['department']" :value="value['id']"></el-option>
                        </span>
                    </el-select>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer" align="center">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="add_emp">确 定</el-button>
            </div>
        </el-dialog>


        <el-dialog title="修改员工" :visible.sync="dialogFormVisible1" width="700px" @close="close_dialog(1)">
            <el-form :model="form1">
                <el-form-item label="头像" :label-width="formLabelWidth">
                    <el-upload
                        class="avatar-uploader"
                        action=""
                        :auto-upload="false"
                        :show-file-list="false"
                        :on-change="changeUpload">
                        <img v-if="imageUrl" :src="imageUrl" class="avatar" alt="">
                        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                    </el-upload>
                </el-form-item>
                <el-form-item label="id" :label-width="formLabelWidth">
                    <el-col :span="21">
                        <el-input v-model="form1.id" autocomplete="off" readonly></el-input>
                    </el-col>
                </el-form-item>
                <el-form-item label="名字" :label-width="formLabelWidth">
                    <el-col :span="21">
                        <el-input v-model="form1.name" autocomplete="off"></el-input>
                    </el-col>
                </el-form-item>
                <el-form-item label="工资" :label-width="formLabelWidth">
                    <el-col :span="21">
                        <el-input v-model="form1.salary" autocomplete="off"></el-input>
                    </el-col>
                </el-form-item>
                <el-form-item label="年龄" :label-width="formLabelWidth">
                    <el-col :span="21">
                        <el-input v-model="form1.age" autocomplete="off"></el-input>
                    </el-col>
                </el-form-item>
                <el-form-item label="性别" :label-width="formLabelWidth">
                    <el-radio v-model="form1.gender" :label="0">男</el-radio>
                    <el-radio v-model="form1.gender" :label="1">女</el-radio>
                </el-form-item>
                <el-form-item label="生日" :label-width="formLabelWidth">
                    <el-col :span="21">
                        <el-date-picker type="date" placeholder="请选择需要修改的日期" format="yyyy年 MM月 dd日"
                                        v-model="form1.birthday"
                                        value-format="yyyy-MM-dd"></el-date-picker>
                    </el-col>
                </el-form-item>
                <el-form-item label="部门" :label-width="formLabelWidth">
                    <el-select v-model="form1.depart" placeholder="请选择部门">
                        <span v-for="(value, index) in department" :key="index">
                            <el-option :label="value['department']" :value="value['id']"></el-option>
                        </span>
                    </el-select>
                </el-form-item>
            </el-form>
            <div align="center">
                <el-button @click="dialogFormVisible1 = false">取 消</el-button>
                <el-button type="primary" @click="update">确 定</el-button>
            </div>
        </el-dialog>


    </div>
</template>

<script>
export default {
    name: "index",
    data() {
        return {
            date: new Date().toLocaleDateString(),
            time: new Date().toLocaleTimeString('chinese', {hour12: false}),
            name: '',
            emp_list: '',
            department: '',
            dialogFormVisible1: false,
            dialogFormVisible: false,
            imageUrl: '',
            form: {
                name: '',
                salary: '',
                age: '',
                gender: '',
                birthday: '',
                depart: '',
                head_pic: '',
            },
            form1: {
                id: '',
                name: '',
                salary: '',
                age: '',
                gender: '',
                birthday: '',
                depart: '',
                head_pic: '',
            },
            old_form: {},
            formLabelWidth: '90px',
            info: '',
            info_click: false,
            total: 0,
            page_now: 1,
        }
    },
    created() {
        if (sessionStorage.name !== undefined) {
            this.name = sessionStorage.name;
            this.get_emp_list();
            this.get_department();
        } else {
            this.$message({
                showClose: true,
                message: '对不起，您还未登录，请登录后在访问!',
                type: 'error'
            });
            this.$router.push("/");
        }
    },
    watch: {
        info(val, oldVal) {
            if (val === '') {
                this.info_click = false;
                this.get_emp_list();
            }
        }
    }
    , methods: {
        logout() {
            this.$confirm('此操作将退出登录, 是否继续?', '', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.$message({
                    type: 'success',
                    message: '退出成功!',
                });
                sessionStorage.removeItem('name');
                sessionStorage.removeItem('token');
                this.$router.push('/');
            }).catch(() => {

            });
        },
        get_emp_list() {
            // this.$axios({
            //     url: 'http://127.0.0.1:8000/emplist/',
            //     method: 'get',
            //     headers: {
            //         'Authorization': 'auth ' + sessionStorage.token + ' jwt'
            //     }
            // }).then(res => {
            //     // console.log(res);
            //     this.emp_list = res.data;
            // }).catch(error => {
            //     if (error.request.status === 403) {
            //         this.$message({
            //             type: 'error',
            //             message: JSON.parse(error.request.response)["detail"]
            //         });
            //         sessionStorage.removeItem('name');
            //         sessionStorage.removeItem('token');
            //         this.$router.push('/');
            //     }
            // })
            this.$axios({
                url: 'http://127.0.0.1:8000/search/?page=' + this.page_now,
                method: 'get',
                headers: {
                    'Authorization': 'auth ' + sessionStorage.token + ' jwt'
                },
            }).then(res => {
                // console.log(res);
                this.emp_list = res.data['results'];
                this.total = res.data['count'];
            }).catch(error => {
                if (error.request.status === 403) {
                    this.$message({
                        type: 'error',
                        message: JSON.parse(error.request.response)["detail"]
                    });
                    sessionStorage.removeItem('name');
                    sessionStorage.removeItem('token');
                    this.$router.push('/');
                }
            })
        },
        page(page) {
            let info = '';
            if (this.info_click)
                info = this.info;
            this.page_now = page;
            this.$axios({
                url: 'http://127.0.0.1:8000/search/?page=' + page,
                method: 'get',
                params: {
                    search: info,
                },
                headers: {
                    'Authorization': 'auth ' + sessionStorage.token + ' jwt'
                },
            }).then(res => {
                // console.log(res);
                this.emp_list = res.data['results'];
                this.total = res.data['count'];
            }).catch(error => {
                if (error.request.status === 403) {
                    this.$message({
                        type: 'error',
                        message: JSON.parse(error.request.response)["detail"]
                    });
                    sessionStorage.removeItem('name');
                    sessionStorage.removeItem('token');
                    this.$router.push('/');
                }
            })
        },
        get_department() {
            this.$axios({
                url: 'http://127.0.0.1:8000/department/',
                method: 'get',
                headers: {
                    'Authorization': 'auth ' + sessionStorage.token + ' jwt'
                }
            }).then(res => {
                // console.log(res);
                this.department = res.data;
            }).catch(error => {
                if (error.request.status === 403) {
                    this.$message({
                        type: 'error',
                        message: JSON.parse(error.request.response)["detail"]
                    });
                    sessionStorage.removeItem('name');
                    sessionStorage.removeItem('token');
                    this.$router.push('/');
                }
            })
        },
        del_emp(id, name, index) {
            this.$confirm('此操作将永久删除 ' + name + ' 员工, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.$axios({
                    url: 'http://127.0.0.1:8000/emplist/' + id + '/',
                    method: 'delete',
                    headers: {
                        'Authorization': 'auth ' + sessionStorage.token + ' jwt'
                    }
                }).then(res => {
                    // console.log(res);
                    // this.get_emp_list();
                    // this.$router.push('/index');
                    this.emp_list.splice(index, 1);
                    this.$message({
                        type: 'success',
                        message: '删除成功!'
                    });
                }).catch(error => {
                    if (error.request.status === 403) {
                        this.$message({
                            type: 'error',
                            message: JSON.parse(error.request.response)["detail"]
                        });
                        sessionStorage.removeItem('name');
                        sessionStorage.removeItem('token');
                        this.$router.push('/');
                    } else
                        this.$message({
                            type: 'error',
                            message: '删除失败!'
                        });
                })
            }).catch(() => {
                this.$message({
                    type: 'info',
                    message: '已取消删除!'
                });
            });
        },
        add_emp() {
            let flag = true;
            for (let key in this.form)
                if (this.form[key] === '')
                    flag = false;
            // console.log(flag);
            if (flag) {
                let formData = new FormData();
                // 将所有的属性添加至formData中 参数1：后台接受的key  参数2：用户输入的值
                formData.append("head_pic", this.form.head_pic);
                formData.append("id", '');
                formData.append("name", this.form.name);
                formData.append("salary", this.form.salary);
                formData.append("age", this.form.age);
                formData.append("gender", this.form.gender);
                formData.append("birthday", this.form.birthday);
                formData.append("depart", this.form.depart);
                this.$axios({
                    url: 'http://127.0.0.1:8000/emplist/',
                    method: 'post',
                    data: formData,
                    headers: {
                        // 当前请求时包含文件
                        'content-type': "multipart/form-data",
                        'Authorization': 'auth ' + sessionStorage.token + ' jwt'
                    },
                }).then(res => {
                    // console.log(res);
                    this.dialogFormVisible = false;
                    this.get_emp_list();
                    this.$message({
                        type: 'success',
                        message: '添加成功!'
                    });
                }).catch(error => {
                    if (error.request.status === 403) {
                        this.$message({
                            type: 'error',
                            message: JSON.parse(error.request.response)["detail"]
                        });
                        sessionStorage.removeItem('name');
                        sessionStorage.removeItem('token');
                        this.$router.push('/');
                    }
                    if (error.request.status === 400) {
                        let msg = '';
                        for (let i in JSON.parse(error.request.response)) {
                            if (i === 'salary')
                                msg += '请输入正确的工资!'
                            if (i === 'age')
                                if (msg)
                                    msg += '<br/>请输入正确的年龄！'
                                else
                                    msg += '请输入正确的年龄！'
                        }
                        this.$message({
                            dangerouslyUseHTMLString: true,
                            type: 'error',
                            message: msg,
                        });
                    }
                });
            } else
                this.$message({
                    type: 'error',
                    message: '输入有空值!'
                });
        },
        update_emp(id) {
            this.dialogFormVisible1 = true;
            this.$axios({
                url: 'http://127.0.0.1:8000/emplist/' + id + '/',
                method: 'get',
                headers: {
                    'Authorization': 'auth ' + sessionStorage.token + ' jwt'
                }
            }).then(res => {
                // console.log(res.data);
                this.form1.id = parseInt(res.data['id']);
                this.form1.name = res.data['name'];
                this.form1.salary = res.data['salary'];
                this.form1.age = res.data['age'];
                this.form1.gender = parseInt(res.data['gender']);
                this.form1.birthday = res.data['birthday'];
                this.form1.depart = parseInt(res.data['depart']);
                this.imageUrl = res.data['head_pic'];
                this.old_form = JSON.parse(JSON.stringify(this.form1));
            }).catch(error => {
                if (error.request.status === 403) {
                    this.$message({
                        type: 'error',
                        message: JSON.parse(error.request.response)["detail"]
                    });
                    sessionStorage.removeItem('name');
                    sessionStorage.removeItem('token');
                    this.$router.push('/');
                }
            })
        },
        update() {
            if (JSON.stringify(this.form1) !== JSON.stringify(this.old_form)) {
                let formData = new FormData();
                // 将所有的属性添加至formData中 参数1：后台接受的key  参数2：用户输入的值
                if (this.form1.head_pic) {
                    formData.append("head_pic", this.form1.head_pic);
                }
                formData.append("id", this.form1.id);
                formData.append("name", this.form1.name);
                formData.append("salary", this.form1.salary);
                formData.append("age", this.form1.age);
                formData.append("gender", this.form1.gender);
                formData.append("birthday", this.form1.birthday);
                formData.append("depart", this.form1.depart);
                this.$axios({
                    url: 'http://127.0.0.1:8000/emplist/' + this.form1.id + '/',
                    method: 'patch',
                    data: formData,
                    headers: {
                        // 当前请求时包含文件
                        'content-type': "multipart/form-data",
                        'Authorization': 'auth ' + sessionStorage.token + ' jwt'
                    },
                }).then(res => {
                    // console.log(res);
                    this.dialogFormVisible = false;
                    this.get_emp_list();
                    this.$message({
                        type: 'success',
                        message: '修改成功!'
                    });
                    this.dialogFormVisible1 = false;
                }).catch(error => {
                    if (error.request.status === 403) {
                        this.$message({
                            type: 'error',
                            message: JSON.parse(error.request.response)["detail"]
                        });
                        sessionStorage.removeItem('name');
                        sessionStorage.removeItem('token');
                        this.$router.push('/');
                    } else
                        this.$message({
                            type: 'error',
                            message: '修改失败，输入错误!'
                        });
                });
            } else
                this.$message({
                    type: 'error',
                    message: '没有修改数据!'
                });
        },
        changeUpload(file) {
            const isJPG = file.raw['type'] === 'image/jpeg' || 'image/png';
            const isLt2M = file.raw['size'] / 1024 / 1024 < 2;
            if (!isJPG) {
                this.$message.error('上传头像图片只能是 JPG/PNG 格式!');
            } else if (!isLt2M) {
                this.$message.error('上传头像图片大小不能超过 2MB!');
            } else {
                this.imageUrl = URL.createObjectURL(file.raw);
                if (this.dialogFormVisible)
                    this.form.head_pic = file.raw
                if (this.dialogFormVisible1)
                    this.form1.head_pic = file.raw
            }
        },
        close_dialog(index) {
            if (index === 0) {
                this.imageUrl = '';
                this.form.name = '';
                this.form.salary = '';
                this.form.birthday = '';
                this.form.depart = '';
                this.form.age = '';
                this.form.gender = '';
                this.form.head_pic = '';
            } else {
                this.imageUrl = '';
            }
        },
        search() {
            if (this.info !== '') {
                this.$axios({
                    url: 'http://127.0.0.1:8000/search/',
                    method: 'get',
                    params: {
                        search: this.info,
                    },
                    headers: {
                        'Authorization': 'auth ' + sessionStorage.token + ' jwt'
                    },
                }).then(res => {
                    // console.log(res);
                    this.emp_list = res.data['results'];
                    this.total = res.data['count'];
                    this.info_click = true;
                    this.$message({
                        type: 'success',
                        message: '查询成功!'
                    });
                }).catch(error => {
                    if (error.request.status === 403) {
                        this.$message({
                            type: 'error',
                            message: JSON.parse(error.request.response)["detail"]
                        });
                        sessionStorage.removeItem('name');
                        sessionStorage.removeItem('token');
                        this.$router.push('/');
                    }
                })
            } else
                this.$message({
                    type: 'error',
                    message: '请输入搜索内容!'
                });
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

<style>
.avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
}

.avatar-uploader .el-upload:hover {
    border-color: #409EFF;
}

.avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 80px;
    height: 80px;
    line-height: 80px;
    text-align: center;
}

.avatar {
    width: 80px;
    height: 80px;
    display: block;
}
</style>
