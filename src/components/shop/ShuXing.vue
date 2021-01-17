<template>
  <div><h1 align="center">属性展示</h1>
    <div id="searchDiv" align="center">
    <el-form :inline="true" :model="searchForm" class="demo-form-inline">
      <el-form-item label="名称">
        <el-input v-model="searchForm.name" placeholder="名称"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="queryShuXing(1)">查询</el-button>
        <el-button type="success" @click="addFormFlag=true">新增</el-button>
      </el-form-item>
    </el-form>
  </div>

    <div id="carTable">
      <el-table
        :data="shuxingData"
        style="width: 100%">
        <el-table-column
          prop="id"
          label="序号"
          width="180">
        </el-table-column>
        <el-table-column
          prop="name"
          label="属性英文名称"
          width="180">
        </el-table-column>

        <el-table-column
          prop="nameCH"
          label="属性中文名字"
        >
        </el-table-column>

        <el-table-column
          prop="typeId"
          label="类型"
          :formatter="formaTypeId">
        </el-table-column>

        <el-table-column
          prop="isSKU"
          label="SKU"
          :formatter="SKU"
        >
        </el-table-column>

        <el-table-column
          prop="id"
          label="操作">
          <template slot-scope="scope">
            <el-button type="primary" icon="el-icon-edit"   @click="toUpdateshuxing(scope.row.id)"></el-button>
            <el-button type="danger" icon="el-icon-delete"  @click="deleteShuxing(scope.row.id)"></el-button>
            <el-button type="primary" @click="toShuXing_value(scope.row.id)" icon="el-icon-zoom-in"></el-button>
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
      <el-dialog title="属性新增信息" :visible.sync="addFormFlag">
        <el-form :model="addForm" ref="addForm"  label-width="80px">
          <el-form-item label="英文名称" prop="name">
            <el-input v-model="addForm.name" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="中文名称" prop="nameCH">
            <el-input v-model="addForm.nameCH" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="属性类型" prop="typeId">
            <el-select v-model="addForm.typeId" placeholder="请选择">
              <el-option
                v-for="item in bandData"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="类型" prop="type">
            <el-select v-model="addForm.type" placeholder="请选择">
              <el-option
                v-for="item in leixngData"
                :key="item.type"
                :label="item.name"
                :value="item.type">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="SKU" prop="isSKU">
            <el-switch
              v-model="addForm.isSKU"
              active-text="是"
              active-color="#13ce66"
              :active-value="1"
              inactive-text="否"
              :inactive-value="0">
            </el-switch>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="add">确 定</el-button>
          <el-button @click="addFormFlag = false">取 消</el-button>
        </div>
      </el-dialog>
    </div>

    <!--修改的模板-->
    <div>
      <!--修改的模板-->
      <el-dialog title="属性修改信息" :visible.sync="updateFormFlag">
        <el-form :model="updateForm" ref="addForm"  label-width="80px">
          <el-form-item label="英文名称" prop="name">
            <el-input v-model="updateForm.name" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="中文名称" prop="nameCH">
            <el-input v-model="updateForm.nameCH" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="属性类型" prop="typeId">
            <el-select v-model="updateForm.typeId" placeholder="请选择">
              <el-option
                v-for="item in bandData"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="类型" prop="type">
            <el-select v-model="updateForm.type" placeholder="请选择">
              <el-option
                v-for="item in leixngData"
                :key="item.type"
                :label="item.name"
                :value="item.type">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="SKU" prop="isSKU">
            <el-switch
              active-text="是"
              v-model="updateForm.isSKU"
              active-color="#13ce66"
              :active-value="1"
              inactive-text="否"
              :inactive-value="0">
            </el-switch>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="update">确 定</el-button>
          <el-button @click="updateFormFlag = false">取 消</el-button>
        </div>
      </el-dialog>
    </div>

    <!--属性值的展示-->
    <div>
      <el-dialog title="属性值展示" :visible.sync="shuxingFlag">
        <el-table
          :data="shuxingForm"
          style="width: 100%">
          <el-table-column
            prop="id"
            label="序号"
            width="180">
          </el-table-column>
          <el-table-column
            prop="value"
            label="属性英文名称"
            width="180">
          </el-table-column>

          <el-table-column
            prop="valueCH"
            label="属性中文名字"
          >
          </el-table-column>
          <el-table-column
            prop="id"
            label="操作">
            <template slot-scope="scope">
              <el-button type="primary" icon="el-icon-edit"   @click="toUpdateshuxingValue(scope.row.id)"></el-button>
              <el-button type="danger" icon="el-icon-delete"  @click="deleteShuxingValue(scope.row.id)"></el-button>
            </template>
          </el-table-column>
        </el-table>
        <el-button type="primary" @click="addValueFormFlag=true">新增</el-button>
      </el-dialog>
    </div>

    <!--属性值的的模板-->
    <div>
      <!--属性值的的模板-->
      <el-dialog title="属性值新增信息" :visible.sync="addValueFormFlag">
        <el-form :model="addValueForm"    label-width="80px">
          <el-form-item label="英文名称" prop="value">
            <el-input v-model="addValueForm.value" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="中文名称" prop="valueCH">
            <el-input v-model="addValueForm.valueCH" autocomplete="off" ></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="addValue">确 定</el-button>
          <el-button @click="addValueFormFlag = false">取 消</el-button>
        </div>
      </el-dialog>
    </div>
    <!--属性值修改模板-->
    <div>
      <!--属性值的的模板-->
      <el-dialog title="属性值修改信息" :visible.sync="updateValueFormFlag">
        <el-form :model="updateValueForm"    label-width="80px">
          <el-form-item label="英文名称" prop="value">
            <el-input v-model="updateValueForm.value" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="中文名称" prop="valueCH">
            <el-input v-model="updateValueForm.valueCH" autocomplete="off" ></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="updateValue">确 定</el-button>
          <el-button @click="updateValueFormFlag = false">取 消</el-button>
        </div>
      </el-dialog>
    </div>
  </div>

