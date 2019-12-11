<template>
  <div>
    <v-tabs v-model="path" grow centered>
      <v-tab>경기일정/결과</v-tab>
      <v-tab>경기순위</v-tab>    
        <v-tab-item>
          <v-tabs v-model="model">
            <v-tab v-for="(i,index) in tabs" :key="index" @click="onClickButton(index)">
              {{ i.name }}   
            </v-tab>
          </v-tabs>
            <v-card >
              <template>
                <v-simple-table>          
                  <div v-for="(day, index) in days" :key="index">
                    <thead>
                      <tr>
                        <th class="text-left">2019.{{ day.scheduleList[0].gameCenterFormattedDate }}</th>
                      </tr>
                    </thead>
                    <tbody>              
                      <tr v-for="(item, index) in day.scheduleList" :key="index" class="text-center">
                        <td cols="2" sm="12">{{ item.stadium }}</td>
                        <td width="25%"> <img :src= "item.awayTeamEmblemUrl"> </td>
                        <td width="10%"> {{ item.awayTeamFullName }} </td>
                        <td> {{ item.awayTeamScore }} </td>
                        <td width="20%"> "{{ item.winner }}" 팀 승리 </td>
                        <td> {{ item.homeTeamScore }} </td>
                        <td width="10%"> {{ item.homeTeamFullName}} </td>
                        <td width="25%"> <img :src= "item.homeTeamEmblemUrl"> </td>
                      </tr>              
                    </tbody>
                  </div>          
                </v-simple-table>
              </template>      
            </v-card>        
        </v-tab-item>
        <v-tab-item>
          <template>
            <div>   
              <v-col cols="12" md="12">
                <v-card>
                  <v-simple-table>
                    <thead>
                      <tr>
                        <th>시즌 2019</th>
                      </tr>          
                        <th class="text-center"> 순위 </th>
                        <th class="text-right" width="10%"> 팀 </th>
                        <th width="20%"></th>           
                        <th class="text-center"> 승 </th>
                        <th class="text-center"> 패 </th>
                        <th class="text-center"> 무 </th>
                        <th class="text-center"> 승률 </th> 
                        <th class="text-center"> 게임차 </th>          
                    </thead>                      
                  <tbody class="text-center">              
                    <tr v-for="(item, index) in rankings" :key="index">
                      <td> {{ item.id }} </td>
                      <td> <img :src= "item.teamLogo" width="70%"> </td>
                      <td class="text-left"> {{ item.team }} </td>
                      <td> {{ item.victory }} </td>
                      <td> {{ item.defeat }} </td>
                      <td> {{ item.draw }} </td>
                      <td> {{ item.odds }} </td>
                      <td> {{ item.gamesBehind }} </td>
                    </tr>
                  </tbody>
                  </v-simple-table>
                </v-card>
              </v-col>
            </div>
          </template>
        </v-tab-item>
    </v-tabs>
    <v-container>바르고 고운말 사용합시다.
      <v-card>
        <v-col col="6" sm="12">
          <v-row>
            <v-text-field v-model="reply" @keyup.enter="prohibitAdd()"  outlined label="댓글을 입력해주세요."></v-text-field>
            <v-btn @click="prohibitAdd()" large color="grey" class="ma-1" width="60" height="50">입력</v-btn>
          </v-row>
        </v-col>
        <v-list>
          <v-list-item v-for="(comment, index) in comments" :key="index">
            <v-card width="1350">            
              <v-row>                
                <v-list class="body-2"> {{ comment.nick }} </v-list> &nbsp;&nbsp;
                <v-list class="caption"> {{ $moment(comment.reportingDate).format('YY-MM-DD HH:mm') }} </v-list>
                <v-menu>
                  <template v-slot:activator="{on}">
                    <v-card-actions class="pt-0">
                      <div v-if="user && comment.uid == user.uid">
                        <v-btn icon v-on="on"><v-icon>mdi-dots-vertical</v-icon></v-btn>
                      </div>
                    </v-card-actions>
                  </template>
                  <v-list class="pt-0 pb-0">
                    <v-list-item class="pl-0 pr-0">
                      <v-btn text @click="sel(comment); dialog = true" class="mr-0 pr-0"><v-icon size="18">mdi-file-document-edit-outline</v-icon>수정</v-btn>                 
                      <v-btn text @click="del(comment.id)" class="ml-0 pl-0"><v-icon size="18">mdi-delete-outline</v-icon>삭제</v-btn>
                    </v-list-item>
                  </v-list>
                </v-menu>
              </v-row>              
              <v-row>
                <v-list-item-content class="body-2"> {{ comment.content }} </v-list-item-content>              
              </v-row>
              <v-row class="pr-0 pl-0">
                <v-btn icon @click="like(comment.id,index)"><v-icon size="20">mdi-thumb-up-outline</v-icon> {{ comment.like }}</v-btn>
                <v-btn icon @click="disLike(comment.id,index)"><v-icon size="20">mdi-thumb-down-outline</v-icon> {{ comment.disLike }}</v-btn>            
              </v-row>
            </v-card>
          </v-list-item>
        </v-list>

        <v-dialog v-model="prohibit" max-width="300">
          <v-card>
            <v-card-title class="hedline"> 로그인이 필요합니다.</v-card-title>
            <v-card-actions>
              <v-col>
                <div class="text-right">
                  <v-btn color="dark darken-1" text @click="prohibit = false"> 확인 </v-btn>
                </div>
              </v-col>
            </v-card-actions>
          </v-card>
        </v-dialog>
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     
        <v-dialog v-model="dialog" max-width="500">
          <v-card>
            <v-card-title class="headline">댓글 수정</v-card-title>                
            <v-text-field v-model="editReply" solo style="width:500px" class="mt-3" label="댓글을 수정해주세요"></v-text-field>
            <v-card-actions>                
              <v-btn large color="dark darken-1" text @click="dialog = false">
                Cancle
              </v-btn>
              <v-btn large color="blue darken-1" text @click="modi(comment.id), dialog=false">
                Edit
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
         <v-btn v-if="pageButton" block @click="paging()"> 더보기 </v-btn>
      </v-card>
    </v-container>    
  </div>
