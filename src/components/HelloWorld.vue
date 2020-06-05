<template>
  <div class="hello">
    <input v-model="searchTerm" type="text" v-on:keyup="updateList">
    <ul v-if="list">
      <li v-for="(item, index) in list" v-bind:key="index">
        <img v-bind:src="item.Poster" alt="">
        <h2>{{item.Title}} ({{item.Year}})</h2>
        <p>{{item.Type}}</p>
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
            console.log(response.data.Search)
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
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

</style>
