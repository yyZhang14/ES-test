<template>
    <div>
        <el-row class="title" style="color: #606060;">
          <img
          src="../../public/assets/img/back.png"
          class="img_style"
          
          @click="toUrl()"
         />
            <p>Detail information of {{dataList.NONCODE_TRANSCRIPT_ID}}</p>

        </el-row>

        <div class="files">
          <h3 class="top">LncRNA Information</h3>
          <div class="content" style="height:40%">
            <el-form label-position="left" inline class="demo-table-expand" >
              <el-form-item label="Gene Name:">
                <span>{{ dataList.Name}}</span>
              </el-form-item>
              <el-form-item label="Organism:">
                <span>{{ dataList.Organism }}</span>
              </el-form-item>
              <el-form-item label="NONCODE Gene ID:">
                <span @click="toUrl_DNA(dataList.NONCODE_Gene_ID)" class="hand">{{ dataList.NONCODE_Gene_ID }}</span>
              </el-form-item>
              <el-form-item label="NONCODE Transcript ID:">  
                <span @click="toUrl_RNA(dataList.NONCODE_TRANSCRIPT_ID)" class="hand">{{dataList.NONCODE_TRANSCRIPT_ID}}</span>
              </el-form-item>
              <el-form-item label="Length:">
                <span>{{ dataList.length}}</span>
              </el-form-item>
              <el-form-item label="Sequence:" >
                    <span class="newlist" v-html="this.showData" @click="kzClick($event)">
                    </span>   
              </el-form-item>
              

            </el-form>
          </div>

        </div>
        <div class="files">
          <h3 class="top">Expression Profile</h3>
          <!-- human -->
          <div v-show="isShow1" class="content" style="height:40%" id="outerDiv1">
            <el-table :data="profile"  :header-cell-style="{background:'rgb(115, 200, 200)',color:'#fff'}" style="width: 100%" >
              <el-table-column prop="adipose" label="adipose" ></el-table-column>
              <el-table-column prop="adrenal" label="adrenal" ></el-table-column>
              <el-table-column prop="brain" label="brain" ></el-table-column>
              <el-table-column prop="brain_R" label="brain_R" ></el-table-column>
              <el-table-column prop="breast" label="breast" ></el-table-column>
              <el-table-column prop="colon" label="colon"></el-table-column>
            </el-table>
            <el-table :data="profile"  :header-cell-style="{background:'rgb(115, 200, 200)',color:'#fff'}" style="width: 100%" >
              <el-table-column prop="foreskin" label="foreskin"></el-table-column>
              <el-table-column prop="heart" label="heart" ></el-table-column>
              <el-table-column prop="heart_R" label="heart_R" ></el-table-column>
              <el-table-column prop="HLF_1" label="HLF_1" ></el-table-column>
              <el-table-column prop="HLF_2" label="HLF_2" ></el-table-column>
              <el-table-column prop="kidney" label="kidney" ></el-table-column>
            </el-table>
            <el-table :data="profile"   :header-cell-style="{background:'rgb(115, 200, 200)',color:'#fff'}"  style="width: 100%">
              <el-table-column prop="liver" label="liver" ></el-table-column>
              <el-table-column prop="liver_R" label="liver_R" ></el-table-column>
              <el-table-column prop="lung" label="lung" ></el-table-column>
              <el-table-column prop="lymphNode" label="lymphNode" ></el-table-column>
              <el-table-column prop="ovary" label="ovary" ></el-table-column>
              <el-table-column prop="placenta_R" label="placenta_R" ></el-table-column>
            </el-table>
            <el-table :data="profile"   :header-cell-style="{background:'rgb(115, 200, 200)',color:'#fff'}" style="width: 100%" >
              <el-table-column prop="prostate" label="prostate" ></el-table-column>
              <el-table-column prop="skeltalMuscle" label="skeltalMuscle" ></el-table-column>
              <el-table-column prop="testes" label="testes" ></el-table-column>
              <el-table-column prop="testes_R" label="testes_R" ></el-table-column>
              <el-table-column prop="thyroid" label="thyroid" ></el-table-column>
              <el-table-column prop="whiteBloodCell" label="whiteBloodCell" ></el-table-column>
            </el-table>
            <div id ="figure1"></div>
          </div>
          <!-- mouse -->
          <div v-show="isShow2" class="content" style="height:40%" id="outerDiv2">
            <el-table :data="profile"  :header-cell-style="{background:'rgb(115, 200, 200)',color:'#fff'}" style="width: 100%" >
              <el-table-column prop="adipose" label="heart" ></el-table-column>
              <el-table-column prop="adrenal" label="hippocampus" ></el-table-column>
              <el-table-column prop="brain" label="liver" ></el-table-column>
              <el-table-column prop="brain_R" label="lung" ></el-table-column>
              <el-table-column prop="breast" label="spleen" ></el-table-column>
              <el-table-column prop="colon" label="thymus"></el-table-column>
            </el-table>
            <div id="figure2"></div>
          </div>
        </div>

    </div>
