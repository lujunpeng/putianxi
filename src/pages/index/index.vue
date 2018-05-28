<template>
  <div class="putianxi">
    <map id="map" class="putianxi-map" :longitude="longitude" :latitude="latitude" scale="13" :markers="markers" @updated="bindupdated"></map>
    <div class="putianxi-foot">
      <div>@2018 Get out PuTianXi</div>
      <div>抵制莆田 人人有责</div>
      <div class="putianxi-source" @click="setClipboardData">数据来源：
        <span>https://github.com/open-power-workgroup/Hospital</span>
      </div>
    </div>
  </div>
</template>

<script>
import { data } from './data'
export default {
  data() {
    return {
      longitude: '',
      latitude: ''
    }
  },
  computed: {
    markers: function() {
      const list = []
      for (let index = 0; index < data.features.length; index++) {
        const item = data.features[index]
        list.push({
          iconPath: '/static/images/marker-icon.png',
          id: parseInt(item.properties.id),
          latitude: parseFloat(item.geometry.coordinates[1]),
          longitude: parseFloat(item.geometry.coordinates[0]),
          width: 20,
          height: 30,
          title: `名称：${item.properties.name}\n地址：${
            item.properties.address
          }`
        })
      }
      return list
    }
  },
  mounted() {
    this.getLocation()
    wx.showLoading({
      title: '加载中'
    })
  },
  methods: {
    getLocation() {
      const that = this
      wx.getLocation({
        type: 'wgs84',
        success: function(res) {
          that.latitude = res.latitude
          that.longitude = res.longitude
        }
      })
    },
    bindupdated() {
      wx.hideLoading()
    },
    // 复制
    setClipboardData() {
      const data = 'https://github.com/open-power-workgroup/Hospital'
      wx.setClipboardData({
        data,
        success: function(res) {}
      })
    }
  },
  // 分享
  onShareAppMessage(res) {
    if (res.from === 'button') {
      // 来自页面内转发按钮
      console.log(res.target)
    }
    return {
      title: '滚蛋吧！莆田系',
      path: '/pages/index/main',
      success(res) {
        // 转发成功
      },
      fail(res) {
        // 转发失败
      }
    }
  }
}
</script>

<style scoped lang="scss" rel="stylesheet/scss">
.putianxi-map {
  position: absolute;
  width: 100%;
  top: 0;
  bottom: 80px;
  height: auto;
}
.putianxi-foot {
  z-index: 10;
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 80px;
  font-size: 14px;
  color: #999;
  text-align: center;
  display: flex;
  flex-direction: column;
  justify-content: center;
  .putianxi-source {
    font-size: 11px;
    span {
      text-decoration: underline;
    }
  }
}
</style>
