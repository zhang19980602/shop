<template>
  <div><h1 align="center">数据展示</h1>
    <div id="searchDiv" align="center">

      <el-form :inline="true" :model="searchForm" class="demo-form-inline">

        <el-form-item label="名称">
          <el-input v-model="searchForm.name" placeholder="名称"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="queryPinpaiData(1)">查询</el-button>
          <el-button type="success" @click="addFormFlag1()">新增</el-button>
        </el-form-item>
      </el-form>
    </div>

    <div id="carTable">
      <el-table
        :data="pinpaiData"
        style="width: 100%">
        <el-table-column
          prop="id"
          label="序号"
          width="180">
        </el-table-column>
        <el-table-column
          prop="name"
          label="名称"
          width="180">
        </el-table-column>

        <el-table-column
          prop="bandE"
          label="首字母"
        >
        </el-table-column>

        <el-table-column
          prop="imgpath"
          label="图片">
          <!-- 按文本处理   :formatter="formatImg"    -->
          <!-- 模板处理  html  -->
          <template slot-scope="scope">
            <!-- <img src="192.168.1.43:8080/imgFiless/f86a6cd6-a0e3-47a6-ba03-62a68ff41c99.gif"/>-->
            <img width="50px" :src="scope.row.imgpath"/>
          </template>
        </el-table-column>

        <el-table-column
          prop="bandDesc"
          label="内容">
        </el-table-column>

        <el-table-column
          prop="ord"
          label="排序">
        </el-table-column>


        <el-table-column
          prop="id"
          label="操作">
          <template slot-scope="scope">
            <el-button type="primary" icon="el-icon-edit"   @click="toUpdatePinpai(scope.row.id)"></el-button>
            <el-button type="danger" icon="el-icon-delete"  @click="deletePinpai(scope.row.id)"></el-button>
          </template>
        </el-table-column>

      </el-table>

      <el-pagination
        @current-change="handleCurrentChange"
        @size-change="handleSizeChange"
        :page-sizes="sizes"
        :page-size="size"
        layout="total, sizes, prev, pager, next, jumper"
        :total="count">
      </el-pagination>
    </div>
<!--新增模板-->
    <div>
      <!--新增模板-->
      <el-dialog title="品牌修改信息" :visible.sync="addFormFlag">
        <el-form :model="addForm" ref="addForm"  label-width="80px">
          <el-form-item label="品牌名称" prop="name">
            <el-input v-model="addForm.name" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="首写字母" prop="bandE">
            <el-input v-model="addForm.bandE" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="图片" prop="imgpath">
            <div> <img :src="addForm.imgpath" width="80"></div>
            <el-upload
              class="upload-demo"
              action="http://localhost:8080/api/pinpai/upload"
              :on-success="imgCallBack"
              name="img"
              list-type="picture">
              <el-button size="small" type="primary">点击上传</el-button>
              <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
            </el-upload>
          </el-form-item>
          <el-form-item label="品牌故事" prop="bandDesc">
            <el-input
              type="textarea"
              placeholder="请输入内容"
              v-model="addForm.bandDesc"
              maxlength="50"
              show-word-limit
            >
            </el-input>
          </el-form-item>
          <el-form-item label="排序" prop="ord">
            <el-input-number v-model="addForm.ord" controls-position="right"  :min="1" :max="10"></el-input-number>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="add">确 定</el-button>
          <el-button @click="addFormFlag = false">取 消</el-button>
        </div>
      </el-dialog>
    </div>

    <div>
      <!--修改模板-->
      <el-dialog title="品牌修改信息" :visible.sync="updateFormFlag">
        <el-form :model="updateForm" ref="updateForm"  label-width="80px">
          <el-form-item label="品牌名称" prop="name">
            <el-input v-model="updateForm.name" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="首写字母" prop="bandE">
            <el-input v-model="updateForm.bandE" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="图片" prop="imgpath">
            <div> <img :src="updateForm.imgpath" width="80"></div>
            <el-upload
              class="upload-demo"
              action="http://localhost:8080/api/pinpai/upload"
              :on-success="imgCallBack"
              name="img"
              list-type="picture">
              <el-button size="small" type="primary">点击上传</el-button>
              <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
            </el-upload>
          </el-form-item>

          <el-form-item label="品牌故事" prop="bandDesc">
            <el-input
              type="textarea"
              placeholder="请输入内容"
              v-model="updateForm.bandDesc"
              maxlength="50"
              show-word-limit
            >
            </el-input>
          </el-form-item>
          <el-form-item label="排序" prop="ord">
            <el-input-number v-model="updateForm.ord" controls-position="right" :min="1" :max="10"></el-input-number>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="update1">确 定</el-button>
          <el-button @click="updateFormFlag = false">取 消</el-button>
        </div>
      </el-dialog>
    </div>

  </div>