</template>

<script>
import axios from "axios";
import echarts from "echarts";
export default{
    data(){
      return{
        dataList:{},
        isShow:true,
        showData:"",
        profile:[],
        RNAid:"NONHSAT000530.2",
        item_data_human:['adipose', 'adrenal', 'brain', 'brain_R', 'breast', 'colon',
            'foreskin','heart','heart_R','HLF_1','HLF_2','kidney',
            'liver','liver_R','lung','lymphNode','ovary','placenta_R',
            'prostate','skeltalMuscle','testes','testes_R','thyroid','whiteBloodCell'],
        item_data_mouse:['adipose', 'adrenal', 'brain', 'brain_R', 'breast', 'colon'],
        isShow1:false,
        isShow2:false,
        FromPage:""
      
      }
    },
    mounted(){

      let sessionData = JSON.parse(sessionStorage.getItem("dataGene"));
      
      if(location.href.indexOf('#reloaded')==-1){
      location.href=location.href+"#reloaded";
        location.reload();
      }
     
      this.FromPage = this.$route.query.page;
      //console.log("visual页面的，标记从哪里来",this.FromPage)
      this.dataList=sessionData;
      //console.log(this.dataList)
      // this.showData=this.dataList.FASTA.slice(0,1000);
      this.showData = `${this.dataList.FASTA.slice(0,1000)}
                    <img
                    src="./open.png"
                    style="width:12px; height:12px"
                    class="open"
                    />
                    `
    
      this.RNAid=sessionData.NONCODE_TRANSCRIPT_ID;


      axios.post("api/property/profiles",{    
        RNAid:this.RNAid
      }).then(respond =>{
        // console.log(respond.data);
        this.profile=respond.data;
        //console.log(this.profile)
        
        if(this.profile[0].organism == "Human"){

          this.isShow1=true;
          this.drawLine1();
        }
        else{

          this.isShow2=true;
          this.drawLine2();
        }      
      });


    },
    methods:{

      kzClick(event){      
        if(event.target.className === 'open' && event.target.nodeName === 'IMG'){
                this.showData = `${this.dataList.FASTA}
                    <img
                    src="./close.png"
                    style="width:12px; height:12px"
                    class="close"
                    />
                    `
        }
        if(event.target.className === 'close' && event.target.nodeName === 'IMG'){
                this.showData = `${this.dataList.FASTA.slice(0,1000)}
                    <img
                    src="./open.png"
                    style="width:12px; height:12px"
                    class="open"
                    />
                    `
        }

      },
      drawLine1(){
        // console.log("调用dramLine1");
                // 解决数据初始渲染不出来的问题
        // setTimeout(()=>{
        //     this.initChart()
        // },1000)
        var chartDom = document.getElementById('figure1');
        var myChart1 = echarts.init(chartDom);
        var option;
        option = {
          title: {
            text: 'Expression profile',
            left: 'center'
          },
          tooltip:{
            trigger:'axis',
            axisPointer:{
              type:'shadow'
            }
          },
          xAxis: {
            type: 'category',
            data:this.item_data_human,
              axisLabel: {
                interval: 0,
                rotate:35
            }
          },
          yAxis: {
            type: 'value',
            name:"FPKM/TPM",     
          },
          series: [
            {
              data: [],
              type: 'bar',
              barCategoryGap:20
            }
          ]
        };
        option.series[0].data=[];
        option.title.text="Expression profile of "+ this.dataList.NONCODE_TRANSCRIPT_ID+" in "+this.profile[0].organism+" tissues"
        this.item_data_human.forEach(item=>{
          option.series[0].data.push(this.profile[0][item]);
        })
        myChart1.setOption(option);
      },
      drawLine2(){
        // console.log("调用dramLine2");
        
        var chartDom = document.getElementById('figure2');
        var myChart2 = echarts.init(chartDom);
        var option;
        option = {
          title: {
            text: 'Expression profile',
            left: 'center'
          },
          tooltip:{
            trigger:'axis',
            axisPointer:{
              type:'shadow'
            }
          },
          xAxis: {
            type: 'category',
            data:['heart', 'hippocampus', 'liver', 'lung', 'spleen', 'thymus'],
              axisLabel: {
                interval: 0,
                rotate:15
            }
          },
          yAxis: {
            type: 'value',
            name:"FPKM/TPM",     
          },
          series: [
            {
              data: [],
              type: 'bar',
              barCategoryGap:20
            }
          ]
        };
        option.series[0].data=[];
        option.title.text="Expression profile of "+ this.dataList.NONCODE_TRANSCRIPT_ID+" in "+this.profile[0].organism+" tissues"
        this.item_data_mouse.forEach(item=>{
          option.series[0].data.push(this.profile[0][item]);
        })
        myChart2.setOption(option);

      },
      toUrl(){
        this.$router.push({
          name:'Gene',
          query:{page:this.FromPage}

        })
      },
      toUrl_RNA(data){
          window.location.href = "http://www.noncode.org/show_rna.php?id="+data.split(".")[0]+"&version="+data.split(".")[1]+"&utd=1#"

      },
      toUrl_DNA(data){
         window.location.href = "http://www.noncode.org/show_gene.php?id="+data.split(".")[0]+"&version="+data.split(".")[1]+"&utd=1#"
      }
      // toImg(data){
      //   this.ShowImg=!data;
      // }

 
      
    }
 
}
</script>

