<template>
    <div class="layer">
      <div class="mask" v-if="showModal" ></div>
      <div class="pop" v-if="showModal">
          <div class="top">
              <div class="tag">添加管理员</div>
              <div @click="showModal=false" class="close touch">
                  <i class="el-icon-close"></i>
              </div>
          </div>
          <div class="main">
            <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
                <el-form-item label="账号" prop="account">
                    <el-input v-model="ruleForm.account"></el-input>
                </el-form-item>
                <!-- <el-form-item label="密码" prop="pwd">
                    <el-input v-model="ruleForm.pwd"></el-input>
                </el-form-item> -->
                <el-form-item label="真实姓名" prop="realname">
                    <el-input v-model="ruleForm.realname"></el-input>
                </el-form-item>
                 <el-form-item label="角色类型" prop="region">
                  <el-select v-model="ruleForm.region" placeholder="请选择角色类型" style="width:100%">
                    <el-option  
                      v-for="(item,index) in options"
                      :key="index"
                      :label="item.name"
                      :value="item.id"></el-option>
                  </el-select>
                </el-form-item>
                <el-form-item label="备注" prop="memo">
                    <el-input v-model="ruleForm.memo"></el-input>
                </el-form-item>
            </el-form>
            
            <div class="sub">
               <el-button type="primary" size="small" @click="submitForm('ruleForm')">确定</el-button>
               <el-button size="small" @click="showModal=false">取消</el-button>
            </div>
          </div>
      </div>
      <el-button type="primary" size="small" @click="showModal=true" class="btn">
          <i class="el-icon-plus"></i>
            {{ tag }}
      </el-button>
    </div>
</template>


<script>
export default {
    name:"AddAdmin",
    props:{
      tag:String
    },
    data(){
        return{
            showModal: false,
            options:[],
            ruleForm: {
                account: '',
                realname:'',
                memo:'',
                region:''
            },
            rules: {
                account: [
                  { required: true, message: '请输入账户', trigger: 'blur' },
                  { min: 6, max: 16, message: '长度在 6 到 16 个字符', trigger: 'blur' }
                ],
                region: [
                  { required: true, message: '请选择角色类型', trigger: 'change' }
                ],
                realname:[
                  {required:true,message:'请输入真实姓名',trigger:'blur'}
                ]
            }
        }
    },
    created(){
      this.$axios.get('/v1/role'+'?token='+this.$store.state.Authorization).then(res=>{
        console.log(res);
        this.options = res.data.data;
        console.log(this.options)
      }).catch(error=>{
        console.log(error)
      })
    },
    methods:{
        submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
              //验证成功
            this.$axios.post('/v1/admin',{
              device:'pc',
              token:localStorage.getItem('Authorization'),
              name:this.ruleForm.account,
              realname:this.ruleForm.realname,
              roleid:this.ruleForm.region,
              memo:this.ruleForm.memo?this.ruleForm.memo:''
            }).then(res=>{
              console.log(res,'000')
              if(res.data.code == 0){
                this.showModal = false;
                this.ruleForm.account = '';
                this.ruleForm.phone = '';
                this.ruleForm.email = '';
                this.$message({
                showClose: true,
                message: `${res.data.msg}`,
                type: 'success',
                duration:0
              });
              this.reload();
              }else{
                this.$message({
                showClose: true,
                message: `${res.data.msg}`,
                type: 'error',
              });
              }
            }).catch(err=>{
              console.log(err)
            })
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
    }
}
</script>

<style lang="scss" scoped>
.layer{
    margin-top: 2rem;
}

.right{
  width: 3rem;
  height: 3rem;
  border: 1px solid;
  margin-right: 5rem;
  margin-top: 1.5rem;
  border-radius: 50%;
}

.mask {
  background-color: #000;
  opacity: 0.5;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 2
}
.pop {
  background-color: #fff;
  position: fixed;
  width: 42rem;
  height: 42rem;
  left: 50%;
  top: 50%;
  margin-left: -21rem;
  margin-top: -30rem;
  z-index: 2;
  .top{
      display:flex;
      flex-direction:row;
      justify-content: flex-end;
      height: 4rem;
      line-height: 4rem;
      border-bottom: 1px solid #eee;
      .tag{
          margin-right: 36%;
      }
      .close{
          padding: 0 1rem;
      }
  }
  .main{
    padding: 4rem 8rem 0 0;
      .sub{
          margin: 3rem 0 0 15rem;
      }
  }
}
</style>

