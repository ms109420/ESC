<template>
<v-container style="max-width:1000px">
<div v-if="coin ==0&&this.tableMode ==1" style="min-height:460px;">
  <v-btn @click="ModeChange()"><v-icon>mdi-table</v-icon></v-btn>
      <v-simple-table >
        <template v-slot:default>
          <thead>
            <tr style="text-align: center;">
              <td>번호</td>
              <td>제목</td>
              <td>글쓴이</td>
              <td><v-icon>mdi-clock-outline</v-icon></td>
              <td>상태</td>
              <td>여부</td>
              <td><v-icon>mdi-eye</v-icon></td>
              <td><v-icon color="blue">mdi-thumb-up</v-icon></td>
              <td><v-icon color="red">mdi-thumb-down</v-icon></td>
            </tr>
          </thead>
          <tbody>
          <tr style="text-align: center;"  v-for="p in paginatedData" :key="p.boardNo">
            <td style="margin-right:50px">{{p.boardNo}}</td>
            <td @click="goDetail(p.boardNo)">{{p.title}}</td>
            <td>{{p.memberId}}</td>
            <td>{{$moment(p.createDate).format('YYYY-MM-DD/hh')}}</td>
            <td>{{p.report}}</td>
            <td><v-btn @click="DeleteBoard(p.boardNo)">삭제</v-btn><v-btn @click="pass(p.boardNo)">보류</v-btn></td>
            <td>{{p.viewcount}}</td>
            <td style="width:70px">{{p.good}}</td>
            <td style="width:70px">{{p.bad}}</td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
    <!--페이지네이션 버튼 -->
    <div class="btn-cover">
    <button :disabled="pageNum === 0" @click="prevPage" class="page-btn"><v-icon>mdi-arrow-left-bold</v-icon></button>
    <span class="page-count">{{ pageNum + 1 }} / {{ pageCount }}</span>
    <button :disabled="pageNum >= pageCount - 1" @click="nextPage" class="page-btn"><v-icon>mdi-arrow-right-bold</v-icon></button>
      <form @keyup.enter="searching(searchMenus,search)">
        <v-row style="margin-left: 80%">
          <v-select  style="max-width: 100px" :items="searchMenu" label="검색" v-model="searchMenus"/>
          <v-text-field  style="max-width: 300px" v-model="search" label="검색란"></v-text-field>
        </v-row>
      </form>
    </div>
</div>


<div v-else-if="coin ==1&&tableMode ==1" style="min-height:460px;">
  <v-btn @click="ModeChange()"><v-icon>mdi-table</v-icon></v-btn>
      <v-simple-table >
        <template v-slot:default>
          <thead>
            <tr style="text-align: center;">
              <td>번호</td>
              <td>제목</td>
              <td>글쓴이</td>
              <td><v-icon>mdi-clock-outline</v-icon></td>
              <td>상태</td>
              <td><v-icon>mdi-eye</v-icon></td>
              <td><v-icon color="blue">mdi-thumb-up</v-icon></td>
              <td><v-icon color="red">mdi-thumb-down</v-icon></td>
            </tr>
          </thead>
          <tbody>
            <tr style="text-align: center;"  v-for="p in searchpaginatedData" :key="p.boardNo">
              <td style="margin-right:50px">{{p.boardNo}}</td>
              <td @click="goDetail(p.boardNo)">{{p.title}}</td>
              <td>{{p.memberId}}</td>
              <td>{{$moment(p.createDate).format('YYYY-MM-DD/hh')}}</td>
              <td>{{p.report}}</td>
              <td>{{p.viewcount}}</td>
              <td style="width:70px">{{p.good}}</td>
              <td style="width:70px">{{p.bad}}</td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>
 <!-- 페이지네이션 버튼 -->
    <div class="btn-cover">
      <button :disabled="searchpageNum === 0" @click="searchPrevPage" class="page-btn"><v-icon>mdi-arrow-left-bold</v-icon></button>
      <span class="page-count">{{ searchpageNum + 1 }} / {{ searchpageCount }}</span>
      <button :disabled="searchpageNum >= searchpageCount - 1" @click="searchNextPage" class="page-btn"><v-icon>mdi-arrow-right-bold</v-icon></button>
      <form @keyup.enter="searching(searchMenus,search)">
        <v-row style="margin-left: 80%">
          <v-select  style="max-width: 100px" :items="searchMenu" label="검색" v-model="searchMenus"/>
          <v-text-field  style="max-width: 300px" v-model="search" label="검색란"></v-text-field>
        </v-row>
        <v-btn style="margin-left: 90%" @click="showAllBoard()">검색해제</v-btn>
      </form>
    </div>
</div>


