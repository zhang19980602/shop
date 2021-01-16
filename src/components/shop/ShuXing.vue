<template>
  <div><h1>属性展示</h1>
    <div id="searchDiv" align="center">

    <el-form :inline="true" :model="searchForm" class="demo-form-inline">

      <el-form-item label="名称">
        <el-input v-model="searchForm.name" placeholder="名称"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="queryShuXing(1)">查询</el-button>
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
          prop="id"
          label="操作">
          <template slot-scope="scope">
            <!--<el-button type="primary" icon="el-icon-edit"   @click="toUpdatePinpai(scope.row.id)"></el-button>
            <el-button type="danger" icon="el-icon-delete"  @click="deletePinpai(scope.row.id)"></el-button>-->
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
    </div></div>
</template>

<script>
    export default {
      data(){return{
        searchForm:{name:""},//条件查询数据
        //数据展示:
        shuxingData:[],
        typeData:[],
        count:0,
        sizes:[2,3,5,10],
        size:2,
      }
      },methods:{
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
          queryShuXing(page){
            //参数格式化
            var searchStr=this.$qs.stringify(this.searchForm);
            console.log(searchStr);
            var url="http://192.168.1.43:8080/api/shuxing/queryAll?limit="+this.size+"&page="+page+"&"+searchStr;
            console.log(url);
            //发起请求
            this.$axios.get(url).then(res=>{
              console.log(res)
              this.shuxingData=res.data.data;
              this.count=res.data.count;
            }).catch(err=>console.log(err));
          },
        queryType(){
          this.$axios.get("http://192.168.1.43:8080/api/type/getData").then(res=>{
            this.typeData=res.data.data;
            console.log(res)
          }).catch(err=>console.log(err));
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
