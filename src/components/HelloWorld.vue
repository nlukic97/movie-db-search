<template>
  <div class="hello">
    <input v-model="searchTerm" type="text" v-on:keyup="updateList">
    <div>
    <label for="type">Type:</label>
      <select v-model="filterType" v-on:change="updateList" name="type" id="type" > <!-- ubacivanje v- opcija cini da se ne prikazuje movie kao default opcija, nego stoji prazan dropdown (cak i kad promenim v-model'd filterType text) -->
        <option value="movie" >movie</option>
        <option value="series" selected>series</option>
        <option value="episode">episode</option>
      </select>
    </div>
    <ul v-if="list">
      <li v-for="(item, index) in list" v-bind:key="index">
        <a v-bind:href="item.imbdID" target="_blank" title="Open in imbd">
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
      filterType:'', //promena ovoga ce idalje napraviti da bude prazan checkbox zbog vue opcija u select tagu
      list: null,
      error:'Search for something...' //error ako samo promenimo opciju filma --> pojavi se na brzaka 'error message' i onda pise ovo. Dodatna validacija za pormenu filma
    } 
  },
  methods: {
    updateList: function(){
      console.log(this.filterType)
      axios
        .get('http://www.omdbapi.com/?s=' + this.searchTerm + '&type=' + this.filterType + '&apikey=34dace2e')
        .then(response => { //kad imas arrow funkciju, ne treba zagrada.
          if(response.data.Response == 'True'){
            this.list = response.data.Search;
            this.error = null;
            // console.log(this.list)
            this.generateImbdUrl()

            //if the for loop from the generateImbdUrl was here, one method would not work. Take a look at question.vue 
            
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
    generateImbdUrl(){ //description:   take the id, and in our list add imbd url and append id
      for(var i = 0; i < this.list.length; i++){ 
        this.list[i].imbdID =' https://www.imdb.com/title/' + this.list[i].imdbID + '/' 
        // console.log(this.list[i].imbdID)
        // console.log(this.list[i]['imbdID'])
      }
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
