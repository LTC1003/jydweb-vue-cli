<template>
  <div class="reset-paswd">
    <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" >
      <el-form-item>
        <h5 v-show="isShowMoblie">验证码将发送到{{covertStar(ruleForm.moblie)}}</h5>
      </el-form-item>
      <el-form-item prop="verify">
        <el-input v-model="ruleForm.verify" placeholder="请输入验证码">
          <template>
            <el-button type="success" slot="append"
            @click="getCodeVerify(ruleForm.moblie)"
            :disabled="isDisabled">{{getCodeState}}</el-button>
          </template>
        </el-input>
      </el-form-item>
      <el-form-item prop="pass">
        <el-input v-model="ruleForm.pass" placeholder="请输新密码"></el-input>
      </el-form-item>
      <el-form-item prop="againPass">
        <el-input v-model="ruleForm.againPass" placeholder="请再次输入"></el-input>
      </el-form-item>
      <el-form-item>
        <div class="button-group">
          <el-button type="primary" size="small" @click="goStepPage(1)">上一步</el-button>
          <el-button type="danger" size="small" @click="goStepPage(3)">下一步</el-button>
        </div>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
  export default {
    name: 'reset-paswd',
    components: {

    },
    data() {
      var validVer = (rule, value, callback) => {
        if(/^(\d){6}$/.test(value) == false) {
          callback(new Error("请获取验证码"));
        } else {
          callback();
        }
      };
      return {
        isDisabled: false,
        getCodeState: '获取验证码',
        isShowMoblie: false,
        ruleForm: {
          moblie: '',
          verify: '',
          pass: '',
          againPass: ''
        },
        rules:{
          verify: [
            { required: true, message: '必填', trigger: 'blur'},
            { validator: validVer, trigger: 'blur' }
          ],
          pass: [
            { required: true, message: '必填', trigger: 'blur' },
            { validator: (rule, value, callback) => {
              if(/^\w{6,12}$/.test(value) == false){
                return callback(new Error("6-12位字符数字"));
              } else {
                callback();
              }
            }, trigger: 'blur'}
          ],
          againPass: [
            { validator: (rule, value, callback) => {
              if(value !== this.ruleForm.pass) {
                callback(new Error('两次输入密码不一致!'));
              } else {
                callback();
              }
            }, trigger: 'change'}
          ]
        }
      }
    },
    mounted() {
      console.log(localStorage.phone)
      this.ruleForm.moblie = localStorage.phone;
    },
    methods: {
      // 请求手机验证码
      // getVerifyCode(){
      //   if(this.ruleForm.moblie == undefined || this.ruleForm.moblie == '' || this.ruleForm.moblie == 'undefined'){
      //     this.ruleForm.moblie = 'undefined';
      //   } else {
      //     this.$api.userInfo.getLoginSendcode({phone_number: this.ruleForm.moblie}).then(
      //       (res) => {
      //         console.log(res, 'zeizei');
      //         this.isShowMoblie = true;
      //       }
      //     )
      //   }
      // },
      getCodeVerify(value){
        if(/^1[3456789]\d{9}$/.test(value) == false){
          return this.$message.error("11位手机号不正确");
        } else {
          this.$api.userInfo.getLoginSendcode({phone_number: value }).then(res => {
            if (res.message === "success") {
              
            } else {
              this.$message.error(res.message);
            }
          })
          if(!this.isDisabled){
            let seed = 60;
            let num = seedCount => {
              seed --;
              if (seed < 1) {
                clearInterval(count);
                this.getCodeState = '重新发送';
                this.isDisabled = false;
              } else {
                this.isDisabled = true;
                this.getCodeState = `重新发送${seed}`;
              }
            };
            let count = setInterval(num, 1000);
          }
        }
      },
      goStepPage(num){
        if(num > 2){
          this.$refs.ruleForm.validate((valid) => {
            if (valid){
              this.$api.userInfo.loginCheckCode({
                phone_number: this.ruleForm.moblie,
                code: this.ruleForm.verify
              }).then(res => {
                // 验证码验证
                if (res.message === "success"){
                  let paramObj = {
                    userMobile: this.ruleForm.moblie,
                    newPassword: this.ruleForm.againPass,
                    phoneType: '-',
                    channel: '-',
                    lastLoginTime: (new Date()).valueOf().toString(),
                    lastLoginIp: '10.12.88.103',
                  }
                  // 调用忘记密码接口
                  this.$api.userInfo.forgetPassword(paramObj).then(res => {
                    console.log(res, 'success is paswd')
                    if(res.code === '00001') {
                      if(res.message === "注册成功"){
                        console.log("注册成功")
                      } else if(res.message === "操作成功"){
                        console.log("密码修改成功")
                      }
                      this.$router.push({name: 'reset-success'})
                      this.$emit('goStepState', num);
                    } else {
                      console.log('密码修改失败, 重来！')
                      return false;
                    }
                    
                  })
                } else if (res.code === '00000'){
                  this.$message.error(res.message);
                }
              })
            } else {
              return false;
            }
          })
        } else {
          this.$router.push({name: 'fillinfo'})
          this.$emit('goStepState', num);
        }
      },
      // 手机号遮盖
      covertStar(tel){
        console.log(tel);
        var reg = /^(\d{3})\d{4}(\d{4})$/;
        tel = tel.replace(reg, "$1****$2");
        return tel;
      },
    }
  }
</script>
<style lang="scss">
  .reset-paswd{
    width: 320px;
    margin: 0 auto;
    margin-top: 70px;
    .el-input-group__append button.el-button{
      background-color: #95C21F;
      color: #ffffff;
      border-radius: 3px;
    }
    .button-group{
      width: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
    }
  }
</style>
