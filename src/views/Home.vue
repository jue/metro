<template>
  <div class="bim-box" v-loading.fullscreen.lock="!modelLoaded">
  <!-- <div class="bim-box"> -->
    <Viewer
      @getClickData="getClickData"
      @modelLoaded="modelLoaded=true"
      class="bim-viewer abs z-1"
      ref="view"
    ></Viewer>
    <!-- 左上角工具栏 -->
    <Tools @changeStation="changeStation" @seachEquipment="seachEquipment" class="tools abs z-2"></Tools>
    <Alert class="alert abs z-2"></Alert>
    <Info v-show="$store.state.switch.showInfo"></Info>
    <div class="router-view abs z-2" v-if="showRouterView">
      <router-view
        :accident="accident"
        :tags="tags"
        @addMarkUp="addMarkUp"
        @restoreState="restoreState"
        @showComponents="showComponents"
        @showEquipmentStatus="showEquipmentStatus"
        @activeHeatmap="activeHeatmap"
        ref="routerview"
      ></router-view>
    </div>

    <Point :points="points" @restoreState="restoreState"></Point>
    <point-edit
      :currPoint="currPoint"
      @getShot="getShot"
      @getState="getState"
      @savePoint="savePoint"
    ></point-edit>
    <Tag-edit :currTag="currTag" @saveTag="saveTag" @saveAccident="saveAccident" ref="tagEdit"></Tag-edit>
  </div>
</template>

<script>
// @ is an alias to /src
// import Viewer from '@/components/bimface/Viewer2.vue'
import Viewer from '@/components/forge/Viewer.vue'
import Tools from '@/components/Tools.vue'
import Alert from '@/components/Alert.vue'
import Info from '@/components/Info.vue'
import Point from '@/components/Point.vue'
import PointEdit from '@/components/PointEdit.vue'
import TagEdit from '@/components/TagEdit.vue'
import axios from 'axios'
export default {
  name: 'home',
  components: {
    Viewer,
    Tools,
    Alert,
    Info,
    Point,
    PointEdit,
    TagEdit
  },
  data() {
    return {
      showRouterView: false,
      points: [], //视角列表
      tags: [], //标签列表
      accident: [], //客伤列表
      isShowPointEdit: false, //是否显示视角编辑框
      currPoint: {
        name: '',
        state: {},
        shot: ''
      },
      currSid: '',
      modelLoaded: false, //模型加载完成
      currTag: {
        type: '', //类型：camera / accident
        name: '',
        desc: '',
        forge_data: {},
        video_url: '' //摄像头的播放URL,示例是萤石云m3u8地址
      }
    }
  },
  beforeMount() {
    //设置默认站点SID
    if (this.$route.query.s != undefined && this.$route.query.s != '') {
      this.$store.commit('update_currSid', this.$route.query.s)
    }
  },
  mounted() {
    this.showRouter()

    //初始化车站
    this.init()
  },
  methods: {
    activeHeatmap(heatmap){
      this.$refs.view.activeHeatmap(heatmap)
    },
    showEquipmentStatus(data, color) {
      this.$refs.view.showEquipmentStatus(data, color)
    },
    showComponents(arr) {
      this.$refs.view.showComponents(arr)
    },
    //搜索设备
    seachEquipment(val) {
      this.$refs.routerview.filteraddMarkUpText = val
    },
    //设置模型标签
    addMarkUp(point, dbId) {
      this.$refs.view.addMarkUp(point, dbId, 'clear')


      // if (this.$route.name == 'accident') {
      //   this.accident.forEach(ele => {
      //     this.$refs.view.addMarkUp(ele.forge_data, ele.type)
      //   })
      // }

      // if (this.$route.name == 'tags') {
      //   this.tags.forEach(ele => {
      //     this.$refs.view.addMarkUp(ele.forge_data, ele.type)
      //   })
      // }
    },
    //点击模型获取当前构件数据
    getClickData(json) {
      this.currTag.forge_data = json
      this.$refs.tagEdit.getForgeData_status = 2
    },
    //获取当前模型state
    getState(callback) {
      callback(this.$refs.view.getState())
    },
    getShot(callback) {
      callback(this.$refs.view.getShot())
    },
    showRouter() {
      if (this.$route.name === 'home') {
        this.showRouterView = false
      } else {
        this.showRouterView = true
      }
    },
    //保存标签点
    saveTag() {
      this.tags.push(this.currTag)
      axios.put(this.$store.state.stationList[this.$store.state.currSid]
        .tagDataUrl, this.tags).then(res => {
        this.$message.success('标签保存成功')
        this.$refs.tagEdit.reset()
        this.$store.commit('update_showTagEdit', false)
        this.$store.commit('update_setMarkUp', true)
      })
    },
    //保存客伤
    saveAccident() {
      this.accident.push(this.currTag)
      axios.put(this.$store.state.stationList[this.$store.state.currSid]
          .accidentDataUrl, this.accident).then(res => {
        this.$message.success('标签保存成功')
        this.$refs.tagEdit.reset()
        this.$store.commit('update_showTagEdit', false)
        this.$store.commit('update_setMarkUp', true)
      })
    },
    //保存视角
    savePoint() {
      this.points.push(this.currPoint)
      axios
        .put(
          this.$store.state.stationList[this.$store.state.currSid].pointDataUrl,
          this.points
        )
        .then(res => {
          this.$message.success('视角保存成功')
          this.currPoint = { name: '', state: {}, shot: '' }
          this.$store.commit('update_showPointEdit', false)
        })
    },

    //恢复视角
    restoreState(state) {
      this.$refs.view.restoreState(state)
    },

    //获取当前车站视角列表
    getPointsList() {
      //获取视角列表
      axios
        .get(
          this.$store.state.stationList[this.$store.state.currSid].pointDataUrl
        )
        .then(res => {
          this.points = res.data
        })
    },

    //获取当前车站所有标签
    getTagsList() {
      axios
        .get(
          this.$store.state.stationList[this.$store.state.currSid].tagDataUrl
        )
        .then(res => {
          this.tags = res.data
        })
    },

    //获取当前车站所有客伤信息
    getAccidentList() {
      axios
        .get(
          this.$store.state.stationList[this.$store.state.currSid]
            .accidentDataUrl
        )
        .then(res => {
          this.accident = res.data
        })
    },

    //第一次打开
    init() {
      this.getPointsList()
      this.getTagsList()
      this.getAccidentList()
    },

    //切换车站
    changeStation() {
      //更换模型
      this.$refs.view.loadModel()
      this.getPointsList()
    }
  },
  watch: {
    $route: {
      handler(val, oldval) {
        this.showRouter()
      }
    }
  }
}
</script>
<style lang="less" scoped>
.abs {
  position: absolute;
}
.bim-box {
  position: relative;
  min-height: 100vh;
}

.router-view {
  position: absolute;
  width: 310px;
  max-height: calc(100vh - 150px);
  overflow-y: auto;
  top: 75px;
  left: 20px;
}

.z-1 {
  z-index: 1;
}
.z-2 {
  z-index: 2;
}
.tools {
  position: absolute;
  top: 20px;
  left: 20px;
  box-shadow: 0 2px 2px rgba(0, 0, 0, 0.15);
  border-radius: 3px;
  overflow: hidden;
  background: #fff;
}

.alert {
  position: absolute;
  top: 24.5px;
  right: 20px;
}
.no-click {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  background-color: rgba(250, 250, 250, 0);
  z-index: 9999;
}
</style>
