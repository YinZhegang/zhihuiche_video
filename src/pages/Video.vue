<template>
      <div id="content" class="videoItem" style="width: 100%;height: 100%">
        <div style="height: 35px;display: flex;flex-flow: line-height: 28px ;flex-flow: row nowrap;justify-content: space-between">
          <i class="yzg-icon yzg-small" :style="{backgroundImage:'url('+ (video.type===2?icon.ren:(video.work_type==1?icon.camera:icon.jiankong)) +')'}"></i>
          <p style="flex: 1;line-height: 2.5">{{!video.loading ==0?video.device_name:''}}</p>
          <el-button v-if="video.type==1" @click="videoAlert()" style="height: 23px;margin: 0 10px;margin-top: 6px;border:#1E7D78 solid 1px;color: #1E7D78;border-radius: 6px;line-height: 10px" plain size="mini">接收设备</el-button>
          <!--           <i  @click="fullWindow(index)" class="yzg-icon yzg-mid" :style="{backgroundImage:'url('+ icon.quanpin+')'}"></i>-->
          <i  @click="enterFullScreen()" class="yzg-icon yzg-mid" :style="{backgroundImage:'url('+ icon.quanpin2+')'}"></i>
          <i  @click="close(index)" class="yzg-icon yzg-mid" :style="{backgroundImage:'url('+ icon.close+')'}"></i>
        </div>
        <div v-if="video.loading ==0||video.loading == -1" class="zhezhao">
          <p style="font-size: 80px;line-height: 45px;position: relative;top: 46%" v-if="video.loading ==0">VIDEO</p>
          <p v-if="video.loading == -1" style="font-size: 16px;line-height: 40px;position: relative;top: 40%">
            <i style="color: #888888;font-size: 70px;display: block" class="el-icon-warning-outline"></i>
            图传获取失败，<a @click="reloadVideo()" style="color:rgb(205, 127, 11);text-decoration: none" href="javascrit:;">点击重试</a>
          </p>
        </div>
        <div v-if="video.loading ==1" class="zhezhao2" @click="loadVideo">
          <i style="font-size: 70px;color: #cacaca;opacity: .4;position: relative;top: 46%" class="el-icon-video-play"></i>
        </div>
        <video id="video" class="video-js" controls preload="auto" style="height: calc(100% - 65px);width: 100%">
        </video>
        <div style="color: #888888;padding: 5px;position: absolute;bottom: 0">分辨率： 720p &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帧速率：25帧/秒</div>
      </div>
</template>

