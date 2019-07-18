<template>
  <div>
    <section class="layout" :getEditState="getEditState" :getBookInfo="getBookInfo" :getBookId="getBookId">
      <div v-if="this.getEditState == false">
        <div  class="content-1 mb-20">
          <h3>{{this.bookInfo.author}}</h3>
          <h3>{{this.bookInfo.publicationDate}}</h3>    
        </div>  
        <p>{{this.bookInfo.description}}</p>
      </div>
      <div v-if="this.getEditState">
        <div class="content-1 mb-20">
          <input v-model="bookInfo.author" />
          <input v-model="bookInfo.publicationDate" />
        </div>
        <div class="mb-20"><textarea v-model="bookInfo.description" /></div>
        <button @click="callUpdatedApi()">updated</button>
      </div>
    </section>
  </div>
</template>
<script>
// import Vue from 'vue'
import axios from 'axios'
import store from '../store.js'
import { isArray } from 'util';
export default {
  components: {
  },
  data() {
    return {
      store,
      currentBook:[],
      bookInfo:{
        author:'',
        publicationDate:'',
        description:'',
      }

    };
  },

  created() { 
  },
  mounted(){
    this.showBookInfo()
  },
  methods: {
    showBookInfo(){
      if(this.getBookInfo.length && this.getBookId.length){
        let num = this.getBookInfo.length -1
         this.currentBook = this.getBookInfo[num].filter((e) => {
          return e['@id'] == this.getBookId
        });
        this.$store.commit("saveCurrentBook",this.currentBook)
        
        //輸入預設給v-model綁定的bookInfo
        this.bookInfo.author = this.currentBook[0].author
        this.bookInfo.publicationDate = this.currentBook[0].publicationDate
        this.bookInfo.description = this.currentBook[0].description
      }


    },
    callUpdatedApi(){
      let author = this.bookInfo.author
      let publicationDate = this.bookInfo.publicationDate
      let description = this.bookInfo.description
      let updatedBook = this.currentBook.map((e) => {
        if(e.author == author && e.publicationDate == publicationDate && e.description == description){
          alert("你尚未更新喔～")
        }
        else{
          if(e.author != author){
            this.$set(e, 'author',author)
          }
          if(e.publicationDate != publicationDate){
            this.$set(e,'publicationDate',publicationDate)
          }
          if(e.description != description){
            this.$set(e,'description',description)
          }
        }
        console.log("my updatedInfo",e)
        return e
      }
      );

      let rs = { "book" : updatedBook[0] }
      let id = updatedBook[0]["@id"]
      let url = "https://demo.api-platform.com" + id 
      axios.put(url,rs)
        .then(response => {
          console.log('updated good', response)
          alert('修改成功～～')
          this.$router.push({path:'/bookList'})
        }, error => {
          console.log('updated bad', error)
        })
    }
  },
  computed:{
    getBookInfo(){
      return this.$store.state.groupList
    },
    getBookId(){
      return this.$store.state.bookId
    },
    getEditState(){
      return this.$store.state.editState
    }
  }
}
</script>
<style lang="scss" scope>
@import '../assets/style/share.scss';
.layout{
  height:100vh;
  p{
    text-align: left;
    font-size:25px;
  }
  textarea{
    width:calc(100vw - 100px);
    height:40vh;
    color: #2c3e50;
    border:0px solid;
    font-size:25px;
  }
  input{
    color: #7f8c8d;
    border:0px solid;
    font-size:20px;
    font-weight: 500;
  }
}
.content-1{
  @extend .flex-row;
  justify-content: space-between;
  color: #7f8c8d;
  //outline: 2px solid;
}
</style>


