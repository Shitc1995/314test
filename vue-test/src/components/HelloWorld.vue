<template>
  <div class="container" style="min-width: 230px;">
    <div class="container-md">作品社区</div>
    <div class="row one-menu" v-for="(item, index) in video">
      <div class="row">
        <div class="row" style="text-align: left;padding: 0 5px;">
          <h5 class="menu-title">{{item.name}}</h5>
          <span class="menu-time">{{item.time}}</span>
        </div>
        <div class="menu-video" :style="{ 'height' : videoHeight + 'px'}">
          <video :id="'myVideo' + index" class="video-js" controls="controls"  style="width: 100%;height: 100%">
            <source src="/static/video/video.mp4" type="video/mp4">
          </video>
          <div class="menu-star">
            <span v-for="value in [5,4,3,2,1]" :key="value" style="margin-left: 5px;" @click="starChange(index, value)">
              <img width="24" height="24" :src="item.star >= value ? '/static/images/star_yes.png' : '/static/images/star_no.png'"/>
            </span>
          </div>
          <div class="menu-charts" :style="{'width': chartWidth + 'px','height': chartWidth + 'px'}">
            <div :id="'charts_id_' + index" :style="{'width': chartWidth + 'px','height': chartWidth + 'px'}"></div>
          </div>
        </div>
        <div class="menu-comment">
          <input v-if="item.showCommentInput" v-focus="item.showCommentInput" v-model="inputContent" @blur="showCommentInputBlur(index)" name="comment" type="text" class="form-control" style="width: 80%">
          <div style="float:right;margin-left: 5px;" @click="showCommentInputClick(index)">
              <img width="20" height="20" src="/static/images/comment.png"/>
          </div>
        </div>
        <div class="menu-comment-list" v-for="(one, i) in item.comment">
          <div class="menu-comment-li" v-if="i < 3 || (i >=3 && item.showAllComment)">{{one}}</div>
        </div>
        <div v-if="item.comment.length > 3" @click="item.showAllComment = !item.showAllComment">
          <img width="16px" height="16px" :src="item.showAllComment ? '/static/images/down.png' : '/static/images/up.png'" alt="">
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'test',
  data () {
    return {
      screenWidth: document.documentElement.clientWidth,
      videoHeight: 200,
      chartWidth: 100,
      video: [
        {
          name: '超级玛丽0',
          time: '2020.01.05 15:30',
          star: 4,
          showCommentInput: false,
          showAllComment: false,
          comment: [
            '111111',
            '2222222222',
            '3333333333',
            '444444444',
          ]
        },
        {
          name: '超级玛丽1',
          time: '2020.01.05 15:30',
          star: 2,
          showCommentInput: false,
          showAllComment: false,
          comment: []
        }
      ],
      inputContent: '',
    }
  },
  watch: {
    'screenWidth': function (val) {

    }
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus();
      }
    }
  },
  mounted() {
    let me = this
    this.resizeFunction()
    this.chartWidth = this.videoHeight > 300 ? 150 : Math.floor(this.videoHeight / 2)
    window.addEventListener('resize', this.resizeFunction, true);
    window.addEventListener('scroll', this.scrollFunction, true);
    setTimeout(function () {
      me.drawLine()
    },1000)
  },
  destroyed() {
    window.removeEventListener('scroll', this.scrollFunction)
    window.removeEventListener('resize', this.resizeFunction);

  },
  methods:{
    // 星级改变
    starChange (index, value) {
      this.video[index].star = value;
    },
    // 图表显示
    drawLine(){
      let len = this.video.length
      for (let i = len - 2; i < len; i++) {
        let myChart = this.$echarts.init(document.getElementById('charts_id_' + i))
        // 绘制图表
        myChart.setOption({
          radar: {
            name: {
              show:false,
            },
            fontSize: 6,
            lineHeight: 10,
            indicator: [
              { name: '销售（Level 1）', max: 100},
              { name: '管理（Level 2）', max: 100},
              { name: '信息（Level 4）', max: 100},
              { name: '客服（Level 6）', max: 100},
              { name: '研发（Level 7）', max: 100},
              { name: '市场（Level 3）', max: 100}
            ]
          },
          tooltip: {
            show:false,
          },
          series: [{
            name: '',
            type: 'radar',
            areaStyle:{
              color:'rgba(128, 128, 128, 0.5)',
            },
            data : [
              {
                value : [83, 60, 88, 75, 90, 79]
              }
            ]
          }]
        });
      }
    },
    // 评论按钮单击，显示评论框
    showCommentInputClick (index) {
      this.video[index].showCommentInput = !this.video[index].showCommentInput;
    },
    // 评论输入框失去焦点，保存评论
    showCommentInputBlur (index) {
      this.video[index].showCommentInput = false;
      if (this.inputContent !== '') {
        this.video[index].comment.push(this.inputContent);
        this.inputContent = '';
      }
    },
    // 监听页面滚动和宽度改变
    resizeFunction () {
      let val = document.documentElement.clientWidth || document.body.clientWidth;
      this.videoHeight = parseInt(val / 2)
    },
    scrollFunction () {
      if (this.video.length < 10) {
        let scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
        let windowHeight = document.documentElement.clientHeight || document.body.clientHeight;
        let scrollHeight = document.documentElement.scrollHeight || document.body.scrollHeight;
        if(scrollTop + windowHeight >= scrollHeight - 20){
          this.loadMore();
        }
      } else {
        window.removeEventListener('scroll', this.scrollFunction);
      }
    },
    // 懒加载更多
    loadMore () {
      this.video.push({
        name: '超级玛丽' + this.video.length,
        time: '2020.01.05 15:30',
        star: 4,
        showCommentInput: false,
        showAllComment: false,
        comment: [ ]
      });
      this.video.push({
        name: '超级玛丽' + this.video.length,
        time: '2020.01.05 15:30',
        star: 4,
        showCommentInput: false,
        showAllComment: false,
        comment: [ ]
      });
      let me = this
      setTimeout(function () {
        me.drawLine()
      },1000)
    },
  },
}
</script>
<style scoped>
  .one-menu{
    border-radius: 5px;border:1px solid #cccccc;
    margin-top: 20px;
  }
  .menu-title{
    display: inline-block;
    border-left: 1px solid goldenrod;
    padding-left: 5px;
    margin-right: 5px;
  }
  .menu-time{
    font-size: 12px;
  }
  .menu-video{
    margin: 0 auto;
    width: 95%;
    border-radius: 5px;
    background: aqua;
    position: relative;
  }
  .menu-star{
    position: absolute;
    top:5px;
    right:5px;
  }
  .menu-charts{
    position: absolute;
    bottom:0px;
    right: 0px;
    border-radius: 5px;
    background: rgba(255,255,255,0.5);
  }
  .menu-comment, .menu-comment-list{
    margin: 10px auto;
    width: 90%;
    text-align: left;
  }
  .menu-comment{
    height: 24px;
  }
  .menu-comment-li{
    height: 14px;
    line-height: 14px;
    font-size: 12px;
  }
</style>
