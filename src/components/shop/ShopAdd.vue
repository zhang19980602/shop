<template>
  <div align="center">
  <div class="container" style="width:800px" >
    <el-steps :active="active" finish-status="success">
      <el-step title="填写商品信息">
      </el-step>
      <el-step title="填写商品属性"></el-step>
      <el-step title="步骤 3"></el-step>
      <el-step title="步骤 4"></el-step>
    </el-steps>

    <!--商品新增-->
    <div>
      <el-form  v-model="shopAddForm" label-width="80px" v-if="active==0?shopAddFormflag=true:shopAddFormflag=false">
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

      </el-form>
    </div>

    <div>
      <el-form  v-model="shopTypeForm" label-width="80px" v-if="active==1?shopTypeFormflag=true:shopTypeFormflag=false">
        <el-form-item label="属性类型">
          <el-select v-model="shopTypeForm.typeId" placeholder="请选择">
            <el-option
              v-for="item in typeData1"
              :key="item.id"
              :label="item.name"
              :value="item.id"
            >
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="商品规格" v-if="shuxingData1.length>0">
          <el-form-item v-for="a in shuxingData1" :key="a.id" :label="a.nameCH">
          <!--复选框-->
              <el-checkbox-group v-if="a.type==2" v-model="a.cks" @change="skuChange">
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
    <el-button style="margin-top: 12px;" v-if="active!=0" @click="after">上一步</el-button>
    <el-button style="margin-top: 12px;" @click="next">下一步</el-button>
  </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        active: 0,
        xialai:[],
        aa:[],
        danxuan:[],
        //商品新增信息
        shopAddFormflag:true,
        shopAddForm:{},
        bandData:[{id:1,name:"1"},{id:2,name:"2"}],
        //商品类型信息:
        shopTypeFormflag:false,
        shopTypeForm1:{},
        shopTypeForm:{typeId:""},
        typeData:[{id:1,name:"上衣"},{id:2,name:"手机"}],
        typeData1:[],
        //SKU
        shuxingData1:[],
        //非SKU
        shuxingData2:[],
        shuxingzhiDate:[],
        tableShow:false,
        cols:[],//表动态列头
        tableData:[]
      };
    },
    methods: {
      tijiao(){
        //非SKU
        let list=[];
        for (let i = 0; i <this.shuxingData2.length; i++) {
          let arr=this.shuxingData2[i].name
          let arr1=this.shuxingData2[i].cks1;
          let ass='{'+'"'+arr+'"'+':'+'"'+arr1+'"'+'}'
          let saveAddvalues={proId:this.shopTypeForm.typeId,attrData:ass}
          list.push(saveAddvalues)
        }
        //SKU
        for (let i = 0; i <this.tableData.length; i++) {
          let saveAddvalues={proId:this.shopTypeForm.typeId,price:this.tableData[i].price,storcks:this.tableData[i].kucun}
          delete this.tableData[i].price;
          delete this.tableData[i].kucun;
          saveAddvalues.attrData=JSON.stringify(this.tableData[i]);
          list.push(saveAddvalues)
        }
        this.shopAddForm.typeId=this.shopTypeForm.typeId
        this.shopAddForm.attrData=JSON.stringify(list)
        var add1=this.$qs.stringify(this.shopAddForm)
        console.log(JSON.stringify(list))
       this.$axios.post("http://192.168.1.43:8080/api/shop/add",add1).then(res=>{
            console.log("成功")
            console.log(res)
        }).catch(err=>console.log(err));
      },
      /*图片上传*/
      imgCallBack:function(response, file, fileList){ //图片上传的回调函数
        // 赋值
        console.log(response)
        this.shopAddForm.imgPath=response.data;
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
      }
      ,
      skuChange(){
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
      },
      after(){
        if (this.active-- < 0) this.active = this.active-1;
      },
      next() {
        if (this.active++ > 3) this.active = 1;
      },
      querytype(){
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
      queryShuXing(typeId){
        this.$axios.get("http://192.168.1.43:8080/api/shuxing/queryByTypeId?typeId="+typeId+"").then(res=>{
          this.shuxingData1=[];
          this.shuxingData2=[];
          let shuxingData=res.data.data;
          for (let i = 0; i <shuxingData.length; i++) {
             if(shuxingData[i].isSKU==1){
               if(shuxingData[i].type!=3){
                 this.$axios.get("http://192.168.1.43:8080/api/shuxing_value/queryAll?pid="+shuxingData[i].id+"").then(res=>{
                   console.log(res.data.data)
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
                   console.log(shuxingData[i].values)
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
          console.log(this.shuxingData2)
        }).catch(err=>console.log(err));
      },
      pinpai(){
        this.$axios.get("http://192.168.1.43:8080/api/pinpai/queryAll?limit="+1000+"&page="+1+"&name="+""+"").then(res=>{
          console.log(res)
        this.bandData=res.data.data;
        }).catch(err=>console.log(err));
      }
    },created:function () {
      this.pinpai()
      this.querytype()
    },watch:{
      shopTypeForm:{
        handler:function(val,oldval){
          this.tableShow=false
          console.log("-------------------------------")
          this.queryShuXing(val.typeId)
        },
        deep:true//对象内部的属性监听，也叫深度监听
      }
    }
  }
</script>

<style scoped>
  .handle-box {
    margin-bottom: 20px;
  }

  .handle-select {
    width: 120px;
  }

  .handle-input {
    width: 300px;
    display: inline-block;
  }
  .table {
    width: 100%;
    font-size: 14px;
  }
  .red {
    color: #00ffff;
  }
  .mr10 {
    margin-right: 10px;
  }
  .table-td-thumb {
    display: block;
    margin: auto;
    width: 40px;
    height: 40px;
  }
</style>
