<template>
  <div id="app">
    <input type="text" @change="inputChange">
    <div id="allmap"></div>
    <div id="twomap"></div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      map: null,
      may: null,
      ary: [
        {
          label: '世纪港湾',
          longitude: '119.575014', // 经度
          latitude: '39.945948', // 纬度
        },
        {
          label: '星光大道',
          longitude: '119.575740', // 经度
          latitude: '39.930410', // 纬度
        },{
          label: '海港区华润啤酒厂',
          longitude: '119.641100', // 经度
          latitude: '39.983030', // 纬度
        },{
          label: '海港区天华大厦',
          longitude: '119.599490', // 经度
          latitude: '39.933760', // 纬度
        },{
          label: '火车站东办公楼二期',
          longitude: '119.588880', // 经度
          latitude: '39.963620', // 纬度
        },{
          label: '乐购二期',
          longitude: '119.598050', // 经度
          latitude: '39.932740', // 纬度
        },{
          label: '恒大城五期',
          longitude: '119.584420', // 经度
          latitude: '39.971680', // 纬度
        },{
          label: '港发大厦',
          longitude: '119.561110', // 经度
          latitude: '39.932830', // 纬度
        },{
          label: '全科医生临床培养基地',
          longitude: '119.579836', // 经度
          latitude: '39.975853', // 纬度
        },{
          label: '火车站办公楼二期',
          longitude: '119.588200', // 经度
          latitude: '39.963670', // 纬度
        },{
          label: '西港路如家商旅酒店',
          longitude: '119.566660', // 经度
          latitude: '39.956550', // 纬度
        },{
          label: '海港区安全局',
          longitude: '119.574650', // 经度
          latitude: '39.952380', // 纬度
        },{
          label: '海港区南山技能培训中心',
          longitude: '119.612439', // 经度
          latitude: '39.917233', // 纬度
        },
      ]
    }
  },
  methods: {
    // 获取自己的位置
    startHacking () {
      let that = this;
      var geolocation = new BMap.Geolocation();
      geolocation.getCurrentPosition(function(res){
        if(this.getStatus() == BMAP_STATUS_SUCCESS){
          var mk = new BMap.Marker(res.point);
          that.may = res.point;
          that.locationHandler(res.point.lng, res.point.lat)
        } else {
          console.log('failed'+this.getStatus());
        }
      },{enableHighAccuracy: true})
    },
    // 生成地图
    locationHandler (lng, lat) {
      var map = new BMapGL.Map("allmap");    // 创建Map实例
      var point1 = new BMapGL.Point(lng, lat);
      map.centerAndZoom(point1, 20);  // 初始化地图,设置中心点坐标和地图级别
      map.enableScrollWheelZoom(true);     //开启鼠标滚轮缩放
      // 开启地图模式
      // map.setMapType(BMAP_EARTH_MAP);
      this.map = map;

      // 循环所有坐标，生成坐标点
      this.ary.forEach((item, index)=> {
        this.mapPoint(item, map)
      })
    },
    // 生成坐标点
    mapPoint (point, map) {
      let that = this;
      // 生成经纬度对象
      let pdd = new BMapGL.Point(point.longitude, point.latitude);
      // 生成图标
      // var myIcon = new BMapGL.Icon("./src/assets/addressPin.png", new BMapGL.Size(52, 52));
      // 创建标注:参数1：坐标对象；参数2：坐标点样式
      var marker = new BMapGL.Marker(pdd);
      // 将标注添加到地图中
      map.addOverlay(marker);
      // 窗口对象参数
      var opts = {
        width : 200,     // 信息窗口宽度
        height: 100,     // 信息窗口高度
        title : point.label, // 信息窗口标题
        message:"这是我的位置"
      }
      // 窗口内容
      var sContent =
      "<button id='bt'>进入</button>";
      // 创建信息窗口对象
      var infoWindow = new BMapGL.InfoWindow(sContent, opts);

      marker.addEventListener("click", function(){
        map.openInfoWindow(infoWindow, pdd); //开启信息窗口
        document.getElementById('bt').onclick = that.alertHandler
      });
    },
    alertHandler () {
      alert('哈哈')
    },
    inputChange (event) {
      let that = this;
      var map = new BMap.Map("twomap");    // 创建Map实例
      var point1 = new BMap.Point(this.may.lng, this.may.lat);
      map.centerAndZoom(point1, 10);  // 初始化地图,设置中心点坐标和地图级别
      // 关键字搜索功能
      var local = new BMap.LocalSearch(map, {
        // renderOptions:{map: this.map},
        onSearchComplete: function (res) {
          // 删除一个标注点
          // map.removeOverlay();
          // 删除所有标注点
          that.map.clearOverlays();
          console.log(res)
          let ary = [];
          res.Ir.forEach(item => {
            ary.push({
              label: item.title,
              longitude: item.point.lng, // 经度
              latitude: item.point.lat, // 纬度
            })
          })
          ary.forEach(elem => {
            that.mapPoint(elem, that.map)
          })
        }
      });
      local.search(event.target.value);
    }
  },
  mounted () {
    this.startHacking()
  }
}
</script>

<style scoped>
  #app, #allmap {
    height: 100%;
  }
  #twomap {
    display: none;
  }
</style>
