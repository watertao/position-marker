<template>
  <div style="height: 100%; width: 100%">
    <div id="container"></div>
    <div id="tool">
      <a-button type="primary" @click="showDrawer">员工住址</a-button>
    </div>
    <a-drawer
      :width="500"
      placement="right"
      :closable="false"
      :visible="drawerVisible"
      @close="onDrawerClose"
    >
      <div style="text-align: right; margin-bottom: 16px;">
        <a-button @click="impEmployee">导入员工住址</a-button>
      </div>
      <a-table :pagination="false" :columns="columns" :data-source="data">
        <span slot="coordinate" slot-scope="record">{{ `${record.longitude}, ${record.latitude}` }}</span>
      </a-table>
    </a-drawer>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from 'vue-property-decorator'

const BMap = (window as any).BMap

const columns = [
  {
    dataIndex: 'department',
    title: '部门'
  },
  {
    title: '姓名',
    dataIndex: 'name'
  },
  {
    title: 'Action',
    key: 'action',
    scopedSlots: { customRender: 'action' }
  }
]

const data: Employee[] = [
  { name: 'watertao', department: '开发二部', address: '闵行', longitude: 121.465103, latitude: 31.041891 },
  { name: 'henry', department: '开发二部', address: '北蔡', longitude: 121.560965, latitude: 31.188001 }
]

@Component({
  components: {
  }
})
export default class App extends Vue {
  private map: any
  private drawerVisible = false
  private columns = columns
  private data: Employee[] = []

  @Watch('data')
  onChangeValue (newVal: Employee[]) {
    this.map.clearOverlays()
    newVal.forEach(({ name, department, address, longitude, latitude }) => {
      const point = new BMap.Point(longitude, latitude)
      const marker = new BMap.Marker(point)
      marker.addEventListener('click', () => {
        marker.openInfoWindow(new BMap.InfoWindow(name, {}))
      })
      this.map.addOverlay(marker)
    })
  }

  showDrawer () {
    this.drawerVisible = true
  }

  onDrawerClose () {
    this.drawerVisible = false
  }

  impEmployee () {
    this.data = data
  }

  mounted () {
    this.map = new BMap.Map('container', {
      minZoom: 11,
      maxZoom: 13
    })
    this.map.enableScrollWheelZoom(true)
    const point = new BMap.Point(121.394287, 31.125084)
    this.map.centerAndZoom(point, 12)
    this.map.setMapStyleV2({ styleId: '0d7e3961ec7714c73d72809933896fd6' })
  }
}

export interface Employee {
  name: string
  department: string
  address: string
  // 经度
  longitude: number
  // 纬度
  latitude: number
}
</script>

<style scoped>
  #container {
    width: 100%;
    height: 100%;
    position: relative;
  }
  #tool {
    position: absolute;
    right: 24px;
    top: 24px;
    text-align: right;
  }
</style>
