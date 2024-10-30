

<template>
  <div>
    <div style="margin-bottom: 20px">
      <el-input v-model="params.name" style="width: 200px; margin-right: 10px" placeholder="请输入姓名"></el-input>
      <el-input v-model="params.phone" style="width: 200px; margin-right: 10px" placeholder="请输入电话"></el-input>
      <el-button type="warning" @click="findBysearch()">搜索</el-button>
      <el-button type="warning" @click="reset()">清空</el-button>
      <el-button type="primary" @click="add()">新增</el-button>
    </div>
    <div>
      <el-table :data="tableData" style="width: 100%">

        <el-table-column prop="name" label="姓名" width="260"></el-table-column>
        <el-table-column prop="sex" label="性别" width="260"></el-table-column>
        <el-table-column prop="password" label="年龄" width="260"></el-table-column>
        <el-table-column prop="phone" label="电话" width="260"></el-table-column>

        <el-table-column  label="操作" width="240">
          <template slot-scope="scope">
            <el-button type="primary" @click = "edit(scope.row)">编辑</el-button>
            <el-popconfirm title="这是一段内容确定删除吗？" @confirm="del(scope.row.id)">
              <el-button slot="reference" type = "danger" style = "margin-left: 15px">删除</el-button>
            </el-popconfirm>

          </template>

        </el-table-column>
      </el-table>
    </div>
    <div class="block" style="margin-top: 20px">
      <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="params.pageNum"
          :page-sizes="[5, 10]"
          :page-size="params.pageSize"
          layout="total, sizes, prev, pager, next"
          :total="total">
      </el-pagination>
    </div>
    <div>
      <el-dialog title="请填写你的个人信息" :visible.sync="dialogFormVisible" width = "40%">
        <el-form :model="form">
          <el-form-item label="姓名" :label-width="50">
            <el-input v-model="form.name" autocomplete="off" styele = "width: 70%"></el-input>
          </el-form-item>
          <el-form-item label="性别" :label-width="50">
            <el-radio v-model="form.sex" label="男">男</el-radio>
            <el-radio v-model="form.sex" label="女">女</el-radio>
          </el-form-item>
          <el-form-item label="年龄" :label-width="50">
            <el-input v-model="form.password" autocomplete="off" styele = "width: 70%"></el-input>
          </el-form-item>
          <el-form-item label="电话" :label-width="50">
            <el-input v-model="form.phone" autocomplete="off" styele = "width: 70%"></el-input>
          </el-form-item>

        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="submit()">确 定</el-button>
        </div>
      </el-dialog>
    </div>
  </div>
</template>

<script>
import request from "@/utils/request";

export default {
  data() {
    return {
      params:{
        name:'',
        phone:'',
        pageNum:1,
        pageSize:5
      },
      tableData: [],
      total:0,
      dialogFormVisible: false,
      form:{}
    }
  },
  //页面加载时做某些事情，在created
  created() {
    this.findBysearch();
  },
  //定义控件触发的方法
  methods:{

    findBysearch(){
      request.get("/user/search",{
        params: this.params
      }).then(res => {
        if(res.code === "0"){
          this.tableData = res.data.list;
          this.total = res.data.total;

        }else{
          this.$message({
            showClose: true,
            message: res.msg,
            type: 'success'
          });
        }
      })
    },
    reset(){
      this.params = {
        pageNum:1,
        pageSize:5,
        name :'',
        phone:''
      }
      this.findBysearch();
    },
    handleSizeChange(pageSize){
      this.params.pageSize = pageSize
      this.findBysearch();
    },
    handleCurrentChange(pageNum){
      this.params.pageNum = pageNum;
      this.findBysearch();
    },
    add(){
      this.form = {};
      this.dialogFormVisible = true;
    },
    submit(){
      request.post("/user",this.form).then(res =>{
        if(res.code ==='0'){
          this.$message({
            showClose: true,
            message: '恭喜你，增加成功',
            type: 'success'
          });
          this.dialogFormVisible = false;
          this.findBysearch();
        }else{
          this.$message({
            showClose: true,
            message: res.msg,
            type: 'success'
          });
        }
      })
    },
    edit(obj){
      this.form = obj;
      this.dialogFormVisible = true;
    },
    del(id){
      request.delete("/user/"+ id).then(res =>{
        if(res.code === '0'){
          this.$message({
            showClose: true,
            message: '删除成功',
            type: 'success'
          });
          this.findBysearch();
        }else{
          this.$message({
            showClose: true,
            message: res.msg,
            type: 'success'
          });
        }
      })
    }

  }
}
</script>