<template>
  <!--
      	作者：luoyiming
      	时间：2019-06-04
      	描述：插件中心-分销
      -->
  <div class="common-seach-wrap">
    <!--秒杀活动-->
    <Active v-if="activeName == 'active'"></Active>
    <!--基础设置-->
    <Setting v-if="activeName == 'setting'"></Setting>
    <!-- 活动审核 -->
    <Audit v-if="activeName == 'audit'"></Audit>
  </div>
</template>
<script>
import bus from '@/utils/eventBus.js';
import PlusApi from '@/api/plus.js';
import Active from './active/index';
import Setting from './setting/getSetting.vue';
import Audit from './product/index.vue';

export default {
  components: {
    Active,
    Setting,
    Audit
  },
  data() {
    return {
      formInline: {
        nick_name: ''
      },
      /*参数*/
      param: {},
      /*当前选中*/
      activeName: 'active',
      /*切换数组*/
      sourceList: [
        {
          key: 'active',
          value: '秒杀活动',
          path:'/plus/seckill/active/index'
        },
        {
          key: 'setting',
          value: '基础设置',
          path:'/plus/seckill/setting/index'
        },
        {
          key: 'audit',
          value: '活动审核',
          path:'/plus/seckill/product/index'
        },
      ],
      /*权限筛选后的数据*/
      tabList:[],
      /*判断third是否有参数*/
      is_third_param: false
    };
  },
  watch:{

    //监听路由
    $route(to, from) {
      this.init();
    }
  },
  created() {

    this.init();

  },
  beforeDestroy() {
    //发送类别切换
    bus.$emit('tabData', { active: null, tab_type:'seckill',list: [] });
    bus.$off('activeValue');
  },
  methods: {

    /*初始化方法*/
    init(){
      this.tabList=this.authFilter();

      if(this.tabList.length>0){
        this.activeName=this.tabList[0].key;
      }

      if (this.$route.query.type != null) {
        this.activeName = this.$route.query.type;
      }

      /*监听传插件的值*/
      bus.$on('activeValue', res => {
        if (this.is_third_param) {
          this.param.user_id = '';
          this.is_third_param = false;
        }
        this.activeName = res;
      });

      //发送类别切换
      let params = {
        active: this.activeName,
        list: this.tabList,
        tab_type:'seckill'
      };
      bus.$emit('tabData', params);
    },

    /*权限过滤*/
    authFilter(){
      let list=[];
      for(let i=0;i<this.sourceList.length;i++){
        let item=this.sourceList[i];
        if(this.$filter.isAuth(item.path)){
          list.push(item);
        }
      }
      return list;
    }

  }
};
</script>
