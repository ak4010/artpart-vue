<template>

<div class="d-flex justify-content-between flex-wrap flex-md-nowrap align-items-center pt-3 pb-2 mb-3 border-bottom">
      <h1 class="h2">우리집 관리비 조회하기</h1>  
  </div>
  <div class="d-flex justify-content-between flex-wrap flex-md-nowrap align-items-center pt-3 pb-2 mb-3 border-bottom">
      <h2 class="h4">관리비 납부 내역</h2>  
  </div>
  <div class="table-responsive">
        <table class="table table-striped table-sm">
          <thead>
            <tr>
              <th scope="col">결제일자</th>
              <th scope="col">결제금액</th>
              <th scope="col">부과년월</th>
              <th scope="col">납부마감일</th>
              <th scope="col">금융사</th>
              <th scope="col">진행상태</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for ="(item, index) in list" :key="index">
              <td>{{$dayjs(item.payment_date).format("YYYY년 MM월 D일")}}</td>
              <td>{{comma(item.payment_money) }}원</td>
              <td>{{$dayjs(item.payment_date).format("YYYY년 MM월 D일")}}</td>
              <td>{{$dayjs(item.payment_ded_line).format("YYYY년 MM월 D일")}}</td>
              <td>{{item.payment_bank}}</td>
              <td>{{item.payment_status}}</td>
            </tr>
            

          </tbody>
        </table>
      </div>
      <div>
          <div></div>
          <div v-if="paging.total_list_cnt > 0" style="margin: 0 auto !important;">
            <span >
            <a  class="page-item" href="javascript:;" @click="fnPage(1)" >&lt;&lt;</a>
            <a class="page-item" href="javascript:;" v-if="paging.page > 7" @click="fnPage(`${paging.start_page-1}`)"
               >&lt;</a>
            <template v-for=" (n,index) in paginavigation()">
                <template v-if="paging.page==n">
                    <strong class="page-item-fn" :key="index">{{ n }}</strong>
                </template>
                <template v-else>
                    <a class="page-item" href="javascript:;" @click="fnPage(`${n}`)" :key="index">{{ n }}</a>
                </template>
            </template>
            <a  class="page-item" href="javascript:;" v-if="paging.total_page_cnt > paging.end_page"
               @click="fnPage(`${paging.end_page+1}`)" >&gt;</a>
            <a  class="page-item" href="javascript:;" @click="fnPage(`${paging.total_page_cnt}`)" >&gt;&gt;</a>
            </span>
          </div>
          </div>
</template>

<script>
import dayjs from 'dayjs';
import 'dayjs/locale/ko';

export default {
  data(){
      return {
          requestBody: {}, //리스트 페이지 데이터전송
          list: {}, //리스트 데이터
          no: '', //게시판 숫자처리
           paging: {
               block: 0,
               end_page: 0,
               next_block: 0,
               page: 0,
               page_size: 0,
              prev_block: 0,
              start_index: 0,
              start_page: 0,
              total_block_cnt: 0,
              total_list_cnt: 0,
              total_page_cnt: 0,
          }, //페이징 데이터
          page: this.$route.query.page ? this.$route.query.page : 1,
          size: this.$route.query.size ? this.$route.query.size : 10,
          //keyword: this.$route.query.keyword,
       
          paginavigation: function () { //페이징 처리 for문 커스텀
              let pageNumber = [] //;
              let start_page = this.paging.start_page;
              let end_page = this.paging.end_page;
              for (let i = start_page; i <= end_page; i++) pageNumber.push(i);
              return pageNumber;
          },
          
        }
        
  },
  methods: {
    comma(value) {

    // 여기에서 필터링 로직을 수행하고, 필터링된 값을 반환합니다.
    return value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },

    UsingList(){
          //스프링 부트에서 전송받은 데이터 출력 처리
          this.requestBody = { // 데이터 전송
              page: this.page,
              size: this.size
          }

          this.$axios.get(this.$serverUrl + "/using/list", {
              params: this.requestBody,
              headers: {}
          }).then((res) => {
              //this.list = res.data  //서버에서 데이터를 목록으로 보내므로 바로 할당하여 사용할 수 있다.
              if (res.data.result_code === "OK") {
                  this.list = res.data.data
                  this.paging = res.data.pagination
                   this.no = this.paging.total_list_cnt - ((this.paging.page - 1) * this.paging.page_size)
              }
              }).catch( error => {
                  console.error(error);
              })
      },fnPage(n) {
          if (this.page !== n) {
              this.page = n
          }
          this.UsingList()
      }
  },
 mounted(){
  this.UsingList();
  this.$dayjs = dayjs; // dayjs를 Vue 인스턴스의 속성으로 등록합니다
  this.$dayjs.locale('ko'); // 사용할 로케일을 설정합니다

  
 } 
 
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.page-item {
    min-width:32px;
    padding:2px 6px;
    text-align:center;
    margin:0 3px;
    border-radius: 6px;
    border:1px solid #eee;
    color:#666;
}
.page-item-fn{
    min-width:32px;
    padding:2px 6px;
    text-align:center;
    margin:0 3px;
    border-radius: 6px;
    border:1px solid #eee;
    color:#666;
}
.page-item:hover {
    background: #E4DBD6;
}
.page-item a {
    color:#666;
}
.page-item-fn {
    background-color : #E7AA8D;
    color:#fff;
}
.page-item-fn a {
    color:#fff;
}
</style>
