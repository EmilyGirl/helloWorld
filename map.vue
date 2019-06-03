<!--  调度 -->
<template>
  <!-- 调度记录 -->
  <div id="dispatch">
    <div class="D-wrapper">
      <hl-dialog
        :title="sjTitle"
        bolName="bootInfoAll"
        iconName="iconjingqulvkezhiliushijiantiaoduzhihangguocheng"
        class="disContent"
        @close="closeStep"
      >
        <div slot="content">
          <div class="topBottom lineDispatch">
            <el-steps :active="active" finish-status="success" align-center>
              <el-step
                v-for="(item, index) in handle_list"
                :key="index"
                :title="item.yjbz"
                :description="item.jssj==='null'?'':item.jssj"
                @click.native="step(item,index)"
              ></el-step>
            </el-steps>
          </div>
          <div class="dispatchTitle">
            <div class="dispatchTitle1">领导指令</div>
            <div class="dispatchTitle1">事件过程记录</div>
          </div>
          <div class="dispatchDirective">
            <div class="dispatchDirectiveLeft">
              <div class="delate-step-rt" ref="heightLeft">
                <!-- 箭头 -->
                <div style="position:absolute,top:0;left:0;font-size:18px;">
                  <!-- <i class="el-icon-arrow-up"></i> -->
                </div>
                <el-timeline>
                  <el-timeline-item v-for="(activity,index) in directList" :key="index">
                    <el-card>
                      <h4
                        style="color:#45a1ff"
                      >接收单位&nbsp;:&nbsp;{{activity.dw}}&nbsp;&nbsp;{{activity.clr}}</h4>
                      <p>{{activity.gcjl}}</p>
                      <div class="rhprocessInfo">
                        <span style="color:#45a1ff">{{activity.yabz}}</span>
                        <br>
                        {{activity.sj}}
                      </div>
                    </el-card>
                  </el-timeline-item>
                </el-timeline>
              </div>
            </div>
            <div class="dispatchDirectiveRight">
              <div class="delate-step-rt" ref="heightRight">
                <el-timeline>
                  <el-timeline-item v-for="(activity,index) in processList" :key="index">
                    <el-card>
                      <h4
                        style="color:#45a1ff"
                      >处理单位&nbsp;:&nbsp;{{activity.dw}}&nbsp;&nbsp;{{activity.clr}}</h4>
                      <p>{{activity.gcjl}}</p>
                      <div class="rhprocessInfo">
                        <span style="color:#45a1ff">{{activity.yabz}}</span>
                        <br>
                        {{activity.sj}}
                      </div>
                    </el-card>
                  </el-timeline-item>
                </el-timeline>
                <div class="lineDispatch1"></div>
              </div>
            </div>
          </div>
          <div class="hlDialog-header">
            <h4 class="hlDialog-title">
              <i class="iconfont colorStyle iconjingqulvkezhiliushijiantiaoduzhihangguocheng"></i>
              领导指令
            </h4>
          </div>
          <div class="hlContent">
            <el-input
              type="textarea"
              style="background: transparent;border: none;"
              :rows="4"
              placeholder="请输入领导指令。"
              v-model="formHeader.gcjl"
            ></el-input>
          </div>
        </div>
        <div slot="footer">
          <div class="dispatchFooter">
            <el-button type="primary" @click="sendDispose" :disabled="finishCheck">提交处置信息</el-button>
            <el-button type="primary" @click="closeStep">关闭</el-button>
          </div>
        </div>
      </hl-dialog>
      <!-- 调度执行人员 -->
      <div class="alertInformation alertmonitor">
        <hl-dialog
          title="调度执行操作"
          bolName="alertInformationEdit"
          iconName="icontiaoduzhihangcaozuo"
          class="alertInformationDialog"
          @close="closealarmcz"
        >
          <div slot="content">
            <div class="alertMonitorContent">
              <el-button
                type="primary"
                size="mini"
                class="addButton"
                :disabled="finishCheck"
                @click="addInfo('新增')"
              >新 增</el-button>
              <el-table :data="alarmData" border>
                <el-table-column label="序号" type="index" width="50"></el-table-column>
                <el-table-column prop="ddzl" show-overflow-tooltip label="调度指令"></el-table-column>
                <el-table-column prop="fzrxm" width="60" show-overflow-tooltip label="负责人"></el-table-column>
                <el-table-column prop="lxfs" show-overflow-tooltip label="联系方式"></el-table-column>
                <!-- 执行状态 11未执行  12执行中 13 完成  14作废-->
                <el-table-column prop="zxzt_text" width="70" show-overflow-tooltip label="状态"></el-table-column>
                <el-table-column label="操作" width="240">
                  <template slot-scope="scope">
                    <div v-if="scope.row.zxzt == '13'"></div>
                    <div v-else-if="scope.row.zxzt == '14'">
                      <el-button
                        type="primary"
                        size="mini"
                        class="handleButton"
                        @click="editInfo('编辑',scope.row)"
                      >编 辑</el-button>
                    </div>
                    <div v-else>
                      <el-button
                        type="primary"
                        size="mini"
                        class="handleButton"
                        @click="editInfo('编辑',scope.row)"
                      >编 辑</el-button>
                      <el-button
                        type="primary"
                        size="mini"
                        class="handleButton"
                        @click="sendMobile('发送',scope.row)"
                      >短 信</el-button>

                      <el-button
                        type="primary"
                        size="mini"
                        class="handleButton"
                        @click="sendSuccess(scope.row)"
                      >完 成</el-button>
                    </div>
                  </template>
                </el-table-column>
              </el-table>
              <div class="page">
                <el-pagination
                  :page-size="pageSize"
                  layout="total, prev, pager, next, jumper"
                  :total="total"
                  :current-page="pageNo"
                  @current-change="currentChange"
                ></el-pagination>
              </div>
            </div>
          </div>
          <div slot="footer">
            <el-button type="primary" @click="closealarmcz">关闭</el-button>
          </div>
        </hl-dialog>
      </div>
      <!-- 地图打点信息 -->
      <hl-dialog bolName="mapInfo" class="mapInfoDialog" @close="closemap">
        <div slot="header" class="header">
          <h4 class="hlDialog-title">
            <i class="iconfont colorStyle" :class="iconName" style="transform: scale(1.3);"></i>
            {{mapPointType||mapDetail.yjfl || mapDetail.sbmc || ""}}
          </h4>
        </div>
        <div slot="content" class>
          <div class="mapInfo" style="min-height:220px">
            <div v-if="mapPointType =='景 区'">
              <h3>{{mapDetail.sjlx_text}}>{{mapDetail.sjbt}}</h3>
              <h4>发生时间：{{mapDetail.fssj}}</h4>
              <div class="detail-wrapper">
                <div class="mapInfoLeft">
                  <h4>位置：[{{mapDetail.jd}},{{mapDetail.wd}}]</h4>
                  <h5>事件描述：{{mapDetail.sjms}}</h5>
                </div>
                <div class="mapInfoRight">
                  <img :src="mapDetail.img" class="detail-img">
                </div>
              </div>
            </div>
            <div v-if="mapPointType =='物资装备'">
              <p>
                <span class="label">一级分类：</span>
                <span class="text">{{mapDetail.yjfl || ""}}</span>
              </p>
              <p>
                <span class="label">二级分类：</span>
                <span class="text">{{mapDetail.ejfl || ""}}</span>
              </p>
              <p>
                <span class="label">存储单位：</span>
                <span class="text">{{mapDetail.ccdw || ""}}</span>
              </p>
              <p>
                <span class="label">存储地点：</span>
                <span class="text">{{mapDetail.ccdd || ""}}</span>
              </p>
              <p>
                <span class="label">存储规格：</span>
                <span class="text">{{mapDetail.ccgg || ""}}</span>
              </p>
              <p>
                <span class="label">数量：</span>
                <span class="text">{{mapDetail.sl || ""}}</span>
              </p>
              <p>
                <span class="label">单位：</span>
                <span class="text">{{mapDetail.dw || ""}}</span>
              </p>
              <p>
                <span class="label">负责人：</span>
                <span class="text">{{mapDetail.fzr || ""}}</span>
              </p>
              <p>
                <span class="label">联系电话：</span>
                <span class="text">{{mapDetail.lxdh || ""}}</span>
              </p>
              <p>
                <span class="label">经度：</span>
                <span class="text">{{mapDetail.jd || ""}}</span>
              </p>
              <p>
                <span class="label">纬度：</span>
                <span class="text">{{mapDetail.wd || ""}}</span>
              </p>
              <p v-if="mapDetail.zt=='0'">
                <span class="label">状态：</span>
                <span class="text">禁用</span>
              </p>
              <p v-if="mapDetail.zt=='1'">
                <span class="label">状态：</span>
                <span class="text">启用</span>
              </p>
            </div>
            <div v-if="mapPointType =='摄像头'">
              <h3>设备类型：{{mapDetail.sblx}}</h3>
              <h3>设备名称：{{mapDetail.sbmc}}</h3>
              <div class="detail-wrapper">
                <div class="mapInfoLeft">
                  <h4>ip:{{mapDetail.ip}}</h4>
                  <h4>位置：[{{mapDetail.jd}},{{mapDetail.wd}}]</h4>
                  <h4>区域：{{mapDetail.qy}}</h4>
                </div>
                <div class="mapInfoRight">
                  <img :src="mapDetail.img" class="detail-img">
                </div>
              </div>
            </div>
          </div>
        </div>
        <div slot="footer">
          <el-button type="primary" class="buttonmap" size="mini" @click="closemap">关闭</el-button>
        </div>
      </hl-dialog>
      <!-- 地图上专家库 物资装备 摄像头 -->
      <!-- 图例 -->
      <div class="legendBox">
        <div
          class="legend-item"
          v-for="(item,i) in legendList"
          :key="i"
          :class="{active:item.active}"
          @click="legendClick(item,i)"
        >
          <div class="legend-item-text">
            <i :class="['iconfont',item.icon]"></i>
          </div>
          <div class="legend-item-text">{{item.title}}</div>
        </div>
      </div>
      <el-button
        class="abbresiveStep"
        type="primary"
        v-show="this.$store.state.bootInfoAll==false"
        @click="showbootInfo"
      >执行过程</el-button>
      <el-button
        class="abbresiveEdit"
        type="primary"
        v-show="this.$store.state.alertInformationEdit==false"
        @click="showInformationEdit"
      >执行操作</el-button>
    </div>
    <div class="disClose" @click="goPrev">
      <i class="el-icon-close"></i>
    </div>
    <ao-map></ao-map>
    <edit @refresh="getgoIngList(1)"></edit>
    <sendMobile></sendMobile>
    <allFinish @refresh="getDispatchstep()" @reIngList="getgoIngList(1)" @reProcess="getProcess()"></allFinish>
    <alertZjk></alertZjk>
    <alertHandle @refresh="getProcess()" @dispatStep="getDispatchstep()"></alertHandle>
  </div>
