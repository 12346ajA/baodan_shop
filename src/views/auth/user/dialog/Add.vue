<template>
  <!--
        作者：luoyiming
        时间：2019-10-25
        描述：权限-管理员列表-添加管理员
    -->
  <el-dialog title="添加管理员" :visible.sync="dialogVisible" @close="dialogFormVisible" :close-on-click-modal="false"
    :close-on-press-escape="false">
    <!--form表单-->
    <el-form size="small" ref="form" :model="form" :label-width="formLabelWidth">
      <el-form-item label="用户名" prop="user_name" :rules="[{required: true,message: ' '}]">
        <el-input v-model="form.user_name" placeholder="请输入用户名"></el-input>
      </el-form-item>
      <el-form-item label="所属角色" prop="role_id" :rules="[{required: true,message: ' '}]">
        <el-select v-model="form.role_id" :multiple="true">
          <el-option v-for="item in roleList" :value="item.role_id" :key="item.role_id"
            :label="item.role_name_h1"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="登录密码" prop="password" :rules="[{required: true,message: ' '}]">
        <el-input v-model="form.password" placeholder="请输入登录密码" type="password"></el-input>
      </el-form-item>
      <el-form-item label="确认密码" prop="confirm_password" :rules="[{required: true,message: ' '}]">
        <el-input v-model="form.confirm_password" placeholder="请输入确认密码" type="password"></el-input>
      </el-form-item>
      <el-form-item label="姓名" prop="real_name" :rules="[{required: true,message: ' '}]">
        <el-input v-model="form.real_name"></el-input>
      </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="dialogVisible = false">取 消</el-button>
      <el-button type="primary" @click="onSubmit" :loading="loading">确 定</el-button>
    </div>
  </el-dialog>
</template>

<script>
  import AuthApi from '@/api/auth.js';
  export default {
    data() {
      return {
        /*左边长度*/
        formLabelWidth: '120px',
        /*是否显示*/
        loading: false,
        /*是否显示*/
        dialogVisible: false,
        /*form表单对象*/
        form: {
          user_name: '',
          role_id: [],
          password: '',
          confirm_password: '',
          real_name: ''
        },
      };
    },
    props: ['open', 'roleList'],
    watch: {
      open: function(n, o) {
        if (n != o) {
          this.dialogVisible = this.open;
        }
      }
    },
    created() {},
    methods: {
      /*添加*/
      onSubmit() {
        let self = this;
        let form = self.form;
        self.$refs.form.validate((valid) => {
          if (valid) {
            self.loading = true;
            AuthApi.userAdd(form, true)
              .then(data => {
                self.loading = false;
                self.$message({
                  message: '恭喜你，添加成功',
                  type: 'success'
                });
                self.dialogFormVisible(true);
              })
              .catch(error => {
                self.loading = false;
              });
          }
        });
      },

      /*关闭弹窗*/
      dialogFormVisible(e) {
        if (e) {
          this.$emit('close', {
            type: 'success',
            openDialog: false
          });
        } else {
          this.$emit('close', {
            type: 'error',
            openDialog: false
          });
        }
      }
    }
  };
</script>

<style></style>