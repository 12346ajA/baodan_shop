<template>
  <!--
      作者：luoyiming
      时间：2019-10-25
      描述：会员-用户列表-会员充值
  -->
  <div>
    <el-dialog title="发放奖品" :visible.sync="dialogVisible" @close='dialogFormVisible' :close-on-click-modal="false" :close-on-press-escape="false">
      <el-form size="small" ref="form" :model="form">
        <el-form-item label="收件人" :label-width="formLabelWidth">
          {{model.name}}
        </el-form-item>
        <el-form-item label="收件电话" :label-width="formLabelWidth">
          {{model.phone}}
        </el-form-item>
        <el-form-item label="物流公司" prop="express_id" :rules="[{required: true,message: ' '}]"  :label-width="formLabelWidth">
          <el-select size="small" v-model="form.express_id" placeholder="请选择">
            <el-option v-for="(item, index) in expressList" :key="index" :label="item.express_name" :value="item.express_id"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="物流单号" :label-width="formLabelWidth" prop="express_no" :rules="[{required: true,message: ' '}]">
          <el-input type="textarea" v-model="form.express_no" placeholder="请输入物流单号"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible">取 消</el-button>
        <el-button type="primary" @click="submit" :loading="loading">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import LotteryApi from '@/api/lottery.js';
import draggable from 'vuedraggable';
export default {
  components: {
    draggable
  },
  data() {
    return {
      loading: false,
      /*左边长度*/
      formLabelWidth: '120px',
      /*是否显示*/
      dialogVisible: false,
      form: {
        express_id: '',
        express_no: ''
      },
    };
  },
  props: ['open_send', 'model', 'expressList'],
  created() {
    this.dialogVisible = this.open_send;
  },
  methods: {
    /*处理*/
    submit() {
      let self = this;
      let form = self.form;
      form.record_id = self.model.record_id;
      self.$refs.form.validate((valid) => {
        if (valid) {
          self.loading = true;
          LotteryApi.send(form, true).then(data => {
              self.loading = false;
              self.$message({
                message: data.msg,
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
        this.$emit('closeDialog', {
          type: 'success',
          openDialog: false
        })
      } else {
        this.$emit('closeDialog', {
          type: 'error',
          openDialog: false
        })
      }
    },
  }
};

</script>
<style>
.edit_container {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  line-height: 20px;
  color: #2c3e50;
}

.ql-editor {
  height: 400px;
}

.draggable-list {
  display: flex;
  justify-content: flex-start;
  flex-wrap: wrap;
}

.draggable-list .wrapper>span {
  display: flex;
  justify-content: flex-start;
  flex-wrap: wrap;
}

.draggable-list .item {
  position: relative;
  width: 110px;
  height: 110px;
  margin-top: 10px;
  margin-right: 10px;
  border-radius: 8px;
  overflow: hidden;
  border: 1px solid #dddddd;
}

.draggable-list .delete-btn {
  position: absolute;
  top: 0;
  right: 0;
  width: 16px;
  height: 16px;
  background: red;
  line-height: 16px;
  font-size: 16px;
  color: #ffffff;
  display: none;
}

.draggable-list .item:hover .delete-btn {
  display: block;
}

.draggable-list .item img {
  position: absolute;
  top: 50%;
  left: 50%;
  -webkit-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
  max-height: 100%;
  max-width: 100%;
}

.draggable-list .img-select {
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px dashed #dddddd;
  font-size: 30px;
}

.draggable-list .img-select i {
  color: #409eff;
}

</style>