</template>

<script>
    export default {
      data(){
        return{
          searchForm:{name:""},
          /*  表格相关的数据  */
          pinpaiData:[],
          /* 分页相关的数据  */
          count:0,
          sizes:[2,3,5,10],
          size:2,
          /* 新增模块的数据  */
          addFormFlag:false,
          addForm:{
            id:"",
            name:"",
            bandE:"",
            imgpath:"",
            bandDesc:"",
            ord:""
          },
          /* 修改模块的数据  */
          updateFormFlag:false,
          updateForm:{
            id:"",
            name:"",
            bandE:"",
            imgpath:"",
            bandDesc:"",
            ord:""
          }
        }
      },
    methods:{
      addFormFlag1(){
        this.addFormFlag=true;
        this.addForm=[];
      },
      handleCurrentChange(page){
        console.log(page);
        this.queryPinpaiData(page);
      },
      handleSizeChange(size){
        this.size=size;
        this.queryPinpaiData(1);
      }, imgCallBack:function(response, file, fileList){ //图片上传的回调函数
        // 赋值
        this.updateForm.imgpath=response.data;
        this.addForm.imgpath=response.data;
      },
      queryPinpaiData:function(page){
        //参数格式化
        var searchStr=this.$qs.stringify(this.searchForm);
        console.log(searchStr);
        var url="http://192.168.1.43:8080/api/pinpai/queryAll?limit="+this.size+"&page="+page+"&"+searchStr;
        console.log(url);
        //发起请求
        this.$axios.get(url).then(res=>{
          this.pinpaiData=res.data.data;
          this.count=res.data.count;
        }).catch(err=>console.log(err));
      },
      //新增
      add(){
        console.log(this.addForm)
        var add=this.$qs.stringify(this.addForm)
        this.$axios.post("http://192.168.1.43:8080/api/pinpai/add?"+add).then(res=>{
          // 把请求的数据  赋给全局
          this.addFormFlag=false;
          this.queryPinpaiData(1);
        }).catch(err=>console.log(err));
      },//删除
      deletePinpai(id)
      {
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {

          this.$axios.post("http://192.168.1.43:8080/api/pinpai/delete?id="+id+"").then(res=>{
            // 把请求的数据  赋给全局
            this.$message({
              type: 'success',
              message: '删除成功!'
            });
            this.queryPinpaiData(1);
          }).catch(err=>console.log(err));
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
      },
      toUpdatePinpai(id){
        this.updateFormFlag=true;
        this.$axios.get("http://192.168.1.43:8080/api/pinpai/queryById?id="+id+"").then(res=>{
          // 把请求的数据  赋给全局
          this.updateForm=res.data.data
        }).catch(err=>console.log(err));
      },
      update1(){
        var update=this.$qs.stringify(this.updateForm)
        console.log(update)
        this.$axios.post("http://192.168.1.43:8080/api/pinpai/update?"+update).then(res=>{
          // 把请求的数据  赋给全局
          this.updateFormFlag=false;
          this.queryPinpaiData(1);
        }).catch(err=>console.log(err));
      }
    },
      created:function () {
        this.queryPinpaiData(1);
        
      }

    }
</script>

<style scoped>

</style>