<style>
.title {
  text-align: center;
  font-size: 1.5em;
  line-height: 40px;
  height: 80px;
  background: #e6f0ef; /* Old browsers */
  background: -moz-linear-gradient(
    -45deg,
    #e6f0ef 45%,
    #b4ede7 100%
  ); /* FF3.6-15 */
  background: -webkit-linear-gradient(
    -45deg,
    #e6f0ef 45%,
    #b4ede7 100%
  ); /* Chrome10-25,Safari5.1-6 */
  background: linear-gradient(
    135deg,
    #e6f0ef 45%,
    #b4ede7 100%
  ); /* W3C, IE10+, FF16+, Chrome26+, Opera12+, Safari7+ */
  filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#e6f0ef', endColorstr='#b4ede7',GradientType=1 );

}
.files {
  width: 70%;
  margin: 0 auto;
} 
.content {
  display: flex;
  flex-direction: column;
  text-align: left;
  padding: 20px;
  border: 1px solid rgb(115, 200, 200);
  border-radius: 10px;
  font-family:"Avenir", Helvetica, Arial, sans-serif;
  
}
span {
  display:inline-block;
  width:100%; 
  word-wrap:break-word; 
  word-break: break-all; 
  white-space:normal ; 
  font-family:"Avenir", Helvetica, Arial, sans-serif;
  font-size:15px;
} 
.newlist {
  position: relative;
  margin: 0 auto;
  width: 70%;
  min-height: 30px;
  line-height: 20px;
  /* border: 1px solid ; */
  word-break: break-all;
  hyphens: auto;
  text-align: left;
  padding: 5px 10px;
  background:#f2f4f6;
  font-family:monospace;
}
#figure1{
  width: 1000px; 
  height:500px;
  display: inline-block;
  text-align: center;
  margin:0 auto;
  

}
#figure2{
  width: 1000px; 
  height:500px;
  display: inline-block;
  text-align: center;
  margin:0 auto;

}
.img_style{

  height: 45px; 
  width: auto; 
  vertical-align: middle; 
  position: absolute;
  left: 0;
}
.top{
    border-radius: 10px;
    background: rgb(115, 200, 200);
    padding: 10px;
    height: 40px;
    line-height: 40px;
    color: #e6f0ef;
    text-align:left
}
.open_style{
  height:18px;
  width:18px;
}
.hand:hover{
  color:#1ee3cf;
  cursor:pointer
}
.el-form-item{
  font-size:16px;
}
button{
  font-size:15px;
  font-family:"Avenir", Helvetica, Arial, sans-serif;
  margin-left: 10px; 
  padding-left: 10px;
  background-color: #5bd1d7;
  border:none;
  border-radius: 10px;
}

</style>