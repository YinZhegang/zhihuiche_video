<template>
    <div id="content" style="width: calc(100% - 40px);height:  calc(100% - 30px);display: flex;flex-flow: row wrap;justify-content: space-between;padding: 10px;">
<!--      <textarea cols="6" v-model="mode" style="width: 200px;position: absolute;top: 50px;z-index: 9999999" />-->
<!--      <button @click="gggg()" style="position: absolute;top: 20px;z-index: 9999999">提交</button>-->
     <div :style="{marginLeft:' 20px',marginTop:video.length==4?'30px':0}">
       <div class="videoItem" v-for="item,index in video" :style="{width: item.width + 'px',height:item.height +65 + 'px'}">
         <div style="height: 35px;display: flex;flex-flow: line-height: 28px ;flex-flow: row nowrap;justify-content: space-between">
           <i class="yzg-icon yzg-small" v-if="!item.loading ==0" :style="{backgroundImage:'url('+ (item.type===1?icon.ren:(item.work_type==1?icon.camera:icon.jiankong)) +')'}"></i>
           <p style="flex: 1;line-height: 2.5">{{!item.loading ==0?item.device_name:''}}</p>
           <el-button v-if="item.loading!=0&&item.type==2&&item.work_type==2" @click="videoAlert(index)" style="height: 23px;margin: 0 10px;margin-top: 6px;border:#1E7D78 solid 1px;color: #1E7D78;border-radius: 6px;line-height: 10px" plain size="mini">接收设备</el-button>
<!--           <i  @click="fullWindow(index)" class="yzg-icon yzg-mid" :style="{backgroundImage:'url('+ icon.quanpin+')'}"></i>-->
           <i  @click="enterFullScreen(index)" class="yzg-icon yzg-mid" :style="{backgroundImage:'url('+ icon.quanpin2+')'}"></i>
           <i  @click="close(index)" class="yzg-icon yzg-mid" :style="{backgroundImage:'url('+ icon.close+')'}"></i>
         </div>
         <div class="zhezhao" :style="{lineHeight:item.height +5 +'px',fontSize:item.height/5 +'px',opacity: item.loading ==0?1:0,zIndex: item.loading ==0?9999:0 }">
           <p>VIDEO</p>
