<template>
<div >
    <div class="header-head">
      <div class="header_txt">修改我的信息</div>  
      <i class="el-icon-check edit_pos" @click="uploadcommets"></i>
    </div>


    <div class="upload" @click="fileClick">
      <div class="upload_warp" v-show="imgList.src">
          <img :src="imgList.src" class="headimg_icon">
      </div>
      <div class="upload_warp" v-show="!imgList.src">
          <i class="el-icon-upload2 addimge_icon"></i>
      </div>
      
      <input @change="fileChange($event)" type="file" id="upload_file"  style="display: none"/>
    </div>
 
    <div class="userinfo_show">
      <div>用户名：<el-input v-model="getUserInfo.username" :placeholder='getUserInfo[0].username' :disabled="true"></el-input></div>
      <div>用户邮箱：<el-input v-model="getUserInfo.useremail" :placeholder='getUserInfo[0].useremail' :disabled="true"></el-input></div>
      <div>用户原密码：<el-input v-model="getUserInfo.password" :placeholder='getUserInfo[0].password' :disabled="true"></el-input></div>
      <div>用户新密码：<el-input v-model="newpassword" :placeholder='getUserInfo.newpassword' :disabled="true"></el-input></div>
    </div>

<!-- modal -->
    <el-dialog title="提示"   :visible.sync="dialogVisible"  width="88%">  
       <span>{{prompt}}</span> 
        <span slot="footer" class="dialog-footer">
         <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
        </span>
    </el-dialog>
    <intfoot></intfoot>
</div>
</template>

<script>
import intfoot from './footer.vue'

export default {
  components :{
      intfoot,
  },
  created(){
    this.getalluseinfo();
  },
  data () {
    return{
      dialogVisible: false,
      prompt:'发送失败',
      getUserInfo:'',
      headimg: '',
      size: 0,
      imgList:'',
      imgList:{
        src:'',
      },
      alluseinfo:'',
      newpassword:'',
      getUserInfo:''
    }
  },

  methods: {
          fileClick() {
              document.getElementById('upload_file').click()
          },
          fileChange(el) {
              if (!el.target.files[0].size) return;
              this.fileList(el.target);
              el.target.value = ''
          },
          fileList(fileList) {
              let files = fileList.files;
              for (let i = 0; i < files.length; i++) {
                  //判断是否为文件夹
                  if (files[i].type != '') {
                      this.fileAdd(files[i]);
                  } else {
                      //文件夹处理
                      this.folders(fileList.items[i]);
                  }
              }
          },
          //文件夹处理
          folders(files) {
              let _this = this;
              //判断是否为原生file
              if (files.kind) {
                  files = files.webkitGetAsEntry();
              }
              files.createReader().readEntries(function (file) {
                  for (let i = 0; i < file.length; i++) {
                      if (file[i].isFile) {
                          _this.foldersAdd(file[i]);
                      } else {
                          _this.folders(file[i]);
                      }
                  }
              })
          },
          foldersAdd(entry) {
              let _this = this;
              entry.file(function (file) {
                  _this.fileAdd(file)
              })
          },
          fileAdd(file) {
              //总大小
              this.size = this.size + file.size;
              //判断是否为图片文件
              if (file.type.indexOf('image') == -1) {
                  file.src = 'wenjian.png';
                  this.imgList= file
              } else {
                  let reader = new FileReader();
                  reader.vue = this;
                  reader.readAsDataURL(file);
                  reader.onload = function () {
                      file.src = this.result;
                      this.vue.imgList= file
                  }
              }
          },
          uploadcommets(){
            let newUserinfo = {
              username : this.getUserInfo[0].username,
              useremail: this.getUserInfo[0].useremail,
              headimage : JSON.stringify(this.imgList),
              headimagelastModified: this.imgList.lastModified
            };

            
            let newaccount = {
              username:this.getUserInfo.username,
              useremail: this.getUserInfo[0].useremail,
              newpassword: this.newpassword,
            };
            console.log(this.getUserInfo.username)
            console.log( JSON.parse(localStorage.getItem('userInfo'))[0].username)

            // if(this.getUserInfo.username == '' || this.getUserInfo.username == undefined){}

              // 修改头像
              // this.$http.post('/api/headinfo/updateheadinfo',newUserinfo).then((response) => {
              //       if(response){
              //           console.log(response)        
              //       }else {
              //           console.log('发送失败') 
                      
              //       }
              //   })


            // localStorage.removeItem('userInfo')
                // 修改用户名密码
                // this.$http.post('/api/login/updateAccount',newaccount).then((response) => {
                //   localStorage.removeItem('userInfo')
                //   console.log(response)
                //   this.$router.push('/login')
                // })         


          },



          getalluseinfo(){ console.log() 
            this.getUserInfo = JSON.parse(localStorage.getItem('userInfo'))
              console.log(this.getUserInfo);
              
            let getuseremail = {
              useremail: this.getUserInfo[0].useremail,
            };
            // console.log(getuseremail)
            this.$http.post('/api/headinfo/getheadinfo',getuseremail).then((response) => {
                  if(response.data == '' || response.data == null || response.data ==[]){
                      let newUserinfo = {
                        username : this.getUserInfo[0].username,
                        useremail: this.getUserInfo[0].useremail,
                        headimage : JSON.stringify(this.imgList),
                        headimagelastModified: this.imgList.lastModified
                      };
                      
                      this.$http.post('/api/headinfo/createheadinfo',newUserinfo).then((response) => {
                            if(response){
                                console.log(response)
                                return;        
                            }else {
                                console.log('发送失败') 
                                return;    
                            }
                        })  
                  } else{
                  this.imgList.src = JSON.parse(response.data[0].headimg).src  
                  // console.log(this.imgList.src) }  
                  }                          
            })

          }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.upload{
  margin: 0 auto;
}
.headimg_icon{
  height: 6rem;
  width: 6rem;
  border-radius: 50%;
}
.edit_pos{
    font-size: 1.2rem;
    position: absolute;
    right: 1rem;
}
.header-head{
    background-color: #409EFF;
    width: 100%;
    height: 2.5rem;
    color: #fff;
    display: flex;
    align-items:center;
}
.header_txt{
    padding-left: .5rem;
}
.upload_warp{
    /* width: 6rem;
    height: 6rem;
    border: 1px solid #E4E7ED; */
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content:center;
    margin: 1rem;
}

.upload{
    background-size: 6rem ;
    width: 6rem;
    height: 6rem;
    background-repeat: no-repeat;
    /* float: left; */
    /* position: relative; */
}
.addimge_icon{
  color: #E4E7ED;
  font-size: 3rem;
}
</style>