</template>

<script>
import { mapState } from "vuex";
import edit from "./edit";
import sendMobile from "./sendMobile";
import allFinish from "./allFinish";
import AoMap from "@/components/common/AMap";
import alertZjk from "./alertZjk";
import alertHandle from "./alertHandle";
export default {
  name: "",
  components: {
    edit,
    sendMobile,
    allFinish,
    AoMap,
    alertZjk,
    alertHandle
  },
  data() {
    return {
      groupList: [],
      userInfo: {},
      sjTitle: "",
      finishCheck: false,
      sjId: "", //事件执行ID
      // 执行操作分页数据
      pageNo: 1,
      pageSize: 10,
      total: 0,
      pageCount: 0,
      iconName: "",
      alarmData: [],
      reserve: false,
      textarea: "",
      active: 0,
      title: "",
      carryId: "",
      legendList: [
        { icon: "iconexpert-library", title: "专家库", active: false },
        { icon: "iconwuzizhuangbei", title: "物资装备", active: true },
        { icon: "iconxianchangshipin", title: "摄像头", active: true },
        { icon: "iconlukuangxinxi1", title: "路况", active: true }
      ],
      isCheckWZ: true,
      mapTitle: "",
      formHeader: {
        gcjl: "",
        zxId: "",
        sjbh: ""
      },
      processList: [],
      form: {},
      handle_list: [],
      directList: [],
      completeSuccess: "",
      allFiniId: "",
      mapCenter: [],
      mapPoint: {
        locate: [],
        detail: {
          img: require("@/assets/image/mapImg/map_eg.png"),
          sjlx_text: "",
          sjbt: "",
          fssj: "",
          sjms: "",
          jd: "",
          wd: "",
          jq: ""
        }
      },
      trafficLayer: "",
      mapMarker: {},
      tabmapMarker: [],
      mapDetail: {},
      mapPointType: "",
      wzList: [],
      cameraList: [],
      mapIcon2: {
        // 图标尺寸
        size: new AMap.Size(37, 43),
        // 图标的取图地址
        image: require("@/assets/image/mapImg/wzMap.png"),
        // // 图标所用图片大小
        imageSize: new AMap.Size(37, 43)
      },
      mapIcon1: {
        // 图标尺寸
        size: new AMap.Size(37, 43),
        // 图标的取图地址
        image: require("@/assets/image/mapImg/zxMap.png"),
        // // 图标所用图片大小
        imageSize: new AMap.Size(37, 43)
      }
    };
  },
  created() {
    this.hldialog();
    let sjId = this.$route.query.sjid;
    this.sjTitle = this.$route.query.sjbt + "调度执行过程";
    // this.sjId = "20190419";
    this.sjId = sjId;
    this.getDispatchstep();
    this.getProcess();
    this.getDirect();
    this.getgoIngList(1);
  },
  computed: {
    ...mapState({
      map: state => state.map.map
    })
  },
  watch: {},
  mounted() {
    this.getDetails();
    this.getDistData();
    let promise1 = this.getCameraList();
    let promise2 = this.getGoodsList();
    // 获取完数据在打点
    Promise.all([promise1, promise2]).then(() => {
      this.addPoint(this.cameraList, this.mapIcon1, "摄像头");
      this.addPoint(this.wzList, this.mapIcon2, "物资装备");
    });
    // 实时路况
    this.trafficLayerNow();
  },
  methods: {
    goPrev() {
      this.$router.go(-1);
    },
    getDistData() {},
    //事件详情信息打点
    getDetails() {
      this.axios({
        url: "/dispatch/eventDispatch/getDispatchInfo",
        method: "post",
        data: { sjbh: this.sjId }
      })
        .then(res => {
          let data = res.data.resultData || {};
          this.mapCenter = [parseFloat(data.jd), parseFloat(data.wd)];
          this.mapPoint.locate = this.mapCenter;
          this.mapPoint.detail.sjlx_text = data.sjlx_text;
          this.mapPoint.detail.sjbt = data.sjbt;
          this.mapPoint.detail.fssj = data.fssj;
          this.mapPoint.detail.jd = data.jd;
          this.mapPoint.detail.wd = data.wd;
          this.mapPoint.detail.sjms = data.sjms;
          this.mapPoint.detail.type = "景 区";

          //  地图渲染打点
          this.getEventPoint();
        })
        .catch(err => console.error(err));
    },
    getEventPoint() {
      this.mapMarker = new AMap.Marker({
        position: new AMap.LngLat(...this.mapPoint.locate),
        map: this.map,
        extData: this.mapPoint.detail
      });
      // var circleMarker = new AMap.CircleMarker({
      //   center: this.mapPoint.locate,
      //   radius: 60, //3D视图下，CircleMarker半径不要超过64px
      //   strokeColor: "#c87a9b",
      //   strokeWeight: 2,
      //   strokeOpacity: 0.5,
      //   fillColor: "rgba(220,46,101,0.4)",
      //   fillOpacity: 0.5,
      //   zIndex: 10,
      //   bubble: true,
      //   cursor: "pointer",
      //   clickable: true
      // });
      // circleMarker.setMap(this.map);
      var circle = new AMap.Circle({
        center: new AMap.LngLat(...this.mapPoint.locate),
        radius: 6000, //半径
        borderWeight: 3,
        strokeColor: "#49a3fe",
        strokeOpacity: 1,
        strokeWeight: 6,
        strokeOpacity: 0.2,
        fillOpacity: 0.4,
        strokeStyle: "dashed",
        // strokeDasharray: [10, 10],
        // 线样式还支持 'dashed'
        fillColor: "#1791fc",
        zIndex: 50
      });

      circle.setMap(this.map);
      this.map.setFitView([circle]);
      this.onPointClick();
    },
    onPointClick() {
      this.mapMarker.on("click", e => {
        this.iconName = "iconjingdian";
        this.$store.state.mapInfo = true;
        this.mapPointType = "景 区";
        this.map.panTo(new AMap.LngLat(...this.mapPoint.locate));
        this.map.setZoomAndCenter(14, this.mapPoint.locate);
        e.target.setAnimation("AMAP_ANIMATION_BOUNCE");
        // this.map.setFitView();
        this.mapDetail = e.target.getExtData();
      });
    },
    //路况信息
    trafficLayerNow() {
      //实时路况图层
      this.trafficLayer = new AMap.TileLayer.Traffic({
        zIndex: 10
      });
      this.trafficLayer.setMap(this.map);
    },
    //图例点击
    legendClick(item, i) {
      if (item.active) {
        item.active = false;
        {
          let currentMarker = this.tabmapMarker.filter(
            subItem => subItem.getExtData().title == item.title
          );
          currentMarker.forEach(subItem => {
            subItem.hide();
          });
        }
        switch (i) {
          case 0:
            this.$store.state.eventZjk = false;
            break;
          case 3:
            this.trafficLayer.hide();
            break;
          default:
            break;
        }
      } else {
        item.active = true;
        switch (i) {
          case 0:
            this.$bus.emit("alertZjk", "专家库", this.sjId);
            this.$store.state.eventZjk = true;
            break;
          case 1:
            let promise2 = this.getGoodsList();
            // 获取完数据在打点
            promise2.then(() => {
              this.addPoint(this.wzList, this.mapIcon2, "物资装备");
            });
            break;
          case 2:
            let promise1 = this.getCameraList();
            // 获取完数据在打点
            promise1.then(() => {
              this.addPoint(this.cameraList, this.mapIcon1, "摄像头");
            });
            break;
          case 3:
            this.trafficLayer.show();
            break;
          default:
            break;
        }
        this.closemap();
      }
    },
    //获取物资列表
    getGoodsList() {
      return new Promise(resolve => {
        this.axios({
          url: "/dispatch/eventDispatch/getMaterials",
          method: "post",
          data: { sjbh: this.sjId }
        }).then(res => {
          let data = res.data.resultData || [];
          this.wzList = data;
          resolve();
        });
      });
    },
    //获取摄像头列表
    getCameraList() {
      return new Promise(resolve => {
        this.axios({
          url: "/dispatch/eventDispatch/getCameras",
          method: "post",
          data: { sjbh: this.sjId }
        }).then(res => {
          let data = res.data.resultData || [];
          this.cameraList = data;
          resolve();
        });
      });
    },
    //地图 添加点位信息
    addPoint(list, icon, title) {
      let mapIcon = new AMap.Icon(icon);
      list.forEach((element, i) => {
        let marker = new AMap.Marker({
          map: this.map,
          position: [element.jd, element.wd],
          icon: mapIcon,
          offset: new AMap.Pixel(-13, -30),
          extData: { title, element }
        });
        //点位点击事件
        marker.on("click", e => {
          this.closemap();
          this.$store.state.mapInfo = true;
          e.target.setAnimation("AMAP_ANIMATION_BOUNCE");
          this.map.setZoomAndCenter(14, [element.jd, element.wd]);
          this.mapDetail = e.target.getExtData().element;
          this.mapPointType = e.target.getExtData().title;
        });
        this.tabmapMarker.push(marker);
        this.map.setFitView();
      });
    },
    removeMarkers() {
      this.map.remove(this.tabmapMarker);
    },

    //调度执行操作表分页
    currentChange(currentPage) {
      this.pageNo = currentPage;
      this.getgoIngList(currentPage || 1);
    },
    // 调度执行操作列表
    getgoIngList(page) {
      let postData = {
        sjbh: this.sjId,
        pageNo: page,
        pageSize: 5
      };
      this.axios({
        url: "/dispatch/tbDdzxcz/getPageData",
        method: "post",
        data: postData
      })
        .then(res => {
          let data = res.data.resultData || {};
          this.alarmData = data.list;
          this.total = data.total;
          this.pageNo = data.pageNum;
          this.pageCount = data.pages;
          this.pageSize = data.pageSize;
        })
        .catch(err => console.error(err));
    },
    // 事件过程记录
    getProcess() {
      let postData = {
        sjbh: this.sjId
      };
      this.axios({
        url: "/dispatch/eventDispatch/getProcessRecords",
        method: "post",
        data: postData
      })
        .then(res => {
          // console.log("事件过程记录", res);
          let data = res.data.resultData || {};
          this.processList = data;
        })
        .catch(err => console.error(err));
    },
    // 领导指令
    getDirect() {
      let postData = {
        sjbh: this.sjId
      };
      this.axios({
        url: "/dispatch/eventDispatch/getLeaderInstructions",
        method: "post",
        data: postData
      })
        .then(res => {
          // console.log("领导指令", res);
          let data = res.data.resultData || {};
          this.directList = data;
        })
        .catch(err => console.error(err));
    },
    // 提交处置信息
    sendDispose() {
      this.formHeader.sjbh = this.sjId;
      let postData = this.formHeader;
      if (this.formHeader.gcjl == "") {
        this.$message({
          type: "warning",
          message: "请填写领导指令记录"
        });
      } else {
        this.axios({
          url: "/dispatch/eventDispatch/saveLeaderInstruction",
          method: "post",
          data: postData
        })
          .then(res => {
            let data = res.data || {};
            if (data.resultFlag == true) {
              this.$message({
                type: "success",
                message: res.data.resultMsg
              });
              this.getDirect();
              this.formHeader.gcjl = "";
            } else {
              this.$message.error(res.data.resultMsg);
            }
          })
          .catch(err => console.error(err));
      }
    },
    // 执行步骤过程接口
    getDispatchstep() {
      let postData = {
        sjbh: this.sjId
      };
      this.axios({
        url: "/dispatch/tbDdzxgc/getAllData",
        method: "post",
        data: postData
      })
        .then(res => {
          let data = res.data.resultData || {};
          this.handle_list = data;
          // console.log(this.handle_list, 514);
          for (let i = 0; i < this.handle_list.length; i++) {
            if (this.handle_list[i].zt == "02") {
              this.active = i;
              this.allFiniId = this.handle_list[i].id;
              this.formHeader.zxId = this.handle_list[i].id;
            } else if (
              this.handle_list[i].zt !== "02" &&
              this.handle_list[i].zt !== "01"
            ) {
              this.active = this.handle_list.length;
            }
            if (this.handle_list[4].zt == "03") {
              this.finishCheck = true;
            }
          }
        })
        .catch(err => console.error(err));
    },
    // 步骤条启动中点击事件
    step(item, index) {
      // 01 未完成  02启动中 03 已完成
      // 判断当前进程是否是启动中
      // 当前调度执行中id
      this.carryId = item.id;
      if (item.zt == "02") {
        this.$bus.emit(
          "alertHandle",
          "事件处理",
          this.carryId,
          item.sjbh,
          item.id
        );
        this.$store.state.alertHandle = true;
      } else {
        return;
      }
    },
    // 执行操作表完成
    sendSuccess(item) {
      let postData = {
        id: item.id
      };
      this.axios({
        url: "/dispatch/tbDdzxcz/completeExecutorOperate",
        method: "post",
        data: postData
      })
        .then(res => {
          let resultFlag = res.data.resultFlag;
          let resuleData = res.data.resultData;
          if (resuleData.complete == true) {
            this.completeSuccess = resuleData.desc;
            this.$bus.emit("allFinish", this.completeSuccess, this.allFiniId);
            this.$store.state.goNextAll = true;
          } else {
            if (resultFlag == true) {
              this.$message({
                type: "success",
                message: res.data.resultMsg
              });
              this.getgoIngList(1);
            } else {
              this.$message.error("错了哦，这是一条错误消息");
            }
          }
        })
        .catch(err => console.error(err));
    },

    // 打开调度过程弹框和执行操作弹框
    hldialog() {
      (this.$store.state.bootInfoAll = true),
        (this.$store.state.alertInformationEdit = true);
    },
    // 关闭执行操作弹框
    closealarmcz() {
      this.$store.state.alertInformationEdit = false;
    },
    closeStep() {
      this.$store.state.bootInfoAll = false;
    },
    closemap() {
      this.$store.state.mapInfo = false;
      this.iconName = "";
      this.mapPoint.detail.type = "";
      this.tabmapMarker.forEach(item => {
        item.setAnimation("AMAP_ANIMATION_NONE");
      });
      this.mapMarker.setAnimation("AMAP_ANIMATION_NONE");
    },
    // 新增调度执行操作
    addInfo(title, obj) {
      this.title = title;
      this.$bus.emit("edit", title, obj, this.sjId);
      this.$store.state.addDispatch = true;
    },
    editInfo(title, item) {
      this.title = title;
      this.$store.state.addDispatch = true;
      this.$bus.emit("edit", title, item);
      this.$store.state.addDispatch = true;
    },
    // 短信发送
    sendMobile(title, item) {
      this.title = title;
      this.$bus.emit("sendMobile", title, item);
      this.$store.state.sendDispatch = true;
    },
    showbootInfo() {
      this.$store.state.bootInfoAll = !this.$store.state.bootInfoAll;
    },
    showInformationEdit() {
      this.$store.state.alertInformationEdit = !this.$store.state
        .alertInformationEdit;
    }
  },
  beforeDestroy() {}
};
</script>
 <style  lang="scss">
