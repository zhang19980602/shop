<template>
  <div>
    <div id="searchDiv" align="center">
      <el-form :inline="true" :model="searchForm" class="demo-form-inline">
        <el-form-item label="名称">
          <el-input v-model="searchForm.name" placeholder="名称"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="queryShopData(1)">查询</el-button>
          <router-link :to="{path:'/shopadd'}">
            <el-button type="primary" icon="el-icon-edit" circle></el-button>
          </router-link>
        </el-form-item>
      </el-form>
    </div>
    <div id="carTable">
      <el-table
        :data="shopData"
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
          prop="title"
          label="副标题"
        >
        </el-table-column>
        <el-table-column
          prop="imgPath"
          label="主图">
          <!-- 按文本处理   :formatter="formatImg"    -->
          <!-- 模板处理  html  -->
          <template slot-scope="scope">
            <!-- <img src="192.168.1.43:8080/imgFiless/f86a6cd6-a0e3-47a6-ba03-62a68ff41c99.gif"/>-->
            <img width="50px" :src="scope.row.imgPath"/>
          </template>
        </el-table-column>
        <el-table-column
          prop="productdecs"
          label="内容">
        </el-table-column>
        <el-table-column
          prop="price"
          label="价格">
        </el-table-column>
        <el-table-column
          prop="typeId"
          label="类型"
          :formatter="typeIdformatter">
        </el-table-column>
        <el-table-column
          prop="stocks"
          label="库存">
        </el-table-column>
        <el-table-column
          prop="sortNum"
          label="排序">
        </el-table-column>
        <el-table-column
          prop="id"
          label="操作"
         width="300">
          <template slot-scope="scope">
            <el-button type="primary"  @click="toUpdateShop(scope.row.id)">修改主表</el-button>
           <el-button type="primary" icon="el-icon-edit"   @click="toUpdateShopvalues(scope.row.id,scope.row.typeId)"></el-button>
            <el-button type="danger" icon="el-icon-delete"  @click="deleteShop(scope.row.id)"></el-button>
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

    <div>
      <el-dialog title="商品属性修改" :visible.sync="addFalg" width="800px">
      <el-form  v-model="shopAddForm" label-width="80px">
        <el-form-item label="商品名称">
          <el-input v-model="shopAddForm.name"></el-input>
        </el-form-item>
        <el-form-item label="副标题">
          <el-input v-model="shopAddForm.title"></el-input>
        </el-form-item>
        <el-form-item label="商品品牌">
          <el-select v-model="shopAddForm.bandId" placeholder="请选择">
            <el-option
              v-for="item in bandData"
              :key="item.id"
              :label="item.name"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="商品介绍">
          <el-input
            type="textarea"
            placeholder="请输入内容"
            v-model="shopAddForm.productdecs"
            maxlength="50"
            show-word-limit
          >
          </el-input>
        </el-form-item>
        <el-form-item label="商品价格">
          <el-input v-model="shopAddForm.price"></el-input>
        </el-form-item>
        <el-form-item label="商品库存">
          <el-input v-model="shopAddForm.stocks"></el-input>
        </el-form-item>
        <el-form-item label="商品排序">
          <el-input v-model="shopAddForm.sortNum"></el-input>
        </el-form-item>
        <el-form-item label="图片" prop="imgpath">
          <div> <img :src="shopAddForm.imgPath" width="80"></div>
          <el-upload
            class="upload-demo"
            action="http://192.168.1.43:8080/api/pinpai/upload"
            :on-success="imgCallBack"
            name="img"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>
        </el-form-item>
        <el-button style="margin-top: 12px;" @click="tijiaozhubiao()">提交</el-button>
      </el-form>
      </el-dialog>
    </div>


    <div >
      <el-dialog title="商品属性修改" :visible.sync="TypeFlag" width="800px">
        <div align="center">
          <el-form  v-model="shopTypeForm">
      <el-select v-model="shopTypeForm.typeId" placeholder="请选择">
        <el-option
          v-for="item in typeData1"
          :key="item.id"
          :label="item.name"
          :value="item.id"
        >
        </el-option>
      </el-select>

          <el-form-item label="商品规格" v-if="shuxingData1.length>0"><br>
            <el-form-item v-for="a in shuxingData1" :key="a.id" :label="a.nameCH">
              <!--复选框-->
             <el-checkbox-group v-if="a.type==2" v-model="a.cks" @change="skuChange1">
                <el-checkbox-button v-for="b in a.values" :key="b.id" :label="b.valueCH"></el-checkbox-button>
              </el-checkbox-group>
            </el-form-item>
          </el-form-item>

            <el-table
              v-if="tableShow"
              :data="tableData"
              style="width: 100%">

              <el-table-column v-for="c in cols" :key="c.id" :label="c.nameCH" :prop="c.name">
              </el-table-column>

              <el-table-column
                label="库存"
                width="180">

                <template slot-scope="scope">
                  <el-input v-model="scope.row.kucun"/>
                </template>

              </el-table-column>
              <el-table-column
                label="价格"
                width="180">
                <template slot-scope="scope">
                  <el-input v-model="scope.row.price"/>
                </template>
              </el-table-column>
            </el-table>
            <el-form-item label="商品参数" v-if="shuxingData2.length>0" >
              <el-form-item v-for="a  in shuxingData2" :key="a.id" :label="a.nameCH">
                <!--下拉框-->
                <el-select  v-if="a.type==0"  placeholder="请选择" v-model="a.cks1">
                  <el-option v-for="b in a.values" :key="b.id"  :label="b.valueCH" :value="b.valueCH"></el-option>
                </el-select>
                <!--单选框-->
                <el-radio-group v-if="a.type==1" v-model="a.cks1">
                  <el-radio-button v-for="b in a.values" :key="b.id" :label="b.valueCH"></el-radio-button>
                </el-radio-group>
                <!--复选框-->
                <el-checkbox-group v-if="a.type==2" v-model="a.cks1">
                  <el-checkbox-button v-for="b in a.values" :key="b.id" :label="b.valueCH" name="type"></el-checkbox-button>
                </el-checkbox-group>
                <!--输入框-->
                <div v-if="a.type==3">
                  <el-input v-model="a.cks1" ></el-input>
                </div>
              </el-form-item>
            </el-form-item>
            <el-button v-if="tableShow" style="margin-top: 12px;" @click="tijiao()">提交</el-button>
          </el-form>
        </div>
      </el-dialog>
    </div>
  </div>
