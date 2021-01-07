<template>
<div class="home" v-loading="loading">
  <header-com :isShowSearch="false"></header-com>
  <div class="h_home">
    <div class="banner">
      <div class="c_banner">
        <h1 class="title_1">Listen to the most authentic voice of users</h1>
        <h2 class="title_2">Read reviews. Write reviews. Find businesses.</h2>
        <div class="b_search">
          <el-input class="s_input" v-model="searchData" placeholder="Search for a company or category…"></el-input>
          <el-button class="s_button" type="primary" icon="el-icon-search" @click="handleSearch">Search</el-button>
        </div>
        <h2 class="title_3">Browse products by category</h2>
        <div class="b_type">
          <div class="type_card" v-for="(item,index) in hotType" :key="item.id" @click="handleProList(item)">
            <div class="type_card_icon">
              <svg-icon value="icon-fenlei" :size="1.6"></svg-icon>
            </div>
            <div class="type_card_text">{{item.name}}</div>
          </div>
          <div class="type_card" @click="goCategories">
            <div class="type_card_icon">
              <svg-icon value="icon-fenlei" :size="1.6"></svg-icon>
            </div>
            <div class="type_card_text">More</div>
          </div>
        </div>
      </div>
    </div>
    <div class="recent_reviews pc" @click="clickCard($event)">
      <div class="r_r_title">
        Recent reviews
        <el-divider><svg-icon value="icon-Downxiangxia35" :size="1.6"></svg-icon></el-divider>
      </div>
      <vue-seamless-scroll v-if="hotReview && hotReview.length>0" :data="hotReview" :class-option="defaultOption" class="seamless-warp">
        <div class="r_r_reviews" >
          <div class="r_r_r_card" v-for="(item,index) in hotReview" :key="index">
            <div class="r_r_r_c_card" v-for="(review,ind) in item" :key="ind">
              <svg-icon value="icon-tubiaozhizuomoban" class="icon_hot" :size="1.5"></svg-icon>
              <div class="c_title">
                <el-avatar class="c_t_img" size="large" :src="review.Icon"></el-avatar>
                <rate
                  class="c_t_rate"
                  :value="review.Rank"
                  :isDisabled="true"
                >
                </rate>
              </div>
              <p class="c_user">{{review.Name}} <span class="rev">reviewed</span> <span :data-pro="JSON.stringify(review)" :id="review.ComentId"  class="pro">{{review.ProName}}</span></p>
              <p class="c_text">
                {{review.Content}}
              </p>
            </div>
          </div>
        </div>
      </vue-seamless-scroll>
      <empty v-else :tips="'No hot comments'" :paddingData="3"></empty>
    </div>
    <div class="recent_reviews phone">
      <div class="r_r_title">
        Recent reviews
        <el-divider><svg-icon value="icon-Downxiangxia35" :size="1.2"></svg-icon></el-divider>
      </div>
        <div class="r_r_reviews" v-if="hotReview && hotReview.length>0">
          <div class="r_r_r_card" v-for="(item,index) in hotReview" :key="index">
            <div class="r_r_r_c_card" v-for="(review,ind) in item" :key="ind">
              <svg-icon value="icon-tubiaozhizuomoban" class="icon_hot" :size="1.3"></svg-icon>
              <div class="c_title">
                <el-avatar class="c_t_img" size="large" :src="review.Icon"></el-avatar>
                <rate
                  class="c_t_rate"
                  :value="review.Rank"
                  :isDisabled="true"
                >
                </rate>
              </div>
              <p class="c_user">{{review.Name}} <span class="rev">reviewed</span> <span @click="handleProInfo(review)" class="pro">{{review.ProName}}</span></p>
              <p class="c_text">
                {{review.Content}}
              </p>
            </div>
          </div>
        </div>
      <empty v-else :tips="'No hot comments'" :paddingData="3"></empty>
    </div>
    <div class="be_heard">
      <div class="r_r_title" style="padding-bottom:10px">
        About
        <el-divider><svg-icon value="icon-Downxiangxia35" :size="1.6"></svg-icon></el-divider>
      </div>
      <p class="heard_text">
        Comment.com is committed to creating the most authentic review platform, where everyone can easily share the most authentic experience. Provide valuable reference for other users.
      </p>
      <div class="what_do"><el-button class="companies" type="gone" plain @click="goCategories">Find out</el-button></div>
    </div>
    <footer-com></footer-com>
  </div>