#dispatch {
  position: relative;
  .mapInfoDialog {
    left: 43%;
    top: 275px;
    cursor: pointer;
    width: 300px;
    min-height: 325px;
    .detail-wrapper {
      display: flex;
      justify-content: space-between;
      .detail-img {
        width: 140px;
        height: 170px;
      }
    }
  }
  .icontiaoduzhihangcaozuo {
    font-size: 35px;
  }
  .iconjingqulvkezhiliushijiantiaoduzhihangguocheng {
    font-size: 35px;
  }
  .el-form-item--mini.el-form-item,
  .el-form-item--small.el-form-item {
    margin-bottom: 15px;
  }
  .buttonmap {
    margin: 0 0 10px 0;
    float: right;
  }
  .tour {
    display: inline-block;
    margin-right: 30px;
  }
  .addButton {
    margin-bottom: 20px;
  }
  .topBottom1 {
    margin-bottom: 10px;
  }
  .tourImg {
    width: 100%;
    height: 150px;
    background: #666666;
  }
  // .iconfont {
  //   font-size: 38px;
  // }
  .el-form-item__content {
    margin-right: 5px;
  }
  .disClose {
    position: fixed;
    right: 12px;
    top: 87px;
    cursor: pointer;
    font-size: 33px;
    z-index: 111;
    color: #666666;
  }
  .abbresiveStep {
    position: fixed;
    right: 20px;
    bottom: 50px;
    cursor: pointer;
    box-shadow: 0 0 4px #cccccc;
    z-index: 111;
  }
  .abbresiveEdit {
    position: fixed;
    right: 20px;
    bottom: 90px;
    cursor: pointer;
    box-shadow: 0 0 4px #cccccc;
    z-index: 111;
  }
  .rhprocessInfo {
    position: absolute;
    top: 0;
    left: -95px;
    width: 80px;
    height: 100px;
  }
  .legendBox {
    overflow: hidden;
    z-index: 99;
    text-align: center;
    position: fixed;
    left: 41%;
    bottom: 20px;
    height: 80px;
    background: #454e5e;
    .legend-item {
      width: 98px;
      height: 80px;
      float: left;
      color: #ffffff;
      cursor: pointer;
      &.active {
        background: #49a3fe;
        .legend-item-text {
          height: 40px;
          line-height: 50px;
        }
      }
      .iconfont {
        font-size: 38px;
        transform: scale(1.3);
      }
    }
  }
  .disContent {
    width: 1000px;
    cursor: pointer;
    left: 10px;
    top: 20px;
    // padding: 0 20px;
  }
  .page {
    text-align: center;
  }
  .alertInformationDialog {
    width: 700px;
    top: 20px;
    right: 40px;
    cursor: pointer;
    .hlDialog-footer {
      overflow: hidden;
      padding: 20px 0 0 10px;
      margin: 10px 0;
      button {
        float: right;
        margin-right: 15px;
      }
    }
  }

  .lineDispatch:after {
    content: "";
    display: block;
    width: 100%;
    height: 1px;
    margin-top: 30px;
    background: linear-gradient(to right, transparent, #bcbcbd, transparent);
  }
  .lineDispatch1 {
    width: 1px;
    height: 100%;
    background: linear-gradient(to bottom, transparent, #bcbcbd, transparent);
    position: absolute;
    left: -185px;
    top: 0;
  }
  .el-step__head.is-success {
    color: #45a1ff;
    border-color: #45a1ff;
  }
  .el-step__title.is-success {
    color: #45a1ff;
  }
  .el-step__description.is-success {
    color: #45a1ff;
  }
  .delate-step-lt {
    width: 80px;
    color: #818181;
    font-size: 13px;
    display: table-cell;
    position: relative;
  }
  .delate-time {
    position: absolute;
  }
  .delate-step-rt {
    margin-left: 100px;
    // display: table-cell;
    position: relative;
  }
  .dispatchDirective {
    overflow-x: hidden;
    overflow-y: scroll;
    max-height: 200px;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    padding: 0 36px;
  }
  .dispatchDirectiveLeft {
    width: 40%;
  }
  .dispatchDirectiveRight {
    width: 40%;
  }
  .el-timeline-item__timestamp.is-bottom {
    color: transparent;
  }
  .topBottom {
    margin-top: 20px;
    margin-bottom: 20px;
  }
  .hlContent {
    .el-textarea__inner {
      background: transparent;
      border: none;
      margin-top: 20px;
    }
  }
  .dispatchFooter {
    float: right;
    margin: 10px 0;
  }
  .el-card.is-always-shadow,
  .el-card.is-hover-shadow:focus,
  .el-card.is-hover-shadow:hover {
  }
  .dispatchTitle {
    overflow: hidden;
    font-weight: bold;
    margin-top: 20px;
    margin-bottom: 20px;
    .dispatchTitle1 {
      float: left;
      width: 50%;
      text-align: center;
    }
  }
  .el-timeline-item__node {
    border: 1px solid #45a1ff;
    background: #ffffff;
  }
  .dispatchTab {
    overflow: hidden;
    text-align: center;
    .dispatchTab1 {
      width: 80px;
      height: 80px;
      float: left;
      color: #ffffff;
      .dispatchText {
        height: 40px;
        line-height: 50px;
      }
    }
  }
}
</style>
