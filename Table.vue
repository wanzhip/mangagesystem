<template>
    <div class="cell">
            <el-table
              :data="tableData"
              style="width: 100%"
              :default-sort = "{prop: 'id', order: 'descending'}"
              :row-style="{height:'20px'}"
              :cell-style="{padding:'0px'}"
              border>
            <el-table-column
              align="center"
                label="ID"
                width="60px"
                prop="id"
                sortable>
              </el-table-column>
              <el-table-column
              align="center"
                label="账号名称"
                prop="name">
              </el-table-column>
              <el-table-column
              align="center"
                label="头像"
                >
              </el-table-column>
              <el-table-column
                label="手机号码"
                align="center"
                prop="phonenum"
                >
              </el-table-column>
              <el-table-column
                label="邮箱"
                align="center"
                prop="email"
                >
              </el-table-column>
              <el-table-column
                label="添加时间"
                align="center"
                prop="regtime"
                >
              </el-table-column>
              <el-table-column
                label="最后登录时间"
                align="center"
                prop="last_time"
                >
              </el-table-column>
              <el-table-column
                label="账号状态"
                align="center"
                >
                <template slot-scope="scope">
                  {{ scope.row.status===1 ?'正常':'禁用'}}
                </template> 
              </el-table-column>
              <!-- <el-table-column
                label="禁止登录日期"
                align="center"
                prop="stoplogindate"
                >
              </el-table-column> -->
              <el-table-column
                label="账号类型"
                align="center"
                >
                <template slot-scope="scope">
                  {{ scope.row.isroot===1 ?'超级管理员':'普通用户'}}
                </template>
              </el-table-column>
              <el-table-column min-width="260px" label="操作" align="center" >
                <template slot-scope="scope" v-if="scope.row.isroot != 1">
                  <el-button
                    size="mini"
                    type="success"
                    @click="handleReset(scope.$index, scope.row)">
                    <i class="el-icon-refresh"></i>
                    重置</el-button>
                  <el-button
                    size="mini"
                    type="primary"
                    @click="handleEdit(scope.$index, scope.row)">
                    <i class="el-icon-edit"></i>
                    编辑</el-button>
                    <el-button
                    size="mini"
                    :type="scope.row.status===1 ?'warning':'success'"
                    @click="handleUseless(scope.$index, scope.row)">
                    <i :class= "scope.row.status===1 ?'el-icon-close':'el-icon-check'"></i>
                    {{ scope.row.status===1 ?'禁用':'启用'}}
                    </el-button>
                  <el-button
                    size="mini"
                    type="danger"
                    @click="handleDelete(scope.$index, scope.row)">
                    <i class="el-icon-delete"></i>
                    删除
                    </el-button>
                </template>
              </el-table-column>
              <!-- <el-table-column
                prop="tag"
                label="标签"
                width="100"
                :filters="[{ text: '家', value: '家' }, { text: '公司', value: '公司' }]"
                :filter-method="filterTag"
                filter-placement="bottom-end">
                <template slot-scope="scope">
                    <el-tag
                    :type="scope.row.tag === '家' ? 'primary' : 'success'"
                    disable-transitions>{{scope.row.tag}}</el-tag>
                </template>
                </el-table-column> -->
            </el-table>
            <!-- <div class="courseDetailBottom">
                <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage"
                    :page-sizes="pageSizes"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="total">
                </el-pagination>
            </div> -->
            <div>
              <EditAdmin ref="editAdmin"/>
            </div>
            
    </div>
</template>

<script>
import EditAdmin from "./EditAdmin"
export default {
  components:{
    EditAdmin
  },
  // inject:['reload'],注释
  data() {
      return {
        // currentPage:1,
        // pageSizes:[10, 20, 30, 40, 50],
        // total:10,
        // optionSize:10,
        tableData: []
      }
  },
  created(){
      this.$axios.get('/v1/admin'+'?token='+this.$store.state.Authorization).then(res =>{
            console.log(res,"22");
            this.tableData = res.data.data.data;
        }).catch(error=>{
          console.log(error)
        })
    },
   methods: {
      refresh(){
        this.$axios.get('/v1/admin'+'?token='+this.$store.state.Authorization).then(res =>{
            this.tableData = res.data.data.data;
        }).catch(error=>{
          console.log(error)
        })
      },
      handleReset(index,row){
        console.log(index,row);
        console.log(row.id)
        const id = row.id;
        this.$axios.post('/v1/admin/reset/'+id,{token:this.$store.state.Authorization}).then(res=>{
          console.log(res)
          if(res.data.code == 0){
            this.$message({
                type: 'success',
                message: `${res.data.msg}`
          });
          }else{
            this.$message({
                type: 'error',
                message: `${res.data.msg}`
            });
          }
        }).catch(error=>{
          console.log(error)
        })
      },
      handleEdit(index, row) {
       this.$refs.editAdmin.showModal = true;
       this.$refs.editAdmin.ruleForm = row;
      },
      handleDelete(index,row) {
        const id = row.id;
        console.log(row,id);
        const params = {token:this.$store.state.Authorization};
        this.$confirm('确定要删除吗?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$axios.delete('/v1/admin/'+id,{data:params}).then(res=>{
            console.log(res);
            if(res.data.code == 0){
              this.$message({
              type: 'success',
              message: '删除成功!'
              });
              this.refresh();
            }
          }).catch(error=>{
            console.log(error);
          })
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });          
        });
      }, 
      handleUseless(index, row) {
        const id = row.id;
        const params = {token:this.$store.state.Authorization};
        if(row.status == 1){
          this.$confirm('确定要禁用吗?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.$axios.post('/v1/admin/ban/'+id,params).then(res=>{
              console.log(res);
              if(res.data.code == 0){
                this.$message({
                type: 'success',
                message: `${res.data.msg}`
                });
                this.refresh();
              }
            }).catch(error=>{
              console.log(error);
            })
          }).catch(() => {
            this.$message({
              type: 'info',
              message: "禁用失败"
            });          
          });
        }else{
          this.$confirm('确定要启用吗?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.$axios.post('/v1/admin/start/'+id,params).then(res=>{
              console.log(res);
              if(res.data.code == 0){
                this.$message({
                type: 'success',
                message: `${res.data.msg}`
                });
                // this.reload();
                this.refresh();
              }
            }).catch(error=>{
              console.log(error);
            })
          }).catch(() => {
            this.$message({
              type: 'info',
              message: "禁用失败"
            });          
          });
        }
      }, 
      // filterTag(value, row) {
      //   return row.tag === value;
      // },
      // handleSizeChange(val) {
      //   console.log(`每页 ${val} 条`);
      // },
      // handleCurrentChange(val) {
      //   console.log(`当前页: ${val}`);
      // }
    }
}
</script>

<style lang="scss" scoped>
.cell{
    margin-top: 2rem;
}
.courseDetailBottom{
  margin-top: 2rem;
}
</style>