<div v-else-if="coin ==0&&cardMode ==1">
   <v-btn @click="ModeChange()"><v-icon>mdi-format-list-bulleted</v-icon></v-btn>
  <v-row>
    <v-card class="mx-auto my-12" width="400" v-for="i in paginatedData" :key="i.boardNo" @click="goDetail(i.boardNo)" outlined hover>
    <v-card-title>{{i.memberId}}</v-card-title>
    <v-divider class="mx-4"></v-divider>
    <v-card-title>{{i.title}}</v-card-title>
    <v-card-subtitle>[{{$moment(i.createDate).format('YYYY-MM-DD/hh:mm')}} 조회{{i.viewcount}}]</v-card-subtitle>
    <v-img v-if="i.img != ''" width="400px" height="350" :src="require(`@/assets/게시판/${i.img}`)"></v-img>
    <v-img v-else-if="i.img == ''" width="400px" height="350" :src="require('@/assets/게시판/사진없음.jpg')"></v-img>
    <v-divider class="mx-4"></v-divider>
    <v-card-text>
      <v-icon color="blue" style="margin-left: 77%" >mdi-thumb-up</v-icon>{{i.good}}<v-icon color="red">mdi-thumb-down</v-icon>{{i.bad}}
    </v-card-text>
    </v-card>
  </v-row>
  <!--페이지네이션 버튼 -->
    <div class="btn-cover">
      <button :disabled="pageNum === 0" @click="prevPage" class="page-btn"><v-icon>mdi-arrow-left-bold</v-icon></button>
      <span class="page-count">{{ pageNum + 1 }} / {{ pageCount }}</span>
      <button :disabled="pageNum >= pageCount - 1" @click="nextPage" class="page-btn"><v-icon>mdi-arrow-right-bold</v-icon></button>
      <form @keyup.enter="searching(searchMenus,search)">
        <v-row style="margin-left: 80%">
          <v-select  style="max-width: 100px" :items="searchMenu" label="검색" v-model="searchMenus"/>
          <v-text-field  style="max-width: 300px" v-model="search" label="검색란"></v-text-field>
        </v-row>
      </form>
    </div>
</div>


<div v-else-if="coin ==1&&cardMode ==1">
  <v-btn @click="ModeChange()"><v-icon>mdi-format-list-bulleted</v-icon></v-btn>
  <v-row>
    <v-card class="mx-auto my-12" width="400" v-for="i in searchpaginatedData" :key="i.boardNo" @click="goDetail(i.boardNo)" outlined hover>
    <v-card-title>{{i.memberId}}</v-card-title>
    <v-card-title>{{i.title}}</v-card-title>
    <v-card-subtitle>[{{$moment(i.createDate).format('YYYY-MM-DD/hh:mm')}} 조회{{i.viewcount}}]</v-card-subtitle>
    <v-img v-if="i.img != ''" width="400px" height="350" :src="require(`@/assets/게시판/${i.img}`)"></v-img>
    <v-img v-else-if="i.img == ''" width="400px" height="350" :src="require('@/assets/게시판/사진없음.jpg')"></v-img>
    <v-divider class="mx-4"></v-divider>
    <v-card-text>
      <v-icon color="blue" style="margin-left: 77%" >mdi-thumb-up</v-icon>{{i.good}}<v-icon color="red">mdi-thumb-down</v-icon>{{i.bad}}
    </v-card-text>
    </v-card>
  </v-row>
  <!--페이지 네이션 버튼 -->
    <div class="btn-cover"><button :disabled="searchpageNum === 0" @click="searchPrevPage" class="page-btn"><v-icon>mdi-arrow-left-bold</v-icon></button>
      <span class="page-count">{{ searchpageNum + 1 }} / {{ searchpageCount }}</span>
      <button :disabled="searchpageNum >= searchpageCount - 1" @click="searchNextPage" class="page-btn"><v-icon>mdi-arrow-right-bold</v-icon></button>
      <form @keyup.enter="searching(searchMenus,search)">
        <v-row style="margin-left: 80%">
          <v-select  style="max-width: 100px" :items="searchMenu" label="검색" v-model="searchMenus"/>
          <v-text-field  style="max-width: 300px" v-model="search" label="검색란"></v-text-field>
        </v-row>
        <v-btn style="margin-left: 90%" @click="showAllBoard()">검색해제</v-btn>
      </form>
    </div>
</div>
</v-container>
</template>

