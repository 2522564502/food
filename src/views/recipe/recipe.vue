<template>
  <div class="recipe">
    <!-- v-model="activeName" -->
    <!-- 菜谱分类 start -->
    <el-tabs v-model="classifyName" type="border-card" @tab-click='tab'>
      <el-tab-pane
        :label="item.parent_name"
        v-for="item in classify"
        :key="item.parent_type"
        :name="item.parent_type" 
      >
        <div class="recipe-link" >
          <router-link
            :to="{ query: { ...$router.query, classify: option.type } }"
            v-for="option in item.list"
            :key="option.type"
            :class="{ active: classifyType === option.type }"
          >
            {{ option.name }}
          </router-link>
        </div>
      </el-tab-pane>
    </el-tabs>
    <!-- 菜谱分类 end -->


    <h2>家常好味道，给你家一般的温暖</h2>
    <el-container>
      <el-aside width="220px" class="recipe-aside">
        <div class="filter-box">
          <h4>筛选</h4>
          <!-- v-model="activeName" -->
          <!-- 筛选 start -->
          <el-collapse v-model="propertyActiveName">
            <el-collapse-item
              :title="item.parent_name"
              v-for="item in property"
              :key="item.parent_type"
              :name="item.parent_type"
            >
              <div class="filter-tags">
                <el-tag
                  type="info"
                  v-for="option in item.list"
                  :key="option.type"
                  @click="selectedTag(option)"
                  :class="{
                    'tag-selected': propertype[option.title] === option.type,
                  }"
                  >{{ option.name }}</el-tag
                >
              </div>
            </el-collapse-item>
          </el-collapse>
          <!-- 筛选 end -->
        </div>
      </el-aside>
      
      <el-main class="filter-menus-box">
        <div class="menu-empty" v-show="!menus.length&&!loading">暂时没有过滤出菜谱信息，请选择其他的筛选条件</div>
        <menu-card style="min-height: 75%" :info="menus"></menu-card>
        <div style="text-align: right" v-show="!loading">
          <el-pagination
            style="display: inline-block"
            :page-size="5"
            layout="total, prev, pager, next"
            :total="total"
            :current-page.sync='page'
            @current-change='hand'
            :hide-on-single-page='true'
          >
          </el-pagination>
        </div>
      </el-main>
    </el-container>
  </div>
</template>
<script>
import MenuCard from "@/components/menu-card.vue";
import { getClassify, getProperty, getMenus } from "@/service/api";


export default {
  components: { MenuCard },
  data() {
    return {
      classify: [],
      property: [],
      propertype: {}, //存储分类的属性
      classifyType: "1-1", //子级
      classifyName: "1", //刷新之后父级路由参数有，而视图没有变化。
      propertyActiveName: [],
      menus:[],
      loading:false,
      total:0,
      page:1
    };
  },
  watch: {
    $route: {
      handler() {
        const { classify } = this.$route.query;
        if (classify) {
          this.classifyType = classify;
          this.classifyName = classify[0];
          this.Menus()
        }
      },
      immediate: true,
    },
  },
  mounted() {
    getClassify().then((data) => {
      // console.log(data);
      this.classify = data.data;
      //   判断query有没有值
      if (!this.$route.query.classify) {
        this.classifyType = this.classify[0].list[0].parent_type;
        this.classifyName = this.classify[0].parent_type;
        this.$router.push({
          query: {
            classify: this.classifyType,
            page: 1,
          },
        });
      }
    });


    getProperty().then((data) => {
      // console.log(data);
      this.property = data.data;
      const { query } = this.$route;
      this.propertype = this.property.reduce((o, item) => {
        o[item.title] = query[item.title] ? query[item.title] : "";
        if (o[item.title]) {
          this.propertyActiveName.push(o[item.title][0]);
        }
        return o;
      }, {});
      // console.log(this.propertype);
    });


    getMenus().then(({data})=>{
      // console.log(data);
      this.menus=data.list
    })


    
  },
  methods: {
    selectedTag(option) {
      let query = { ...this.$route.query };
      if (this.propertype[option.title] === option.type) {
        this.propertype[option.title] = "";
        delete query[option.title];
      } else {
        this.propertype[option.title] = option.type;
        query[option.title] = option.type;
      }
      this.$router.push({
        query,
      });
    },


    Menus(){
      let query={...this.$route.query}
      let params={
        page:query.page||1,
        classify:query.classify
      }
      delete query.page
      delete query.classify
      if(Object.keys(query).length){
        params.property={
          ...query
        }
      }
      this.loading=true
      let loading=null
      this.$nextTick(()=>{
        loading=this.$loading({
          target:".filter-menus-box",
          text:'loading...',
          spinner:'el-icon-loading',
          background:'rgb(0,0,0,0.6)'


        })
      })
      this.menus=[]
      getMenus(params).then(({data})=>{
      // console.log(data);
      if(loading)loading.close()
      this.loading=false
      this.menus=data.list
      this.total=data.total
      this.page=data.current_page
    })
    },


    hand(){
      this.$router.push({
        query:{
          ...this.$route.query,
          page:this.page
        }
      })
    },


    tab(){
      let classifyName=this.classifyName
      let item=this.classify.find(item=>item.parent_type===classifyName)
      // console.log(item);
      if(item){
        this.classifyName=item.parent_type
        this.classifyType=item.list[0].type
        this.$router.push({
          query:{
            ...this.$route.query,
            classify:this.classifyType
          }
        })
      }
    }


  },
};
</script>
<style lang="stylus">
.recipe-link {
  font-size: 0;
  margin-top: 5px;
 
 
  a {
    display: inline-block;
    font-size: 12px;
    padding: 0px 8px;
    height: 28px;
    line-height: 28px;
  }
 
 
  .active {
    background: #ff3232;
    color: #fff;
  }
}
 
 
.recipe {
  h2 {
    text-align: center;
    line-height: 150px;
  }
 
 
  .el-main {
    padding: 0;
  }
 
 
  .filter-box {
    background: #fff;
    padding: 10px;
    width: 100%;
    float: left;
    box-sizing: border-box;
  }
}
 
 
.filter-tags {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
 
 
  .tag-selected {
    background-color: #ff3232 !important;
    color: #fff !important;
  }
}
 
 
.menu-empty {
  width: 100%;
  text-align: center;
  font-size: 20px;
}
</style>
 
 