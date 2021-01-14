
<template>
  <div>
    <h1>商品分类管理</h1>
    <el-tree
      :data="data"
      show-checkbox
      node-key="id"
      default-expand-all
      :expand-on-click-node="false">
      <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
            <el-button
              size="mini"
              type="text"
              icon="el-icon-edit"
              circle @click="() => toUpdate(data)"></el-button>
          <el-button
            type="text"
            size="mini"
            @click="() => append(data)">
            新增
          </el-button>
          <el-button
            type="text"
            size="mini"
            icon="el-icon-delete"
            @click="() => remove(node, data)">
          </el-button>
        </span>
      </span>
    </el-tree>
    <!--新增弹窗-->
    <div>
    <el-dialog title="录入" :visible.sync="addFormFlag">
      <el-form  ref="addTypeForm"   label-width="80px">
      名字<el-input
        placeholder="请输入内容"
        prefix-icon="el-icon-search"
        v-model="addTypeForm.name">
      </el-input>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="addForm">确 定</el-button>
        <el-button @click="addFormFlag = false">取 消</el-button>
      </div>
    </el-dialog>
    </div>
    <!--修改的弹窗-->
    <div>
      <el-dialog title="修改" :visible.sync="updateFormFlag">
        <el-form  ref="updateTypeForm"   label-width="80px">
          名字<el-input
          placeholder="请输入内容"
          prefix-icon="el-icon-search"
          v-model="updateTypeForm.name">
        </el-input>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="updateForm">确 定</el-button>
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
        data:[],//tree 的数据
        ajaxData:[],// 远程请求的数据
        jsonStr:"", //递归拼接处理
        defaultProps:{},


      //新增的模板
        addFormFlag:false,
        addTypeForm:{name:"",pid:""},

        //修改的模板
        updateFormFlag:false,
        updateTypeForm:{name:"",id:""}
      }
    }
    ,methods:{
      chandleData:function(){ //ajaxData  处理成 data   //转换数据
        //找到顶层节点的数据
        for (let i = 0; i <this.ajaxData.length ; i++) {
          if(this.ajaxData[i].pid==0){
            this.diguiNode(this.ajaxData[i]);
            console.log(this.diguiNode)
            break;
          }
        }
        //将字符串 转为json对象
        this.data.push(JSON.parse(this.jsonStr));
      },  //  id  name  pid        id  name children []
      diguiNode:function (node) {
        // 判断是否为父节点
        var bf=this.isParent(node);
        if(bf==true){
          //{"id":1,"label":"分类",}
          //{"id":1,"label":"分类","children":[]}
          this.jsonStr+='{"id":'+node.id+',"label":"'+node.name+'","children":[';
          //拼接子节点
          //查找此节点的子节点
          let count=0
          for (let i = 0; i <this.ajaxData.length ; i++) {
            //判断是否为当前节点的子节点
            if(node.id==this.ajaxData[i].pid){
              count++;
              this.diguiNode(this.ajaxData[i]);
              this.jsonStr+=",";
            }
          }
          //处理多余的,号
          if(count!=0){
            this.jsonStr=this.jsonStr.substr(0,this.jsonStr.length-1);
          }
          //拼完整
          this.jsonStr+=']}';
        }else{
          this.jsonStr+='{"id":'+node.id+',"label":"'+node.name+'"}';
        }

      }
      ,isParent:function(node){ // 判断是否为父节点  pid 有没有指向当前id
        for (let i = 0; i <this.ajaxData.length ; i++) {
          if(node.id==this.ajaxData[i].pid){
            return true;
          }
        }
        return false;
      }
      ,
      toUpdate(data)
      {
        this.updateFormFlag=true;
        this.updateTypeForm.name=data.label
        this.updateTypeForm.id=data.id;
        console.log(data)
      },
      append(data) {
       this.addFormFlag=true;
       console.log(data)
       this.addTypeForm.pid=data.id;

      },
      remove(node, data) {
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          let tthis=this;
      if(node.childNodes.length==""){
         this.$axios.post("http://192.168.1.43:8080/api/type/update?id="+data.id+"&isDel="+1+"").then(res=>{
            // 把请求的数据  赋给全局
            tthis.$message({
              type: 'success',
              message: '删除成功!'
            });
        location.reload();
            console.log(res)
          }).catch(err=>console.log(err));
      }else
        {
          tthis.$message({
            type: 'warning',
            message: '分级下面有内容,不准删除!'
          });
        }
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
      },
      addForm(){
        var add=this.$qs.stringify(this.addTypeForm)
        console.log(add)
        let tthis=this;
        this.$axios.post("http://192.168.1.43:8080/api/type/add?"+add).then(res=>{
           // 把请求的数据  赋给全局
          tthis.addFormFlag=false;
          debugger
          location.reload();
          console.log(res)
        }).catch(err=>console.log(err));
      },
      updateForm(){
        let update=this.$qs.stringify(this.updateTypeForm)
        console.log(update)
        var tthis=this;
        this.$axios.post("http://192.168.1.43:8080/api/type/update?"+update).then(res=>{
          // 把请求的数据  赋给全局
          tthis.updateFormFlag=false;
          location.reload();
          console.log(res)
        }).catch(err=>console.log(err));
      },
      query(){
        this.$axios.get("http://192.168.1.43:8080/api/type/getData").then(res=>{
          this.ajaxData = res.data.data// 把请求的数据  赋给全局
          this.chandleData();
        }).catch(err=>console.log(err));
      }
    }
    ,created:function(){
      //远程请求数据
      this.query()
      console.log(this.data.length)
    },
    watch:{
      ajaxData:{//深度监听，可监听到对象、数组的变化
    handler(val, oldVal){
       console.log(val.length)
    },
    deep:true //true 深度监听
  }

    }
  }
</script>

<style scoped>

</style>
