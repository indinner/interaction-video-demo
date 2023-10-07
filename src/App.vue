<template>
  <div id="app">



    <div class="video-box">
      <video id="myVideo" class="video-js" width="100%" height="100%" controls :src="currentVideo.videoUrl"></video>
      <div v-if="questionShow&&currentVideo.questionContent!==undefined" class="question-box">
        <div class="question">
          <div class="question-content">
            {{ currentVideo.questionContent }}
          </div>
          <div @click="selectOption(index)" :id="'questionOption'+index" v-for="(item,index) in currentVideo.options" class="question-option">A. {{ item.content }}</div>
        </div>
      </div>
    </div>




  </div>
</template>

<script>
// js 使用
import Videojs from 'video.js'
import 'video.js/dist/video-js.css'
export default {
  name: 'app',

  data(){
    return{
      videoPlayerOption: {
        controls: true, //确定播放器是否具有用户可以与之交互的控件。没有控件，启动视频播放的唯一方法是使用autoplay属性或通过Player API。
        poster: '',//封面
        autoplay: true, //自动播放属性,
        muted: false, // 静音播放
        preload: 'auto', //建议浏览器是否应在<video>加载元素后立即开始下载视频数据。
        fluid: true
      },
      currentVideo:'',//正在播放的视频
      player:null,//存储video.js返回的player对象
      questionShow:false,//是否展示问题
      videoConfig:{
        startVideoID:'ID1001',//视频初始ID
        videoList:[
          {
            videoID:'ID1001',//视频ID
            videoUrl:'http://www.w3school.com.cn/example/html5/mov_bbb.mp4',//视频播放地址
            questionContent:'什么是BMI?',//视频播放结束后的题目
            options:[
              {
                content:'我不知道',//选项描述
                nextVideoID:'ID1002',//选择该选项后要跳转的视频
              },
              {
                content:'我知道',//选项描述
                nextVideoID:'ID1003',//选择该选项后要跳转的视频
              }
            ],//题目选项
            answer:0,//题目答案
          },
          {
            videoID:'ID1002',//视频ID
            videoUrl:'http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4',//视频播放地址
          },
          {
            videoID:'ID1003',//视频ID
            videoUrl:'https://media.w3.org/2010/05/sintel/trailer.mp4',//视频播放地址
          }
        ]
      },//视频配置
    }
  },

  mounted() {

    //首先获取startVideoID
    let startVideoID=this.videoConfig.startVideoID
    this.videoCore(startVideoID)

  },
  methods:{

    //视频切换核心
    videoCore(videoID){
      //从videoList查找视频
      this.currentVideo=this.videoConfig.videoList.find((video)=>{
        return video.videoID===videoID
      })
      this.player=Videojs(document.getElementById('myVideo'),this.videoPlayerOption,()=>{})

      this.player.on('timeupdate',()=>{
        let currentTime=this.player.currentTime();
        console.log("当前视频ID:",this.currentVideo.videoID,"当前视频进度：",currentTime)
        /**
         * TODO:判断当前视频播放进度,将当前视频播放时间通知出去
         **/
      })
      this.player.on('play',()=>{
        console.log("监听到视频开始播放")
      })
      this.player.on('ended',()=>{
        console.log("监听到视频播放完毕")
        /**
         * TODO:展示视频后带的题目
         **/
        this.questionShow=true
      })
    },

    //答案选择
    selectOption(userAnswer){

      //判断答案是否正确，展示相应的颜色

      if(userAnswer===this.currentVideo.answer){
        document.getElementById('questionOption'+userAnswer).style.background='green'
      }else {
        document.getElementById('questionOption'+userAnswer).style.background='red'
      }

      setTimeout(()=>{
        this.questionShow=false
        let nextVideoID=this.currentVideo.options[userAnswer].nextVideoID
        console.log("开始播放下一个视频，视频ID:",nextVideoID)
        this.videoPlayerOption.autoplay=true
        this.videoCore(nextVideoID)
      },1500)
    }
  }

}
</script>

<style>
.question-option{
  background-color: rgb(227, 238, 223);
  padding: 10px;
  margin-top: 12px;
  margin-right: 200px;
  border-radius: 7px;
  cursor: pointer;
  color: black;
  font-weight: 700;

}
.question-content{
  font-size: 22px;
}
.question{
  background-color: #107bc7;
  width: 50%;
  padding: 30px;
  margin: auto;
  border-radius: 10px;
}
.question-box{
  width: 1280px;
  height: 720px;
  position: absolute;
  background: rgba(0,0,0,0.5);
  top: 0;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
}
.video-box{
  width: 1280px;
  height: 720px;
  position: relative;
}
</style>