<script>
import Vue from 'vue'
import cookies from 'vue-cookies'
import axios from 'axios';
import { mapActions, mapState } from 'vuex';
Vue.use(cookies)
export default {
  name: 'FreeBoardListForm',
  data () {
    return {
      //tableMode: 1,
      //cardMode: 0,
      searchMenus: '',
      pageNum: 0,
      searchpageNum: 0,
      search: '',
      ip: '',
      coin: 0,
      searchList: [],
      searchFilterList: [],
      searchMenu: [
        {text: '글제목', value:'글제목'},
        {text: '작성자' , value:'작성자'}
      ],
      selectedItem: 1,

     
    }
  },
  props: {
    ReportedBoardList: {
      type: Array,
      required: true
    },
    pageSize: {
      type: Number,
      required: false,
      default: 8
    }
  },
  methods: {
    ...mapActions(['SetTableMode', 'SetCardMode']),
    nextPage () {
      this.pageNum += 1;
    },
    searchNextPage(){
      this.searchpageNum +=1;
    },
    prevPage () {
      this.pageNum -= 1;
    },
    searchPrevPage() {
      this.searchpageNum -=1;
    },
    goDetail(boardNo){
        this.$router.push({name: 'BoardReadPage', params:{boardNo}})

        axios.post(`https://evsbackend.herokuapp.com/board/viewcount/${boardNo}`)
        .then( () =>{

        })
    },
    viewcount(boardNo){
      console.log("동작")
      axios.post(`https://evsbackend.herokuapp.com/board/viewcount/${boardNo}`)
        .then( () =>{

        })
    },
    searching(searchMenus,search){
      if(searchMenus ==''){
        alert("찾는 카테고리를 선택해주세요")
      }
      if(searchMenus =="글제목"){
        console.log('동작')
      axios.post(`https://evsbackend.herokuapp.com/board/reportedTitleSearchList/${search}`)
      .then( (res)=> {
        this.searchList = []
        if(res.data == ''){
          alert("해당검색어로 검색되는 글이 존재하지않습니다.")
        }
        else{
          for(var i = 0 ; i< res.data.length; i ++) {
            if(res.data[i].report =="신고됨"){
              this.searchList.push(res.data[i]) 
            }
          }
          
          this.coin= 1}
       
      }
        
        
        )
      }

      if(searchMenus =="작성자"){axios.post(`https://evsbackend.herokuapp.com/board/reportedMemberIdSearchList/${search}`)
      .then( (res)=>{
        this.searchList = []
        if(res.data == ''){
           alert("해당검색어로 검색되는 글이 존재하지않습니다.")
        }
        else{
          for(var i = 0 ; i< res.data.length; i ++) {
            if(res.data[i].report =="신고됨"){
              this.searchList.push(res.data[i]) 
            }
          }
          
          this.coin= 1}
      }
    
        )
      }
      
    },
    showAllBoard(){
      this.coin = 0;
    },

    ModeChange(){
      
      /*
     if(this.cardMode == 0){
       this.cardMode =1;
     }else{
       this.cardMode = 0
     }
     if(this.tableMode == 1){
       this.tableMode =0;
     }else{
       this.tableMode = 1
     }*/
     if(this.$store.state.tableMode == 1){
      Vue.$cookies.remove("TableMode")
      Vue.$cookies.remove("CardMode")
       this.SetTableMode(2)
      this.SetCardMode(1)
      Vue.$cookies.set('TableMode', 2, '1h')
      Vue.$cookies.set('CardMode', 1, '1h')
      console.log(this.tableMode)
      console.log(this.cardMode)
     }
     else{
      Vue.$cookies.remove("TableMode")
      Vue.$cookies.remove("CardMode")
      this.SetTableMode(1)
      this.SetCardMode(2)
      Vue.$cookies.set('TableMode', 1, '1h')
      Vue.$cookies.set('CardMode', 2, '1h')

     }
      
     
    },
    Write(){
      if(this.$store.state.session == null){
        alert("로그인이 필요합니다.")
        this.$router.push({name: 'LoginPage'})
      }
      else{
        this.$router.push({name: "BoardRegister"})
      }
    },
 DeleteBoard(boardNo){

          axios.post(`https://evsbackend.herokuapp.com/board/DeleteBoard/${boardNo}`)
          .then( () =>{
          alert('글이 삭제되었습니다')
          this.$router.go()
          })
                                
          },
  pass(boardNo) {
    axios.post(`https://evsbackend.herokuapp.com/board/ReportPass/${boardNo}`)
    .then( ( ) => {
      alert( "글이 보류되었습니다.")
      this.$router.go()
    })

  }
  },
  computed: {
      ...mapState(['tableMode','cardMode']),
    pageCount () {
      let listLeng = this.ReportedBoardList.length,
          listSize = this.pageSize,
          page = Math.floor(listLeng / listSize);
      if (listLeng % listSize > 0) page += 1;

      return page;
    },
    searchpageCount () {
      let listLeng = this.searchList.length,
          listSize = this.pageSize,
          page = Math.floor(listLeng / listSize);
      if (listLeng % listSize > 0) page += 1;

      return page;
    },
    paginatedData () {
      const start = this.pageNum * this.pageSize,
            end = start + this.pageSize;
      return this.ReportedBoardList.slice(start, end);
    },
    searchpaginatedData () {
      const start = this.searchpageNum * this.pageSize,
            end = start + this.pageSize;
      return this.searchList.slice(start, end);
    },
  },
  created(){
    if(this.$store.state.tableMode ==null){
      this.SetTableMode()
      this.SetCardMode()
    }
    else{
      this.SetTableMode(Vue.$cookies.get("TableMode"))
      this.SetCardMode(Vue.$cookies.get("CardMode"))
    }

  }

}
</script>

<style scoped>
td{
  font-family: 'Do Hyeon', sans-serif;
}

</style>