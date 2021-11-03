<template>
  <div class="login-section">
    <!-- :rules="rules" -->
    <el-form
      label-position="top"
      label-width="100px" 
      class="demo-ruleForm"
      status-icon
      :rules="rules"
      :model="rulesForm"
      ref="ruleForm"
    >
      <el-form-item label="用户名" prop="name">
        <el-input type="text" v-model="rulesForm.name"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input type="password" v-model="rulesForm.password"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitFrom('ruleForm')">提交</el-button>
        <el-button>重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
import {register} from '@/service/api';
export default {
  data() {
    return {
      rulesForm:{
        name:"",
        password:""
      },
      rules:{
        name:[
          {required:true,message:'请输入用户名',trigger:'blur'},
          {min:3,max:5,message:'长度在3~5个字符',trigger:'blur'}
        ],
        password:[
          {required:true,message:'请输入密码',trigger:'blur'},
          {min:3,max:5,message:'长度在3~5个字符',trigger:'blur'}
        ]
      }
    };
  },
  methods: {
    submitFrom(formName){
      this.$refs[formName].validate((valid)=>{
        if(valid){
          register({
            name:this.rulesForm.name,
            password:this.rulesForm.password
          }).then((data)=>{
            console.log(data);
          })
        }else{
          console.log('error submit!!');
          return false;
        }
      })
    }
  }
}
</script>

<style lang="stylus">
.login-section
  padding 0px 20px
</style>