<script>
import ren from '@/assets/renhover.png'
import tongxunche from '@/assets/tongxinhover.png'
import camera from '@/assets/camera.png'
import quanpin from '@/assets/quanpin.png'
import quanpin2 from '@/assets/quanpin2.png'
import close from '@/assets/close.png'
import jiankong from '@/assets/jiankonghover.png'
import videoItem from '@/components/video-item'
export default {
  name: 'Index',
  data () {
    return {
      icon: {
        ren, quanpin, quanpin2, close, videoItem, tongxunche, jiankong, camera
      },
      clientWidth: 0,
      video: {
        videoid: 'content',
        device_id: '2e230045552',
        device_name: '还考呢很高',
        stream_url: '',
        type: 2,
        work_type: 1,
        loading: 2,
        player: null
      }
    }
  },
  methods: {
    loadVideo () {

    },
    fullWindow () {
      this.video.player.requestFullscreen()
    },
    videoAlert () {
      DispatchJSManger.OpenVideoAlert(JSON.stringify(this.video))
    },
    reloadVideo () {
      this.video.loading = 1
      this.video.player.src({
        src: this.video.stream_url
      })
      this.$nextTick(() => {
        this.video.player.play()
      })
    },
    close (idx) {
      if (this.video.player) this.video.player.pause()
      this.video.loading = 0
      DispatchJSManger.VideoCLose(this.video[idx])
    },
    goPlay () {
      this.video.player.play()
    },
    play () {
      // this.video[idx].player.reset()
      // this.video[idx].player.load()
      this.video.player.play()
    },
    enterFullScreen () {
      if (this.video.loading == 0 || this.video.loading == -1) return
      this.video.player.requestFullscreen()
      DispatchJSManger.FullWindow()
    },
    dispose (idx) {
      this.video.player.dispose()
    },
    loadVideoByIndex (idx) {
      var that = this
      if (this.video[idx].player) {
        this.video[idx].loading = 3
        this.video[idx].player.play()
      } else {
        this.$nextTick(() => {
          // eslint-disable-next-line no-undef
          this.video.player = videojs('video', {
            autoplay: false,
            muted: false, // 静音
            fluid: true,
            controls: false, // 控制条,
            preload: 'auto', // 预加载
            language: 'zh-CN', // 初始化语言
            playbackRates: [1, 2, 3, 4, 5, 8, 10, 20], // 播放速度
            'techOrder': ['flash'],
            // eslint-disable-next-line standard/object-curly-even-spacing
            sources: { src: that.video.stream_url}
            // type: 'rtmp/flv'
          }, function () {
            console.log('--------------成功初始化视频--------------')
            that.video.loading = 3
            that.video.player.play()
            that.video.player.one('playing', function () { // 监听播放
              console.log('开始播放')
            })
            that.video.player.one('error', function (error) { // 监听错误
              console.error('监听到异常，错误信息：%o', error)
              that.video.loading = -1
            })
          })
        })
      }
    }
  },
  created () {
    this.clientWidth = document.body.clientWidth
    window.quitFullWindow = () => {
      this.video.player.exitFullscreen()
    }
    window.replaceVideoByIndex = (index, data) => {
      this.loadDataByIndex(index, JSON.parse(data))
    }
  },
  mounted () {
    // 初始化视频，设为全局变量
    // insertVideo(`{"device_id":"2e230045552","stream_url":"https://www.w3cschool.cn/statics/demosource/mov_bbb.mp4","device_name":"还考呢很高","type":2}`)
    // console.log(this.video)
    // this.loadDataByIndex(1, {id: 'yzg2019', rmtp: 'https://www.w3cschool.cn/statics/demosource/mov_bbb.mp4'})
  }
}
</script>

<style scoped>
  .video-js{
    background-color: #888888!important;
  }
  i{
    color: #42b983;
    font-size: 25px;
    margin: 0 5px;
    cursor: pointer;
  }
  .videoItem{
    border-radius: 5px;
    border: 1px solid #c6c6c6;
    position:relative;
    float: left;
    box-sizing: border-box;
    overflow: hidden;
  }
  .yzg-icon{
    background-position: center;
    background-size: 100% 100%;
    display: inline-block;
    margin: 0 10px;
  }
  .yzg-small{
    position: relative;
    top: 10px;
    width: 14px;
    height: 16px;
  }
  .yzg-mid{
    position: relative;
    top: 8px;
    width: 18px;
    height: 18px;
  }
  .zhezhao{
    position: absolute;
    top:35px;
    width: 100%;
    height: calc(100% - 65px);
    z-index: 999;
    background-color: #888888;
    background: -webkit-radial-gradient(#CACACA,#A1A1A1); /* Safari 5.1 - 6.0 */
    background: -o-radial-gradient(#CACACA,#A1A1A1); /* Opera 11.6 - 12.0 */
    background: -moz-radial-gradient(#CACACA,#A1A1A1); /* Firefox 3.6 - 15 */
    background: radial-gradient(#CACACA,#A1A1A1); /* 标准的语法（必须放在最后） */
    text-align: center;
    color: #888888;
  }
  .zhezhao2{
    position: absolute;
    top:35px;
    width: 100%;
    height: calc(100% - 65px);
    z-index: 999;
    background-color: #000000;
    /*background: -webkit-radial-gradient(#a7a6a6, #757575); !* Safari 5.1 - 6.0 *!*/
    /*background: -o-radial-gradient(#a7a6a6,#757575); !* Opera 11.6 - 12.0 *!*/
    /*background: -moz-radial-gradient(#a7a6a6,#757575); !* Firefox 3.6 - 15 *!*/
    /*background: radial-gradient(#a7a6a6,#757575); !* 标准的语法（必须放在最后） *!*/
    text-align: center;
    color: #888888;
  }
</style>
