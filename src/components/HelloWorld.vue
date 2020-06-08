<template>
  <div class="hello">
    <div class="searchFilt">
      <label for="search">Search titles:</label>
      <input v-model="searchTerm" type="text" id="search" v-on:keyup="updateList">

      <label for="type">Type:</label>
      <select v-model="filterType" v-on:change="validationForSearch" name="type" id="type" > <!-- ubacivanje v- opcija cini da se ne prikazuje movie kao default opcija, nego stoji prazan dropdown (cak i kad promenim v-model'd filterType text) -->
        <option value="" >All</option>
        <option value="movie" >Movie</option>
        <option value="series" selected>Series</option>
        <option value="episode">Episode</option>
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
            console.log(this.list)
            this.generateImbdUrl()
            this.validatePoster()

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
    },
    validationForSearch(){
      if(this.searchTerm.length >= 3){
        this.updateList()
      } 
    },
    validatePoster(){
      for(var i = 0; i < this.list.length; i++){
        if(this.list[i].Poster == 'N/A'){
          this.list[i].Poster = 'https://newprojects.99acres.com/projects/unknown/united_infra/images/zj7xmckr.jpg';
        }
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.hello {
  font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
  .searchFilt {
    background-color:#751B51;
    padding:20px 0;
    label {
      margin-left: 20px;
      margin-right: 8px;
      color:#fff;
      &:hover {
        cursor:pointer;
      }
    }
    input {
      padding:5px;
    }
    select{
      padding:5px;
      &:hover {
        cursor:pointer;
      }
    }
  }
  ul{
    list-style-type: none;
    margin:0px 80px;
    padding:0 10px;
    li{
      margin-bottom:35px;
      padding:30px 100px 50px 100px;
      &:nth-child(odd){
        background-color:rgb(5, 163, 34);
        a {
        color:#fff;
          h2 {
            background-color:rgb(11, 53, 19);
            padding:5px;
          }
          p {
            background-color:rgb(11, 53, 19);
            padding:5px;
          }
        }
      }
      &:nth-child(even){
        background-color:#C2405C;
        a {
        color:#fff;
          h2 {
            background-color:#C21938;
            padding:5px;
          }
          p {
            background-color:#C21938;
            padding:5px;
          }
        }
      }
      a {
        text-decoration: none;
        img{
          max-width: 300px;
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
