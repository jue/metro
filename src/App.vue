<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<style lang="less">
*,
*:before,
*:after {
  box-sizing: border-box;
  margin: 0;
  outline: none;
  // transition: all 0.2s linear;
}
body {
  color: #303133;
  font-family: "Source Sans Pro", -apple-system, BlinkMacSystemFont, "Segoe UI",
    Roboto, "Helvetica Neue", Arial, sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  padding: 0;
  background: url(https://img14.360buyimg.com/uba/jfs/t1/99322/14/2339/2892/5dce3d61E74213df8/e81abd2efc920499.png)
    #fcf9f2;
}
a {
  text-decoration: none;
  color: #409eff;
}
img {
  max-width: 100%;
  vertical-align: middle;
}
#app {
  min-height: 100vh;
}
[class*=" el-icon-bimgo-"],
[class^="el-icon-bimgo-"] {
  font-family: "iconfont" !important;
}

.icon {
  width: 1em;
  height: 1em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}

// ::-webkit-scrollbar {
//   display: none;
// }

/* 滚动槽 */
::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}
::-webkit-scrollbar-track {
  border-radius: 3px;
  background: rgba(0, 0, 0, 0.06);
  -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.08);
}
/* 滚动条滑块 */
::-webkit-scrollbar-thumb {
  border-radius: 3px;
  background: rgba(0, 0, 0, 0.12);
  -webkit-box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.2);
}

.model-tag {
  color: #409eff;
  font-size: 20px;
}

.danger {
  color: #f56c6c !important;
}

.success {
  color: #67c23a !important;
}

.warning {
  color: #e6a23c !important;
}
</style>