</template>

<script>
    import $ from 'jquery'
    export default {
      data(){
      return{
        searchForm:{name:""},
        /*  表格相关的数据  */
        shopData:[],
        /* 分页相关的数据  */
        count:0,
        sizes:[2,3,5,10],
        size:2,
        //类型
        typeData:[],
        typeData1:[],
        TypeFlag:false,
        //SKU回显
        shopTypeForm:{typeId:""},
        //SKU
        shuxingData1:[],
        //非SKU
        shuxingData2:[],
        tableShow:true,
        tableData:[],
        cols:[],
        proId:"",
        //商品主表修改
        addFalg:false,
        shopAddForm:{},
        bandData:[]
      }
      },methods:{
        toUpdateShop(id){
         this.addFalg=true
          this.$axios.get("http://192.168.1.43:8080/api/shop/queryById?id="+id+"").then(res=>{
            console.log(res.data.data)
            this.shopAddForm=res.data.data;
          }).catch(err=>console.log(err));
        },
        tijiaozhubiao(){
          let update=this.$qs.stringify(this.shopAddForm)
          this.$axios.post("http://192.168.1.43:8080/api/shop/update",update).then(res=>{
            this.$message({
              type: 'success',
              message: '修改成功!'
            });
            this.addFalg=false;
            this.queryShopData(1);
          }).catch(err=>console.log(err));
        },
        tijiao(){
          //非SKU
          let list=[];
          for (let i = 0; i <this.shuxingData2.length; i++) {
            let arr=this.shuxingData2[i].name
            let arr1=this.shuxingData2[i].cks1;
            let ass='{'+'"'+arr+'"'+':'+'"'+arr1+'"'+'}'
            let saveAddvalues={proId:this.proId,attrData:ass}
            list.push(saveAddvalues)
          }
          //SKU
          for (let i = 0; i <this.tableData.length; i++) {
            let saveAddvalues={proId:this.proId,price:this.tableData[i].price,storcks:this.tableData[i].kucun}
            delete this.tableData[i].price;
            delete this.tableData[i].kucun;
            saveAddvalues.attrData=JSON.stringify(this.tableData[i]);
            list.push(saveAddvalues)
          }
          var add1={attr:JSON.stringify(list),id:this.proId,typeId:this.shopTypeForm.typeId}
         var add=this.$qs.stringify(add1)
          console.log(JSON.stringify(list))
         this.$axios.post("http://192.168.1.43:8080/api/shop/updateShopvalues",add).then(res=>{
           this.$message({
             type: 'success',
             message: '修改成功!'
           });
            this.TypeFlag=false
           this.queryShopData(1)
          }).catch(err=>console.log(err));

        },
        skuChange(SKU){
          //清空动态列头
          this.cols=[];
          this.tableData=[];
          //声明笛卡尔积的参数
          let arr=[];
          //判断是否要生成笛卡尔积
          let flag=true;
          for (let i = 0; i <this.shuxingData1.length ; i++) {
            //添加动态列头名称
            this.cols.push({"id":this.shuxingData1[i].id,"nameCH":this.shuxingData1[i].nameCH,"name":this.shuxingData1[i].name});
            //添加笛卡尔积参数
            //判断当前sku属性 是否被选中
            if(this.shuxingData1[i].cks.length==0){
              flag=false;
              break;
            }
          }
          if(flag==true){
            for (let i = 0; i <this.shuxingData1.length; i++) {
              let arr1=[];
             for (let j = 0; j <this.shuxingData1[i].cks.length; j++) {
                       if(arr1.indexOf(this.shuxingData1[i].cks[j])==-1){
                         arr1.push(this.shuxingData1[i].cks[j])
                       }
              }
              arr.push(arr1)
            }
            let res=this.discarts(arr);


            //遍历结果集   ["","",""]
            for (let i = 0; i <res.length ; i++) {
              //得到数据
              let valuesAttr=res[i];
              let  tableValue={};
              for (let j = 0; j < valuesAttr.length; j++) {
                let key=this.cols[j].name;
                tableValue[key]=valuesAttr[j];
              }
                tableValue.price=SKU[i].price
                tableValue.kucun=SKU[i].storcks
              this.tableData.push(tableValue);
            }
            console.log(this.tableData)
          }
          this.tableShow=flag;
        },
        typeIdformatter(row,column,value,index){
          for (let i = 0; i <this.typeData1.length ; i++) {
            if(value==this.typeData1[i].id){
              return this.typeData1[i].name
            }
          }
        },
        toUpdateShopvalues(id,typeId){
          this.proId=id;
          this.count=1
          this.TypeFlag=true
          this.shopTypeForm.typeId=typeId
          this.queryTypeById(id,typeId)
        },
        queryTypeById(id,typeId){
          this.shuxingData1=[];
          this.shuxingData2=[];
          let SKUshuxing=[]
          let SKU=[]
          let feiSKU=[]
          $.get({
            url:"http://192.168.1.43:8080/api/shop/queryByTypeId",
            data:{typeId:id},
            async:false,
            success:function(res){
              console.log(res)
              for (let i = 0; i <res.data.length; i++) {
                if(res.data[i].price!=null)
                {
                  SKU.push(res.data[i]);
                  SKUshuxing.push(JSON.parse(res.data[i].attrData))
                }else{
                  feiSKU.push(JSON.parse(res.data[i].attrData));
                }
              }
            }
          })

          this.$axios.get("http://192.168.1.43:8080/api/shuxing/queryByTypeId?typeId="+typeId+"").then(res=>{
              let shopValues=res.data.data;
            for (let i = 0; i <shopValues.length; i++) {
              if(shopValues[i].isSKU==1){
                shopValues[i].cks=[];
                for (let j = 0; j <SKUshuxing.length ; j++) {
                  if(shopValues[i].cks.indexOf(SKUshuxing[j][shopValues[i].name])==-1){
                    shopValues[i].cks.push(SKUshuxing[j][shopValues[i].name])
                  }
                }

               $.get({
                 url:"http://192.168.1.43:8080/api/shuxing_value/queryAll",
                 data:{pid:shopValues[i].id},
                 async:false,
                 success:function(res){

                   shopValues[i].values=res.data;
                 }
               })
                this.shuxingData1.push(shopValues[i])
              }else if(shopValues[i].isSKU==0){
                $.get({
                  url:"http://192.168.1.43:8080/api/shuxing_value/queryAll",
                  data:{pid:shopValues[i].id},
                  async:false,
                  success:function(res){
                    shopValues[i].values=res.data;
                    shopValues[i].cks1=""
                  }
                })
                this.shuxingData2.push(shopValues[i])
                console.log(this.shuxingData2)
                let hh=""
               for (let j = 0; j <feiSKU.length ; j++) {
                  if(feiSKU[j][shopValues[i].name]!=undefined){
                      hh=feiSKU[j][shopValues[i].name]
                      this.shuxingData2[j].cks1=hh
                  }

                }
              }
            }
            this.skuChange(SKU)
          }).catch(err=>console.log(err))

        },
        deleteShop(id){
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {

            this.$axios.post("http://192.168.1.43:8080/api/shop/delete?id="+id+"").then(res=>{
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
        handleCurrentChange(page){
          this.queryShopData(page);
        },
        handleSizeChange(size){
          this.size=size;
          this.queryShopData(1);
        },
        queryShopData:function(page){
          //参数格式化
          var searchStr=this.$qs.stringify(this.searchForm);
          var url="http://192.168.1.43:8080/api/shop/queryAll?limit="+this.size+"&page="+page+"&"+searchStr;
          //发起请求
          this.$axios.get(url).then(res=>{
            this.shopData=res.data.data;
            this.count=res.data.count;
          }).catch(err=>console.log(err));
        },
        queryType(){
          this.$axios.get("http://192.168.1.43:8080/api/type/getData").then(res=>{
            this.typeData=res.data.data;
            for (let i = 0; i <res.data.data.length ; i++) {
              if(res.data.data[i].pid==0){
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
            this.typeData1.push(node)
          }
        },
        isParent:function(node){// 判断是否为父节点  pid 有没有指向当前id
          for (let i = 0; i <this.typeData.length ; i++) {
            if(node.id==this.typeData[i].pid){
              return true;
            }
          }
          return false;
        },
        discarts:function() {
          //笛卡尔积
          var twodDscartes = function (a, b) {
            var ret = [];
            for (var i = 0; i < a.length; i++) {
              for (var j = 0; j < b.length; j++) {
                ret.push(ft(a[i], b[j]));
              }
            }
            return ret;
          }
          var ft = function (a, b) {
            if (!(a instanceof Array))
              a = [a];
            var ret = a.slice(0);
            ret.push(b);
            return ret;
          }
          //多个一起做笛卡尔积
          return (function (data) {
            var len = data.length;
            if (len == 0)
              return [];
            else if (len == 1)
              return data[0];
            else {
              var r = data[0];
              for (var i = 1; i < len; i++) {
                r = twodDscartes(r, data[i]);
              }
              return r;
            }
          })(arguments.length > 1 ? arguments : arguments[0]);
        },
        queryShuXing(typeId){
          console.log(this.count)
          if(this.count==0){
          this.$axios.get("http://192.168.1.43:8080/api/shuxing/queryByTypeId?typeId="+typeId+"").then(res=>{
            this.shuxingData1=[];
            this.shuxingData2=[];
            let shuxingData=res.data.data;
            for (let i = 0; i <shuxingData.length; i++) {
              if(shuxingData[i].isSKU==1){
                if(shuxingData[i].type!=3){
                  this.$axios.get("http://192.168.1.43:8080/api/shuxing_value/queryAll?pid="+shuxingData[i].id+"").then(res=>{
                    shuxingData[i].values=res.data.data;
                    shuxingData[i].cks=[];
                    this.shuxingData1.push(shuxingData[i])
                  }).catch(err=>console.log(err));
                }else{
                  shuxingData[i].cks=[];
                  this.shuxingData1.push(shuxingData[i])
                }
              }else if(shuxingData[i].isSKU==0){
                if(shuxingData[i].type!=3){
                  this.$axios.get("http://192.168.1.43:8080/api/shuxing_value/queryAll?pid="+shuxingData[i].id+"").then(res=>{
                    shuxingData[i].values=res.data.data;
                    shuxingData[i].cks1="";
                    if(shuxingData[i].type==2){
                      shuxingData[i].cks1=[];
                    }
                    this.shuxingData2.push(shuxingData[i])
                  }).catch(err=>console.log(err));
                }else{
                  shuxingData[i].cks1="";
                  this.shuxingData2.push(shuxingData[i])
                }

              }
            }
          }).catch(err=>console.log(err));}
        },
        pinpai(){
          this.$axios.get("http://192.168.1.43:8080/api/pinpai/queryAll?limit="+1000+"&page="+1+"&name="+""+"").then(res=>{
            this.bandData=res.data.data;
          }).catch(err=>console.log(err));
        },
        /*图片上传*/
        imgCallBack:function(response, file, fileList){ //图片上传的回调函数
          // 赋值
          this.shopAddForm.imgPath=response.data;
        },
        skuChange1(){
          //清空动态列头
          this.cols=[];
          this.tableData=[];

          //声明笛卡尔积的参数
          let arr=[];
          //判断是否要生成笛卡尔积
          let flag=true;
          for (let i = 0; i <this.shuxingData1.length ; i++) {
            //添加动态列头名称
            this.cols.push({"id":this.shuxingData1[i].id,"nameCH":this.shuxingData1[i].nameCH,"name":this.shuxingData1[i].name});
            //添加笛卡尔积参数
            //判断当前sku属性 是否被选中
            if(this.shuxingData1[i].cks.length==0){
              flag=false;
              break;
            }
          }
          if(flag==true){
            for (let i = 0; i <this.shuxingData1.length; i++) {
              arr.push(this.shuxingData1[i].cks)
            }
            let res=this.discarts(arr);

            //遍历结果集   ["","",""]
            for (let i = 0; i <res.length ; i++) {
              //得到数据
              let valuesAttr=res[i];
              let  tableValue={};
              for (let j = 0; j < valuesAttr.length; j++) {
                let key=this.cols[j].name;
                tableValue[key]=valuesAttr[j];
                console.log(key);
              }
              this.tableData.push(tableValue);
            }
          }
          this.tableShow=flag;
        }


      },
      created:function () {
        this.queryType();
        this.pinpai()
        this.queryShopData(1);
      },watch:{
        shopTypeForm:{
          handler:function(val,oldval){
            this.tableShow=false
            this.queryShuXing(val.typeId)
            this.count=0
          },
          deep:true//对象内部的属性监听，也叫深度监听
        }
      }


    }
</script>

<style scoped>

</style>