</template>



<script >  
import Vue from "vue"
import moment from "moment"
import VueMomentJS from "vue-momentjs"

Vue.use(VueMomentJS, moment)

  export default {
    head: {
      title: '야구와구 시즌' 
    },
    data () {
      return {        
        execution: true,
        prevention: [],
        prohibit: false,
        pageButton: false,
        count:0,
        pageSize:10,
        rankings: [],
        path:0,
        date:[],      
        editReply:'',
        dialog: false,
        reply:'',
        comments: [],
        comment: {},
        days: [],        
        model: 0,
        tabs: [
          {name:'3월 2주차', date: '20190311'}, {name:'3월 3주차', date: '20190318'}, {name:'3월 4주차', date: '20190325'}, {name:'4월 1주차', date: '20190401'}, {name:'4월 2주차', date: '20190408'}, {name:'4월 3주차', date: '20190415'},
          {name:'4월 4주차', date: '20190422'}, {name:'4월 5주차', date: '20190429'}, {name:'5월 2주차', date: '20190506'}, {name:'5월 3주차', date: '20190513'}, {name:'5월 4주차', date: '20190520'}, {name:'5월 5주차', date: '20190527'},
          {name:'6월 1주차', date: '20190603'}, {name:'6월 2주차', date: '20190610'}, {name:'6월 3주차', date: '20190617'}, {name:'6월 4주차', date: '20190624'}, {name:'7월 1주차', date: '20190701'}, {name:'7월 2주차', date: '20190708'},
          {name:'7월 3주차', date: '20190715'}, {name:'7월 4주차', date: '20190722'}, {name:'7월 5주차', date: '20190729'}, {name:'8월 2주차', date: '20190805'}, {name:'8월 3주차', date: '20190812'}, {name:'8월 4주차', date: '20190819'},
          {name:'8월 5주차', date: '20190826'}, {name:'9월 1주차', date: '20190902'}, {name:'9월 2주차', date: '20190909'}, {name:'9월 3주차', date: '20190916'}, {name:'9월 4주차', date: '20190923'}, {name:'9월 5주차', date: '20190930'},
          {name:'10월 2주차', date: '20191007'}, {name:'10월 3주차', date: '20191014'}, {name:'10월 4주차', date: '20191021'}
        ]
      }
    },
    computed: {
      user() {
         return this.$store.state.users.user
      }
    },
    created() {
      let date = this.$moment(new Date(Date.now())).format('YYYYMMDD');
      const currentDate = date;
      let candidateDate = Math.abs(this.tabs[0].date - currentDate);
      let candidateIndex = 0;      

      this.tabs.forEach(tab => {
        const diff = Math.abs(tab.date - currentDate);
        if(candidateDate >= diff) {
          candidateDate = diff;
          candidateIndex = this.tabs.indexOf(tab);
        }
      });
      this.model = candidateIndex;

      this.$axios.get("http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/ranking").then((res)=>{
      this.rankings = res.data.reverse();        
       });
      
      this.$axios.get(`http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/schedule/${this.model}`).then((res)=> {
      this.days = res.data.scheduleMap;        
        });        
      this.resetComment();
      // console.log(this.user);
    },    
    methods: {
      sel(comment){
        this.comment = comment;
      },
      prohibitAdd() {
        if(this.user == null) {
          this.prohibit = true;
          this.reply = '';          
        } else {
          this.add()
        }
      },
      add() {        
        if(this.reply !='') {
          this.$axios.post('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/comments',{
            nick: this.user.username,
            uid: this.user.uid,
            content: this.reply,
            reportingDate: new Date().getTime()
          }).then((res) => {
            this.resetComment();
          });          
          this.reply ="";
        }
      },
      del(id) {          
          this.$axios.delete(`http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/comments/${id}`,{
            id: id
          }).
          then((res) => {
            this.resetComment();            
        });
      },
      modi(id) {       
        if(this.editReply!=''){
          this.dialog=false;
            this.$axios.put('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/comments',{          
              id: id,
              nick: this.user.username,
              uid: this.user.uid,
              content: this.editReply,
              reportingDate: new Date().getTime()
            }).
          then((res) => {  
            this.editReply='';
              this.resetComment()        
            })          
          }else{
            return
          };
      },      
      resetComment() {
        this.$axios.get(`http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/comments/${0}/${10}`).then((res) => {
          this.comments = res.data.content;            
          if(res.data.totalElements <= 10) {
              this.pageButton = false;
          } else {
            this.pageButton = true;
          }         
        });
         this.$axios.get('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/overlapPrevention').then((res) => {
            this.prevention = res.data;            
          })
      },
      paging() {        
        this.pageSize += 10;
        this.$axios.get(`http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/comments/${0}/${this.pageSize}`).then((res) => {
        this.comments = res.data.content;         
        });        
      },
      like(id,index) {
        this.execution = true;
        if(this.user != null) {
          this.$axios.get('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/overlapPrevention').then((res) => {
            this.prevention = res.data;
              // console.log(this.prevention);              
          })
          this.prevention.forEach(prevention =>{
            if(this.user.uid == prevention.uid && id == prevention.cid) {
              this.execution = false;
                this.$axios.get('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/overlapPrevention').then((res) => {
                  this.prevention = res.data;              
          })
              return
          }})
          if(this.execution == true) {
            this.count = this.comments[index].like += 1;
              this.$axios.put('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/comments',{
                id: id,
                like: this.count,
                reportingDate: this.comments[index].reportingDate,
                nick: this.comments[index].nick,
                content: this.comments[index].content,
                uid: this.comments[index].uid,
                disLike: this.comments[index].disLike
                })
                this.$axios.post('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/overlapPrevention',{
                  cid: id,
                  uid: this.user.uid
                  })
                  this.resetComment();
            }
        } else {
          this.prohibit = true;
          return
        }
      },
      disLike(id,index) {
        this.execution = true;
        if(this.user != null) {
          this.$axios.get('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/overlapPrevention').then((res) => {
            this.prevention = res.data;              
          })
          this.prevention.forEach(prevention =>{
            if(this.user.uid == prevention.uid && id == prevention.cid) {
              this.execution = false;
                this.$axios.get('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/overlapPrevention').then((res) => {
                  this.prevention = res.data;              
          })
              return
          }})
          if(this.execution == true) {
            this.count = this.comments[index].disLike += 1;
              this.$axios.put('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/comments',{
                id: id,
                like: this.comments[index].like,
                reportingDate: this.comments[index].reportingDate,
                nick: this.comments[index].nick,
                content: this.comments[index].content,
                uid: this.comments[index].uid,
                disLike: this.count
                })
                this.$axios.post('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/overlapPrevention',{
                  cid: id,
                  uid: this.user.uid
                  })
                  this.resetComment();
            }
        } else {
          this.prohibit = true;
          return
        }      
      },
      onClickButton(index) {
        this.days = [];      
        this.$axios.get(`http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/schedule/${this.tabs[index].date}`).then((res) => {
          this.days = res.data.scheduleMap;
            console.log(this.days)
          // for(let [index1, item1] of this.days.entries()) {
          //   for(let [index2, item2] of item1.scheduleList.entries()) {
          //     // console.log(index2);
          //     // console.log(item2);              
          //     this.$axios.post('http://ec2-15-164-102-10.ap-northeast-2.compute.amazonaws.com:9090/schedule',{                           
          //       gameDate: item2.gameCenterFormattedDate,
          //       awayTeamLogo: item2.awayTeamEmblemUrl,
          //       homeTeamLogo: item2.homeTeamEmblemUrl,
          //       homeTeam: item2.homeTeamFullName,
          //       awayTeamScoare: item2.awayTeamScore,
          //       homeTeamScoare: item2.homeTeamScore,
          //       winner: item2.winner                
          //     }).then((res) => {});
            //  }
            // }
        });
      }      
    }
  }
</script>