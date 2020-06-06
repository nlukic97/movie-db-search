<template>
  <div class="hello">
    <input v-model="searchTerm" type="text" v-on:keyup="updateList">
    <ul v-if="list">

      <li v-for="(item, index) in list" v-bind:key="index">
        <a v-bind:href="item.imbdID" target="_target" title="Open in imbd">
          <img v-bind:src="item.Poster" alt="">
          <h2>{{item.Title}} ({{item.Year}})</h2>
          <p>{{item.Type}}</p>
        </a>
      </li>

    </ul>
    <p v-if="error">{{this.error}}</p>
  </div>
</template>

<script>
import axios from 'axios'; //ovako importujes axios module koji ce hvatati info. ovako za module vrsis import
export default {
  name: 'HelloWorld',
  data(){
    return {
      apiKey:'34dace2e',
      searchTerm:'',
      list: null,
      error:'Search for something...'
    } 
  },
  methods: {
    updateList: function(){
      axios
        .get('http://www.omdbapi.com/?s=' + this.searchTerm + '&apikey=34dace2e')
        .then(response => { //kad imas arrow funkciju, ne treba zagrada.
          if(response.data.Response == 'True'){
            this.list = response.data.Search;
            this.error = null;
            console.log(this.list)
            
            for(var i = 0; i < this.list.length; i++){  // Zasto ovde ispod vucem ovako ?
              this.list[i].imbdID =' https://www.imdb.com/title/' + this.list[i]['imdbID'] + '/'
              // this.list[i].imbdID =' https://www.imdb.com/title/' + this.list[i].imdbID + '/' // zasto ne bi ovo radilo ?
              
              // console.log(this.list[i].imbdID)
              // console.log(this.list[i]['imbdID'])
            }
          } else {
            if(response.data.Error == 'Something went wrong.'){
              this.error = 'Search for something...';
            } else {
              this.error = response.data.Error;
            }
            this.list = null;
          }
        })
        .catch(this.error = 'Something went wrong. Please try again')
    },
    openInImdb: function(id){
      console.log(id);
      // console.log(this.list)
      window.open('https://www.imdb.com/title/'+ id + '/');
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.hello {
  // input {

  // }
  ul{
    list-style-type: none;
    li{
      margin-bottom:35px;
      a {
        text-decoration: none;
        color:#333;
        img{
        &:hover {
        cursor:pointer;
        }
      }
      h2 {
        // margin:4px;
        margin:15px 0 5px 0;
        &:hover {
        cursor:pointer;
        }
      }
      p{
        margin:0;
        &:hover {
        cursor:pointer;
        }
      }
      }
    }
  }
}
</style>