</template>

<script>
    export default {
      data(){return{
         arr:"",
        searchForm:{name:""},//条件查询数据
        //数据展示:
        shuxingData:[],
        //类型的数据
        typeData:[],
        leixngData:[{type:0,name:"下拉框"},{type:1,name:"单选框"},{type:2,name:"复选框"},{type:3,name:"输入框"}],
        count:0,
        sizes:[2,3,5,10],
        size:2,
        /* 新增模块的数据  */
        bandData:[],
        addFormFlag:false,
        addForm:{
          id:"",
          name:"",
          nameCH:"",
          typeId:"",
          type:"",
          isSKU:""
        },
        //修改的模板
        updateFormFlag:false,
        updateForm:{
          id:"",
          name:"",
          nameCH:"",
          typeId:"",
          type:"",
          isSKU:""
        },
        //属性值展示
        attId:"",
        shuxingFlag:false,
        shuxingForm:[]
        ,
        //属性值的新增
        addValueFormFlag:false,
        addValueForm:{
          value:"",
          valueCH:"",
        },
        //属性值的修改
        updateValueFormFlag:false,
        updateValueForm:{
        }
      }
      },methods:{
        updateValue(){
          var update=this.$qs.stringify(this.updateValueForm)
          this.$axios.post("http://192.168.1.43:8080/api/shuxing_value/update?"+update).then(res=>{
            // 把请求的数据  赋给全局
            this.updateValueFormFlag=false;
            this.toShuXing_value(this.attId);
          }).catch(err=>console.log(err));
        },
        toUpdateshuxingValue(id){
          this.updateValueFormFlag=true;
          this.$axios.get("http://192.168.1.43:8080/api/shuxing_value/queryById?id="+id+"").then(res=>{
            this.updateValueForm=res.data.data;
          }).catch(err=>console.log(err));
        },
        deleteShuxingValue(id){},
        addValue(){
          this.addValueForm.attId=this.attId;
          var add=this.$qs.stringify(this.addValueForm)
          console.log(add)
         this.$axios.post("http://192.168.1.43:8080/api/shuxing_value/add?"+add).then(res=>{
            // 把请求的数据  赋给全局
            this.addValueFormFlag=false;
            this.toShuXing_value(this.attId);
          }).catch(err=>console.log(err));
        },
        //属性值的展示
        toShuXing_value(id){
          this.attId=id;
          this.$axios.get("http://192.168.1.43:8080/api/shuxing_value/queryAll?pid="+id+"").then(res=>{
            this.shuxingForm=res.data.data;
          }).catch(err=>console.log(err));
          this.shuxingFlag=true;
        },
        //修改
        update(){
          console.log(this.updateForm)
          var update=this.$qs.stringify(this.updateForm)
          console.log(update)
          this.$axios.post("http://192.168.1.43:8080/api/shuxing/update?"+update).then(res=>{
            // 把请求的数据  赋给全局
            this.updateFormFlag=false;
            this.queryShuXing(1);
          }).catch(err=>console.log(err));
        },
        //回显
        toUpdateshuxing(id){
          this.updateFormFlag=true;
          this.$axios.get("http://192.168.1.43:8080/api/shuxing/queryById?id="+id+"").then(res=>{
            this.updateForm=res.data.data;
          }).catch(err=>console.log(err));
        },//逻辑删除
        deleteShuxing(id){
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.$axios.post("http://192.168.1.43:8080/api/shuxing/delete?id="+id+"").then(res=>{
              // 把请求的数据  赋给全局
              this.$message({
                type: 'success',
                message: '删除成功!'
              });
              this.queryShuXing(1);
            }).catch(err=>console.log(err));
          }).catch(() => {
            this.$message({
              type: 'info',
              message: '已取消删除'
            });
          });
        },
        //新增
        add(){
          console.log(this.addForm)
          var add=this.$qs.stringify(this.addForm)
          this.$axios.post("http://192.168.1.43:8080/api/shuxing/add?"+add).then(res=>{
            // 把请求的数据  赋给全局
            this.addFormFlag=false;
            this.queryShuXing(1);
          }).catch(err=>console.log(err));
        },
        handleCurrentChange(page){
          this.queryShuXing(page)
        },
        handleSizeChange(size){
          this.size=size;
          this.queryShuXing(1);
        },
        formaTypeId(row,column,value,index){
          for (let i = 0; i <this.typeData.length; i++) {
            if(value==this.typeData[i].id)
            {return this.typeData[i].name}
          }
        },
        SKU(row,column,value,index){
          return value==1?"是":"否"
        },
          queryShuXing(page){
            //参数格式化
            var searchStr=this.$qs.stringify(this.searchForm);
            console.log(searchStr);
            var url="http://192.168.1.43:8080/api/shuxing/queryAll?limit="+this.size+"&page="+page+"&"+searchStr;
            //发起请求
            this.$axios.get(url).then(res=>{
              this.shuxingData=res.data.data;
              this.count=res.data.count;
            }).catch(err=>console.log(err));
          },
        queryType(){
          this.$axios.get("http://192.168.1.43:8080/api/type/getData").then(res=>{
            this.typeData=res.data.data;
            for (let i = 0; i <res.data.data.length ; i++) {
              if(res.data.data[i].pid==0){
                this.arr3=res.data.data[i];
                this.diguiNode(res.data.data[i]);
                break;
              }
            }
          }).catch(err=>console.log(err));
        },
        diguiNode:function (node) {
          // 判断是否为父节点
          var bf=this.isParent(node);
          if(bf==true){
            for (let i = 0; i <this.typeData.length ; i++) {
              //判断是否为当前节点的子节点
              if(node.id==this.typeData[i].pid){
               this.diguiNode(this.typeData[i]);
              }
            }
          }
          if(bf==false){
            console.log(false)
            for (let i = 0; i <this.typeData.length ; i++) {
              if(node.pid==this.typeData[i].id){
                this.arr='{"id":'+node.id+',"name":'+'"分类/'+this.typeData[i].name+"/"+node.name+'"'+'}';
                this.bandData.push(JSON.parse(this.arr))
              }
            }
          }
          },
        isParent:function(node){// 判断是否为父节点  pid 有没有指向当前id
          for (let i = 0; i <this.typeData.length ; i++) {
            if(node.id==this.typeData[i].pid){
              return true;
            }
          }
          return false;
        }
      },
      created:function () {
        this.queryType();
        this.queryShuXing(1);
      }
    }
</script>

<style scoped>

</style>
