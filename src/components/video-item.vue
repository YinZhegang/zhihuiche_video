<template>
  <div class="videoItem" :style="{width: video[0].width*2 + 'px',height:video[0].height*2+ 133 + 'px',position:'relative'}">
    <div style="height: 30px;display: flex;flex-flow: row nowrap;justify-content: space-between">
      <i class="yzg-icon yzg-small" :style="{backgroundImage:'url('+ icon.ren+')'}"></i>
      <p style="flex: 1;line-height: 2">测试人员{{0}}</p>
      <el-button style="height: 23px;margin: 0 10px;border:#1E7D78 solid 1px;color: #1E7D78;border-radius: 6px;line-height: 10px" plain size="mini">接收设备</el-button>
      <i  @click="enterFullScreen(0)" class="yzg-icon yzg-mid" :style="{backgroundImage:'url('+ icon.quanpin+')'}"></i>
      <i  @click="enterFullScreen(0)" class="yzg-icon yzg-mid" :style="{backgroundImage:'url('+ icon.quanpin2+')'}"></i>
      <i  @click="dispose(0)" class="yzg-icon yzg-mid" :style="{backgroundImage:'url('+ icon.close+')'}"></i>
    </div>
    <div v-if="true" class="zhezhao" :style="{height:video[0].height*2 +5 + 'px',lineHeight:video[0].height*2 +5 +'px',fontSize:video[0].height/5 +'px' }">VIDEO</div>
    <video :id="video[0].id"  class="video-js" controls preload="auto" :style="{width:video[0].width*2 + 'px',height:video[0].height*2 + 'px'}" data-setup='{ "html5" : { "nativeTextTracks" : false } }'>
    </video>
    <div style="color: #888888;padding: 5px">分辨率： 720p &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帧速率：25帧/秒</div>
  </div>
</template>

<script>
export default {
  name: 'video-item'
}
</script>

<style scoped>

</style>
