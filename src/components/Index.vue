<template>
  <div class="main" v-if="status>0">
    <header-self @typeText="okChange($event)" @indexText="indexChange($event)" @moneyText="moneyChange($event)"></header-self>
    <main-table :type="type" :main="main" :mainage="mainage" :mainagetotal="mainagetotal" :money="money" :status="status" v-if="index===0"></main-table>
    <main-chart :type="type" :main="main" :mainage="mainage" :mainagetotal="mainagetotal" :money="money" :status="status" v-if="index===1"></main-chart>
    <div class="bg"></div>
  </div>
</template>

<style>
.bg {
  z-index: -2;
  background: #efefef;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>

<script>
import HeaderSelf from './Header';
import MainTable from './MainTable';
import MainChart from './MainChart';
export default {
  components: {
    HeaderSelf,
    MainTable,
    MainChart,
  },
  beforeMount: function() {
    var _this = this;
    this.email = this.$route.query.email;
    this.token = this.$route.query.token;
    this.$http.get("http://wxwebapi.aukeyit.com/api/checkUser?email=" + _this.email + "&token=" + _this.token).then(function (resp) {
      if(resp.data.data) {
        _this.$http.get("http://purchase-brand.aukeyit.com/api/stock?type=1").then(function (resp) {
          if (resp.data.code == 200) {
            _this.main = resp.data.data;
            _this.status++;
          }
        }).catch(function(resp) {
          _this.$vux.toast.text('加载失败', 'middle');
        });
        _this.$http.get("http://purchase-brand.aukeyit.com/api/age?type=1").then(function (resp) {
          if (resp.data.code == 200) {
            _this.mainage = resp.data.data;
            _this.status++;
          }
        }).catch(function(resp) {
          _this.$vux.toast.text('加载失败', 'middle');
        });
        _this.$http.get("http://purchase-brand.aukeyit.com/api/all").then(function (resp) {
          if (resp.data.code == 200) {
            _this.mainagetotal = resp.data.data;
          }
        }).catch(function(resp) {
          _this.$vux.toast.text('加载失败', 'middle');
        });
      } else {
        _this.$vux.toast.text('您没有权限访问资源', 'middle');
      }
    }).catch(function(resp) {
      _this.$vux.toast.text('加载失败', 'middle');
    });
  },
  data() {
    return {
      type: 0, /*顶部切换*/
      index: 0, /*图表切换*/
      money: 0, /*类型切换*/
      status: 0, /*加载状态切换*/
      main: {},
      mainage: {},
      mainagetotal: {},
      email: '',
      token: ''
    }
  },
  watch: {
    money: function() {
      var _this = this;
      this.$http.get("http://purchase-brand.aukeyit.com/api/stock?type=" + (_this.money + 1)).then(function (resp) {
        if (resp.data.code == 200) {
          _this.main = resp.data.data;
          _this.status++;
        }
      }).catch(function(resp) {
        _this.$vux.toast.text('加载失败', 'middle');
      });
      this.$http.get("http://purchase-brand.aukeyit.com/api/age?type=" + (_this.money + 1)).then(function (resp) {
        if (resp.data.code == 200) {
          _this.mainage = resp.data.data;
          _this.status++;
        }
      }).catch(function(resp) {
        _this.$vux.toast.text('加载失败', 'middle');
      });
    }
  },
  methods: {
    okChange: function (res) {
      this.type = res
    },
    indexChange: function (res) {
      this.index = res
    },
    moneyChange: function (res) {
      this.money = res
    }
  }
}
</script>