<!--           <p v-if="item.loading ==0">VIDEO</p>-->
<!--           <p v-if="item.loading == -1" style="font-size: 16px;line-height: 40px;position: relative;top: 40%">-->
<!--             <i style="color: #888888;font-size: 70px;display: block" class="el-icon-warning-outline"></i>-->
<!--             图传获取失败，<a @click="reloadVideo(index)" style="color:rgb(205, 127, 11);text-decoration: none" href="javascrit:;">点击重试</a>-->
<!--           </p>-->
         </div>
         <div class="zhezhao2" @click="loadVideoByIndex(index)" :style="{lineHeight:item.height +5 +'px',fontSize:item.height/5 +'px',opacity:item.loading ==1||item.loading==2?1:0 }">
            <i style="font-size: 70px;color: #cacaca;opacity: .4" :class="item.loading==2?'el-icon-loading':'el-icon-video-play'"></i>
         </div>
         <div :ref="item.videoid" :style="{width: item.width + 'px',height: item.height + 'px',overflow:'hidden'}">
           <video :id="item.videoid" class="video-js" controls preload="auto" :style="{width: item.width + 'px',height: item.height + 'px'}">
           </video>
         </div>
         <div v-if="item.loading!=0" style="color: #888888;padding: 5px;z-index: 999;background: #ffffff;">分辨率： 720p &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帧速率：25帧/秒</div>
       </div>
     </div>
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
import loading from '@/assets/loading.gif'
import videoItem from '@/components/video-item'
import { Message } from 'element-ui'
export default {
  name: 'Index',
  data () {
    return {
      throtle: true,
      mode: '',
      icon: {
        ren, quanpin, quanpin2, close, videoItem, tongxunche, jiankong, camera, loading
      },
      videoSix: [],
      clientWidth: 0,
      video: [],
      videoModel: {
        id: 'video',
        rmtp: ''
      }
    }
  },
  methods: {
    gggg () {
      eval(this.mode)
    },
    fullWindow (idx) {
      this.video[idx].player.requestFullscreen()
    },
    videoAlert (idx) {
      DispatchJSManger.OpenVideoAlert(JSON.stringify(this.video[idx]))
    },
    reloadVideo (idx) {
      this.video[idx].loading = 1
      this.video[idx].player.src({
        src: this.video[idx].stream_url
      })
      this.$nextTick(() => {
        this.video[idx].player.play()
      })
    },
    close (idx) {
      if (this.video[idx].player) {
        this.video[idx].player.pause()
      }
      this.$nextTick(() => {
        this.video[idx].loading = 0
      })
      this.$forceUpdate()
      DispatchJSManger.VideoClose(JSON.stringify(this.video[idx]))
    },
    goPlay (idx) {
      this.video[idx].player.play()
    },
    play (idx) {
      // this.video[idx].player.reset()
      // this.video[idx].player.load()
      this.video[idx].player.play()
    },
    switchVideoWindow (n) {
      var that = this
      if (this.video.length < n) {
        let winTtem = []
        for (let i = 0; i < n - this.video.length; i++) {
          winTtem.push({
            videoid: 'video' + this.video.length,
            device_id: '',
            device_name: '',
            work_type: 1,
            type: 2,
            loading: 0,
            player: null
          })
        };
        console.log(winTtem)
        switch (this.video.length) {
          case 4:
            if (n == 6) {
              this.video = [...this.video, ...winTtem]
              for (let i = 0; i < this.video.length; i++) {
                this.video[i].width = Math.ceil((this.clientWidth - 100) / 3)
                this.video[i].height = Math.ceil((this.clientWidth - 100) * 9 / 48)
              };
              this.video[0].width = Math.ceil((this.clientWidth - 100) * 2 / 3) + 13
              this.video[0].height = Math.ceil((this.clientWidth - 100) * 18 / 48) + 77
            }
            if (n == 9) {
              this.video = [...this.video, ...winTtem]
              console.log(this.video)
              this.$nextTick(() => {
                this.video.forEach((item, index) => {
                  this.video[index].width = Math.ceil((this.clientWidth - 100) / 3)
                  this.video[index].height = Math.ceil((this.clientWidth - 100) * 9 / 48)
                })
              })
            }
            break
          case 6:
            if (n == 9) {
              this.video = [...this.video, ...winTtem]
              console.log(this.video)
              this.$nextTick(() => {
                this.video.forEach((item, index) => {
                  this.video[index].width = Math.ceil((this.clientWidth - 100) / 3)
                  this.video[index].height = Math.ceil((this.clientWidth - 100) * 9 / 48)
                })
              })
            }
            break
          case 9:
            this.video = [...this.video, ...winTtem]
            console.log(this.video)
            this.$nextTick(() => {
              this.video.forEach((item, index) => {
                this.video[index].width = Math.ceil((this.clientWidth - 100) / 3)
                this.video[index].height = Math.ceil((this.clientWidth - 100) * 9 / 48)
              })
            })
            break
        }
      } else {
        this.video.splice(n, this.video.length - n)
        switch (this.video.length) {
          case 4:
            this.$nextTick(() => {
              this.video.forEach((item, index) => {
                this.video[index].width = Math.ceil((this.clientWidth - 90) / 2)
                this.video[index].height = Math.ceil((this.clientWidth - 90) * 9 / 32)
              })
            })
            break
          case 6:
            this.video.forEach((item, index) => {
              this.video[index].width = Math.ceil((this.clientWidth - 100) / 3)
              this.video[index].height = Math.ceil((this.clientWidth - 100) * 9 / 48)
            })
            this.video[0].width = Math.ceil((this.clientWidth - 100) * 2 / 3) + 13
            this.video[0].height = Math.ceil((this.clientWidth - 100) * 18 / 48) + 77
            break
        }
      }
      this.$nextTick(() => {
        this.video.forEach((item, index) => {
          // let domObj = document.getElementById('video' + index + '_flash_api')
          let domDiv = document.getElementById('video' + index)
          if (domDiv) {
            // domObj.style.width = this.video[index].width + 'px'
            // domObj.style.height = this.video[index].height + 'px'
            domDiv.style.width = this.video[index].width + 'px'
            domDiv.style.height = this.video[index].height + 'px'
          }
          that.$forceUpdate
          // this.video[index].player.size(this.video[index].width,this.video[index].height)
        })
      })
    },
    intVideoWindow (n) {
      var winTtem = []
      for (let i = 0; i < n; i++) {
        winTtem.push({
          videoid: 'video' + i,
          device_id: '',
          device_name: '',
          stream_url: '',
          type: 2,
          work_type: 1,
          loading: 0,
          player: null
        })
      }
      switch (n) {
        case 4:
          for (let i = 0; i < n; i++) {
            winTtem[i].width = Math.ceil((this.clientWidth - 90) / 2)
            winTtem[i].height = Math.ceil((this.clientWidth - 90) * 9 / 32)
          };
          this.video = winTtem
          break
        case 6:
          for (let i = 0; i < n; i++) {
            winTtem[i].width = Math.ceil((this.clientWidth - 100) / 3)
            winTtem[i].height = Math.ceil((this.clientWidth - 100) * 9 / 48)
          };
          winTtem[0].width = Math.ceil((this.clientWidth - 100) * 2 / 3) + 13
          winTtem[0].height = Math.ceil((this.clientWidth - 100) * 18 / 48) + 77
          this.video = winTtem
          break
        case 9:
          for (let i = 0; i < n; i++) {
            winTtem[i].width = Math.ceil((this.clientWidth - 100) / 3)
            winTtem[i].height = Math.ceil((this.clientWidth - 100) * 9 / 48)
          };
          this.video = winTtem
          break
      }
    },
    enterFullScreen (idx) {
      if (this.video[idx].loading == 0 || this.video[idx].loading == -1 || this.video[idx].loading == 2) return
      this.video[idx].player.requestFullscreen()
      DispatchJSManger.FullWindow()
    },
    dispose (idx) {
      this.video[idx].player.dispose()
    },
    loadDataByIndex (idx, data) {
      if (this.video[idx].player) {
        this.video[idx].device_id = data.device_id
        this.video[idx].type = data.type
        this.video[idx].work_type = data.work_type
        this.video[idx].device_name = data.device_name
        this.video[idx].loading = 2
        this.video[idx].player.src({
          src: data.stream_url
        })
        this.video[idx].player.play()
        // this.$nextTick(() => {
        //   this.video[idx].player.pause()
        // })
      } else {
        this.video[idx].device_id = data.device_id
        this.video[idx].type = data.type
        this.video[idx].work_type = data.work_type
        this.video[idx].device_name = data.device_name
        this.video[idx].id = data.id
        this.video[idx].stream_url = data.stream_url
        // this.video[idx].loading = 1
        this.loadVideoByIndex(idx)
      }
    },
    loadVideoByIndex (idx) {
      var that = this
      if (this.video[idx].loading == 3) return
      if (this.video[idx].player) {
        this.video[idx].loading = 2
        this.video[idx].player.play()
      } else {
        this.$nextTick(() => {
          this.video[idx].loading = 2
          // eslint-disable-next-line no-undef
          this.video[idx].player = videojs(this.video[idx].videoid, {
            autoplay: false,
            muted: false, // 静音
            fluid: true,
            controls: false, // 控制条,
            preload: 'auto', // 预加载
            language: 'zh-CN', // 初始化语言
            playbackRates: [1, 2, 3, 4, 5, 8, 10, 20], // 播放速度
            'techOrder': ['flash'],
            // eslint-disable-next-line standard/object-curly-even-spacing
            sources: { src: that.video[idx].stream_url}
            // type: 'rtmp/flv'
          }, function () {
            console.log('--------------成功初始化视频--------------')
            that.video[idx].player.play()
            that.video[idx].player.on('playing', function () { // 监听播放
              console.log('开始播放')
              if (that.video[idx].loading == 0) {
                that.video[idx].player.pause()
                return
              }
              that.video[idx].loading = 3
            })
            that.video[idx].player.on('error', function (error) { // 监听错误
              console.error('监听到异常，错误信息：%o', error)
              that.video[idx].loading = -1
            })
          })
        })
      }
    }
  },
  watch: {
    video (n, o) {
      if (n.length >= 6) {
        // console.log(n)
        // this.videoSix = this.video.slice(3, 8)
        // console.log(this.videoSix)
        // this.video[1].width = (this.clientWidth - 90) / 2
      }
    }
  },
  created () {
    this.clientWidth = document.body.clientWidth
    window.quitFullWindow = () => {
      for (var i = 0; i < this.video.length; i++) {
        try {
          this.video[i].player.exitFullscreen()
        } catch (e) {
        }
      }
    }
    window.switchVideoWindow = (n) => {
      this.switchVideoWindow(n)
    }
    window.replaceVideoByIndex = (index, data) => {
      this.loadDataByIndex(index, JSON.parse(data))
    }
    window.insertVideo = (data) => {
      if (!this.throtle) return
      var that = this
      let da = JSON.parse(data)
      let len = this.video.length
      for (let i = 0; i < len; i++) {
        // if (this.video[i].device_id == da.device_id) {
        //   if (this.video[i].loading == 3) {
        //     Message({
        //       message: '视频已经加载，请勿重复添加',
        //       type: 'warning',
        //       center: true
        //     })
        //     return
        //   }
        // }
        if (this.video[i].loading == 0) {
          this.loadDataByIndex(i, da)
          // this.video[i].loading = 1
          // this.video[i].player.src({
          //   src: da.rmtp
          // })
          break
        }
        if (i == len - 1) {
          that.video[0].type = da.type
          that.video[0].work_type = da.work_type
          that.video[0].device_name = da.device_name
          that.video[0].loading = 2
          if (that.video[0].player) {
            that.video[0].player.src({
              src: da.stream_url
            })
            that.video[0].player.play()
          } else {
            that.video[0].stream_url = da.stream_url
          }
          break
        }
      }
    }
  },
  mounted () {
    // 初始化视频，设为全局变量
    this.intVideoWindow(4)
    // insertVideo(`{"device_id":"9999","stream_url":"rtmp://media3.sinovision.net:1935/live/livestream","device_name":"还考呢很444高","type":2,"work_type":2}`)
    // this.loadDataByIndex(1, {id: 'yzg2019', rmtp: 'https://www.w3cschool.cn/statics/demosource/mov_bbb.mp4'})
    // location.reload()
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
    margin: 5px;
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
    z-index: 999999;
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