</div>
</template>
<script>
import vueSeamlessScroll from 'vue-seamless-scroll'
export default {
  components:{
    vueSeamlessScroll
  },
  data(){
    return{
      loading:false, //加载
      searchData:null, //搜索
      hotReview:null, //热门评论
      hotType:null, //热门分类
    }
  },
  computed: {
    defaultOption () {
      return {
        step: 1, // 数值越大速度滚动越快
        limitMoveNum: this.hotReview.length, // 开始无缝滚动的数据量 this.dataList.length
        hoverStop: true, // 是否开启鼠标悬停stop
        direction: 3, // 0向下 1向上 2向左 3向右
        openWatch: true, // 开启数据实时监控刷新dom
        singleHeight: 0, // 单步运动停止的高度(默认值0是无缝不停止的滚动) direction => 0/1
        singleWidth: 0, // 单步运动停止的宽度(默认值0是无缝不停止的滚动) direction => 2/3
        waitTime: 1000 // 单步运动停止的时间(默认值1000ms)
      }
    }
},
  mounted(){
    this.getQueryCommentTypeData();
  },
  methods:{
    /**
     * 去当前热门分类的产品列表
     */
    handleProList(item){
      this.$router.push({
        path:'/product-list',
        query:{
          Name:item.name,
          Id:item.id
        }
      })
    },
    clickCard(e){
      if(!e.target.id){
        return;
      }
      const item = JSON.parse(e.target.dataset.pro);
      if(!item.ProId){
        return;
      }
      this.$router.push({
        path:'/product-info',
        query:{
          pid:item.ProId
        }
      })
    },
    /**
     * 查看评论中的产品详情
     */
    handleProInfo(review){
      if(!review.ProId){
        return;
      }
      this.$router.push({
        path:'/product-info',
        query:{
          pid:review.ProId
        }
      })
    },
    /**
     * 搜索
     */
    handleSearch(){
      if(this.searchData){
          this.$router.push({
          path:'/product-list',
          query:{
            searchData:this.searchData
          }
        })
      }
    },
    /**
     * 获取首页热门评论
     */
    getQueryCommentTypeData(){
      this.loading=true;
      Promise.all([
        this.$apiHttp.getQueryHotType({params:{pageCount:3}}),
        this.$apiHttp.getQueryHotComment({params:{pageCount:12}})
      ]).then((resp)=>{
        if(resp[0].res == 200 && resp[1].res == 200){
          if(resp[1].data){
            this.hotReview = _.chunk(resp[1].data,2);
          }
          this.hotType=resp[0].data;
        }
        this.loading=false;
      })
    },
    /**
     * 查看分类
     */
    goCategories(){
      this.$router.push({ path: '/categories'});
    }
  }
}
</script>
<style lang="less" scoped>
.home{
  height: 100%;
}
.h_home{
  height: calc(100% - 72px);
  overflow: auto;
  .banner{
    background: url("~@/assets/images/banner.png") no-repeat;
    height: 600px;
    width: 100%;
    background-size:cover;
    .c_banner{
      padding: 80px 24px 88px;
      width: 43%;
      margin-left: 15%;
      .title_1{
        margin-bottom: 12px;
        color:#2e1917;
      }
      .title_2{
        font-size: 1.25rem;
        color: #2e1917;
        margin-bottom: 40px;
        font-weight: 500;
      }
      .title_3{
        font-size: 1rem;
        color: #2e1917;
        margin-bottom: 30px;
        font-weight: 500;
      }
      .b_search{
        display: flex;
        flex-direction: row;
        margin-bottom: 56px;
        .s_input{
          margin-right: 3px;
          /deep/.el-input__inner{
            height: 60px;
            color: #1B1B21;
            font-size: 1rem;
          }
        }
        .s_button{
          font-size: 1rem;
        }

      }
      .b_type{
        padding-top: 16px;
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        flex-wrap: wrap;
        .type_card{
          cursor: pointer;
          width: 105px;
          height: 110px;
          background-color: white;
          margin-bottom: 8px;
          box-shadow: 0 2px 2px 0 rgba(0,0,50,0.04);
          display: flex;
          flex-direction: column;
          align-items: center;
          justify-content: center;
          border-radius: 8px;
          padding: 0 5px;
          &:hover{
            box-shadow: 0 3px 18px -5px rgba(80,166,255,.7);
          }
          .type_card_text{
            font-size: 0.875rem;
            line-height: 1rem;
            color: #1b1b1b;
            text-align: center;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            overflow: hidden;
            text-overflow: ellipsis;
            -webkit-box-orient: vertical;

          }
          .type_card_icon{
            margin-bottom: 0.2rem;
          }
          &:hover{
            .type_card_text{
              color: #409eff;
            }
          }
        }
      }
    }
  }
  .r_r_title{
      text-align: center;
      font-size: 40px;
      font-weight: bold;
      padding:45px 0 48px 0;
      .el-divider{
        height: 2px;
        .el-divider__text{
          background-color:#F1F1F1;
        }
      }
    }
  .recent_reviews{
    background-color:rgba(210,210,210,0.3);
    overflow:hidden;
    .seamless-warp{
      // height: 480px;
    }
    .r_r_reviews{
      padding: 0 12px;
      display:flex;
      flex-direction: row;
      width: 2160px;
      padding-top: 10px;
      &:last-child {
        margin-right:-143px;
      }
      .r_r_r_card{
        display: flex;
        flex-direction: column;
        padding-bottom: 15px;
        margin-right: 10px;
        width: 330px;
        flex-shrink: 0;
        .r_r_r_c_card{
          position: relative;
          border-radius: 8px;
          background: #ffffff;
          margin-bottom: 10px;
          padding: 22px 15px 22px 22px;
          width: 290px;
          box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);
          transition: transform .6s,box-shadow .6s;
          cursor: default;
          &:hover{
            transform: perspective(1px) scale(1.05);
            box-shadow: 0 12px 20px 0 rgba(0,0,50,0.12);
          }
          .icon_hot{
            position: absolute;
            top: 0;
            right: 25px;
          }
          .c_title{
            display: flex;
            flex-direction: row;
            align-items: center;
            .c_t_img{

            }
            .c_t_rate{
              margin-left: 8px;
              /deep/.el-rate__icon{
                font-size: 1.2rem;
              }
              /deep/.icon-pingfendengjiRating4{
                font-size: 1.2rem;
              }
            }
          }
          .c_user{
            color: #1b1b21;
            font-weight: 700;
            font-size: 0.75rem;
            .rev{
              color: #adadad;
            }
            .pro{
              cursor: pointer;
            }
          }
          .c_text{
            font-size: 0.875rem;
            display: -webkit-box;
            -webkit-line-clamp: 6;
            overflow: hidden;
            text-overflow: ellipsis;
            -webkit-box-orient: vertical;
            color: #1B1B21;
          }
        }
      }
    }
  }
  .phone{
    display: none;
  }
  .pc{
    display: flex;
    flex-direction: column;
  }
  .be_heard{
    background-color:rgba(210,210,210,0.3);
    padding: 50px 0px 80px;
    .heard_title{
      text-align: center;
      font-size: 2.875rem;
      margin-top: 0;
      margin-bottom: 16px;
      color: #ffffff;
    }
    .heard_text{
      text-align: center;
      margin-left: auto;
      margin-right: auto;
      margin-bottom: 32px;
      font-size: 1.25rem;
      line-height: 1.75rem;
      max-width: 780px;
      color:   #4f5051;
    }
    .what_do{
      text-align: center;
      .companies{
        border: 2px solid  #F1F1F1;
        font-weight: bold;    
      }
      .el-button--gone:hover
      {
        background:#F1F1F1;
        color: #4f5051;
        border-color: #4f5051;
      }
      .el-button--gone{
        background:#4f5051;
        color: #F1F1F1;
      }
    }
  }
}
	@media all and (max-width: 1024px) {
    .h_home{
      height: calc(100% - 72px);
      overflow: auto;
      .banner{
        background: url("~@/assets/images/banner.png") no-repeat;
        height: auto;
        .c_banner{
          padding: 1rem;
          width: 90%;
          margin-left: auto;
          margin-right: auto;
          .title_1{
            margin-bottom: 0.875rem;
            font-size: 1.7rem;
          }
          .title_2{
            font-size: 0.875rem;
            margin-bottom: 1rem;
          }
          .title_3{
            font-size: 1rem;
            margin-bottom: 1rem;
          }
          .b_search{
            margin-bottom: 2rem;
            .s_input{
              margin-right: 0.1rem;
              /deep/.el-input__inner{
                height: 3rem;
                font-size: 1rem;
              }
            }
            .s_button{
              font-size: 1rem;
            }

          }
          .b_type{
            padding-top: 0.2rem;
            .type_card{
              // width: 0px;
              margin-bottom: 10px;
              box-shadow: 0 2px 2px 0 rgba(0,0,50,0.04);
              .type_card_text{
                font-size: 0.75rem;
                line-height: 1rem;
              }
              .type_card_icon{
                margin-bottom: 0.1rem;
              }
            }
          }
        }
      }
      .phone{
        display: flex;
        flex-direction: column;
      }
      .pc{
        display: none;
      }
      .r_r_title{
          font-size: 1.25rem;
          padding:2rem 0;
        }
      .recent_reviews{
        .r_r_reviews{
          padding: 0 8px;
          display:-webkit-box;
          width: calc(100% - 24px);
          margin-right: 0 !important;
          overflow-x: scroll;
          .r_r_r_card{
            padding-bottom: 0.8rem;
            margin-right: 0.5rem;
            width: 70%;
            .r_r_r_c_card{
              margin-bottom: 0.5rem;
              padding: 1.1rem 0.5rem;
              width: calc(100% - 1rem);
              transition: transform .6s;
              border-radius: 8px;
              cursor: default;
              &:hover{
                transform: perspective(0px) scale(1);
                box-shadow: 0 12px 20px 0 rgba(0,0,50,0.12);
              }
              .icon_hot{
                right: 15px;
              }
              .c_title{
                .c_t_rate{
                  margin-left: 0.5rem;
                }
              }
            }
          }
        }
      }
      .be_heard{
        padding: 2rem 1rem 2rem;
        .heard_title{
          font-size: 2rem;
          margin-bottom: 0;
        }
        .heard_text{
          margin-bottom: 1rem;
          font-size: 1rem;
          line-height: 1.75rem;
        }
      }
    }
	}
</style>