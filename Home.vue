<template>
  <div class="home">
    <!--<van-nav-bar title="薪动平台"
                 left-text="返回"
                 left-arrow
                 @click-left="onClickLeft"/>-->
    <van-cell-group style="margin-top:10px">
      <van-cell is-link
                @click="onList()">
        <template slot="title">
          <div class="item">
            <img src="../assets/img/icon-2.png"
                 class="icon">
            <span class="custom-title">关联账户</span>
          </div>
        </template>
      </van-cell>
      <van-cell is-link
                @click="onLucky()">
        <template slot="title">
          <div class="item">
            <img src="../assets/img/icon-3.png"
                 class="icon">
            <span class="custom-title">幸运抽奖</span>
          </div>
        </template>
      </van-cell>
      <!--<van-cell is-link
                @click="onAward()">
        <template slot="title">
          <div class="item">
            <img src="../assets/img/icon-4.png"
                 class="icon">
            <span class="custom-title">我的奖品</span>
          </div>
        </template>
      </van-cell>-->
      <van-cell is-link
                @click="onQuestion()">
        <template slot="title">
          <div class="item">
            <img src="../assets/img/icon-5.png"
                 class="icon">
            <span class="custom-title">调查问卷</span>
          </div>
        </template>
      </van-cell>
      <van-cell is-link
                @click="onTest()">
        <template slot="title">
          <div class="item">
            <img src="../assets/img/icon-6.png"
                 class="icon">
            <span class="custom-title">代发明细</span>
          </div>
        </template>
      </van-cell>
    </van-cell-group>
  </div>
</template>

<script>
  import { NavBar, Cell, CellGroup, Toast } from 'vant'
  import System from '../service/system'

  export default {
    name: 'home',
    components: {
      [NavBar.name]: NavBar,
      [Cell.name]: Cell,
      [CellGroup.name]: CellGroup
    },
    data () {
      return {
        Filter: {},
        userData: {},
        custNo: '',
        custName: '',
        token: '',
        openAcc: '',
        agFlag: ''
      }
    },
    beforeCreate () {
      document.querySelector('body').setAttribute('style', 'background:#f7f7f7')
    },
    created(){
      this.$wx.hideOptionMenu()
    },
    beforeMount () {
    },
    mounted () {
      this.getCustNo()
    },
    methods: {
      getCustNo () {
        this.token = this.getDecodeUrlParam('token')
        this.openAcc = this.getDecodeUrlParam('openAcc')
        var Filter = {}
        Filter.token = this.token
        Filter.openAcc = this.openAcc
        System.getCustNoByToken({ Filter }).then(res => {
          if (res.data.code === 0) {
            this.custNo = res.data.data
            this.custName = res.data.resultsMap.custName
            this.agFlag = res.data.resultsMap.agFlag
            // Toast(res.data.msg)
          } else {
            Toast(res.data.msg)
          }
        }).catch(err => {
          console.log(err)
        })
      },
      onList () {
        // this.$router.push('/List')
        this.$router.push({
          name: 'list',
          params: {
            custNo: this.custNo
          }
        })
      },
      onLucky () {
        // this.$router.push('/Lucky')
        this.$router.push({
          name: 'DrawList',
          params: {
            custNo: this.custNo
          }
        })
      },
      onAward () {
        // this.$router.push('/Award')
        if (this.agFlag === '1') {
          this.$router.push({
            name: 'award',
            params: {
              custNo: this.custNo
            }
          })
        } else {
          Toast('您近期未在我行代发工资11，无法使用该功能')
        }
      },
      onQuestion () {
        // this.$router.push('/QuestionList')
        if (this.agFlag === '1') {
          this.$router.push({
            name: 'QuestionList',
            params: {
              custNo: this.custNo
            }
          })
        } else {
          Toast('您近期未在我行代发工资1，无法使用该功能')
        }
      },
      onTest () {
        // this.$router.push('/AgDetail')
        if (this.agFlag === '1') {
          this.$router.push({
            name: 'AgDetail',
            params: {
              custNo: this.custNo
            }
          })
        } else {
          Toast('您近期未在我行代发工资，无法使用该功能')
        }
      },
      onClickLeft () {
        Toast('返回')
        this.$router.go(-1)
      },
      getDecodeUrlParam (type) {
        var reg = new RegExp('(^|\\?|&)' + type + '=([^&|\\?|$]*)', 'i')
        var r = window.location.search.substr(1).match(reg)
        if (r != null) {
          return unescape(decodeURI(r[2]))
        }
        return null
      },
    }
  }
</script>
<style scoped>
  .home {
    background-color: #f7f7f7;
  }

  .icon {
    width: 24px;
    height: 24px;
    margin-right: 10px;
  }

  .item {
    display: flex;
    flex-direction: row;
    align-items: center;
  }
</style>
