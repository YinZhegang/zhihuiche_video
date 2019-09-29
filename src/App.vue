<template>
  <div id="app" style="height: 100%">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
  html,div,p,h1,h2,h3,h4,h5,table,tr,td,tt,th,thead,tfoot,ul,ol,li{
    margin: 0;
    padding: 0;
  }
  body{
    background-color: #F5F5F5;
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    margin:0;padding:0px;
    height: 100%;
    font-family:"Microsoft YaHei",YaHei,"微软雅黑",SimHei,"黑体";font-size:14px
  }
  html{
    height: 100%;
  }
  ::-webkit-scrollbar {
    width: 0px;
  }

  ::-webkit-scrollbar-track {
    background-color: none;
  }

  ::-webkit-scrollbar-thumb {
    background-color: none;
  }

  ::-webkit-scrollbar-thumb:hover {
    background-color: none;
  }

  ::-webkit-scrollbar-thumb:active {
    background-color: none;
  }
</style>
