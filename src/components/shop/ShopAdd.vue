<template>
  <div>
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
          <el-form-item v-for="a in  shuxingData1" :key="a.id" :label="a.nameCH">
            <!--下拉框-->
              <el-select  v-if="a.type==0" placeholder="请选择" v-model="xialai">
                <el-option v-for="b in a.values" :key="b.id"  :label="b.valueCH" :value="b.id"></el-option>
              </el-select>
          <!--单选框-->
            <el-radio-group v-if="a.type==1" v-model="danxuan">
              <el-radio-button v-for="b in a.values" :key="b.id" :label="b.valueCH"></el-radio-button>
            </el-radio-group>
          <!--复选框-->
              <el-checkbox-group v-if="a.type==2" v-model="aa">
                <el-checkbox-button v-for="b in a.values" :key="b.id" :label="b.valueCH" name="type"></el-checkbox-button>
              </el-checkbox-group>
          <!--输入框-->
            <div v-if="a.type==3">
              <el-input>11111</el-input>
            </div>
          </el-form-item>
        </el-form-item>

        <el-form-item label="商品参数" v-if="shuxingData2.length>0" >
          <el-form-item v-for="a  in shuxingData2" :key="a.id" :label="a.nameCH">
            <!--下拉框-->
            <el-select  v-if="a.type==0" placeholder="请选择" v-model="xialai">
              <el-option v-for="b in a.values" :key="b.id"  :label="b.valueCH" :value="b.id"></el-option>
            </el-select>
            <!--单选框-->
            <el-radio-group v-if="a.type==1" v-model="danxuan">
              <el-radio-button v-for="b in a.values" :key="b.id" :label="b.valueCH"></el-radio-button>
            </el-radio-group>
            <!--复选框-->
            <el-checkbox-group v-if="a.type==2" v-model="aa">
              <el-checkbox-button v-for="b in a.values" :key="b.id" :label="b.valueCH" name="type"></el-checkbox-button>
            </el-checkbox-group>
            <!--输入框-->
            <div v-if="a.type==3">
            <el-input ></el-input>
          </div>

          </el-form-item>
        </el-form-item>
      </el-form>
    </div>
    <el-button style="margin-top: 12px;" v-if="active!=0" @click="after">上一步</el-button>
    <el-button style="margin-top: 12px;" @click="next">下一步</el-button>
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
        shuxingData:[],
        //SKU
        shuxingData1:[],
        //非SKU
        shuxingData2:[],
        shuxingzhiDate:[]
      };
    },
    methods: {
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
          this.shuxingData=res.data.data;
          for (let i = 0; i <this.shuxingData.length; i++) {
             if(this.shuxingData[i].isSKU==1){
               if(this.shuxingData[i].type!=3){
                 this.$axios.get("http://192.168.1.43:8080/api/shuxing_value/queryAll?pid="+this.shuxingData[i].id+"").then(res=>{
                   this.shuxingData[i].values=res.data.data;
                   this.shuxingData1.push(this.shuxingData[i])
                 }).catch(err=>console.log(err));
               }else{
                 this.shuxingData1.push(this.shuxingData[i])
               }
             }else if(this.shuxingData[i].isSKU==0){
               if(this.shuxingData[i].type!=3){
                 this.$axios.get("http://192.168.1.43:8080/api/shuxing_value/queryAll?pid="+this.shuxingData[i].id+"").then(res=>{
                   this.shuxingData[i].values=res.data.data;
                   this.shuxingData2.push(this.shuxingData[i])
                 }).catch(err=>console.log(err));
               }else{
                 this.shuxingData2.push(this.shuxingData[i])
               }

             }
          }
          console.log(this.shuxingData2)
        }).catch(err=>console.log(err));
      },
    },created:function () {
      this.querytype()
    },watch:{
      shopTypeForm:{
        handler:function(val,oldval){

          console.log("-------------------------------")
          this.queryShuXing(val.typeId)
        },
        deep:true//对象内部的属性监听，也叫深度监听
      }
    }
  }
</script>

<style scoped>

</style>
