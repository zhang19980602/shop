<template>

  <el-button @click="dianji()"> 点击</el-button>
</template>

<script>
    export default {

      methods:{
        dianji:function(){
          var arr=["鸿蒙","shanchu"]
          var arr1=["128GB","111"]
          var arr4=[]
          arr4.push(arr1)
          arr4.push(arr)
          var arr3=this.discarts(arr4)
          console.log(arr3)
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
      }
    }
</script>

<style scoped>

</style>
