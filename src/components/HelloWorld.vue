<template>
  <div class="hello container">
    <h1 class="text-center">List of Players</h1>
    <h3>Search</h3><input type="text" v-model="searchQuery">
    <div class="row">
      <div class="col-md-3" v-for="player in resultQuery" :key="player.id">
        <div class="card">
          <img class="card-img-top" :src="getImgUrl(player.Id)" alt="Card image cap">
          <div class="card-body">
            <h5 class="card-title">{{player.PFName}} {{player.Id}}</h5>
            <p class="card-text">{{player.SkillDesc}}</p>
            <h6>Vlaue: $ {{player.Value}}</h6>
            <h6>Upcoming Match: <br>{{player.UpComingMatchesList[0].CCode}} vs {{player.UpComingMatchesList[0].VsCCode}}</h6>
            <h6>Date: {{getLocalTime(player.UpComingMatchesList[0].MDate)}}</h6>
          </div>
        </div>
      </div>
    </div>
    <h1 ></h1>
  </div>
</template>

<script>
import utcLocal from 'utc-local'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      result: null,
      teamsList: null,
      playerList: [],
      responseAvailable: false,
      searchQuery: ""
    }
  },
  created() {
    this.responseAvailable = false;
    fetch("https://api.npoint.io/d6bd0efc05639084eb17/", {
      "method": "GET",
    })
    .then(response => { 
        if(response.ok){
            return response.json()    
        } else{
            alert("Server returned " + response.status + " : " + response.statusText);
        }                
    })
    .then(response => {
      console.log(response)
        this.result = response; 
        this.playerList = response.playerList,
        this.teamsList = response.teamsList
        this.responseAvailable = true;
    })
    .catch(err => {
        console.log(err);
    });
  },
  methods: {
    getImgUrl(Id) {
      let images = "/static/player-images/"+Id+".jpg"
      return images
    },
    getLocalTime(time) {
      return utcLocal.getLocal(time)
    },
    sortFunc: function (){
      return this.playerList.sort(function(a, b){
        return (a.Value > b.Value) ? 1 : -1;
      });
    }
  },
  computed: {
    resultQuery(){
      if(this.searchQuery){
        return this.sortFunc().filter((item)=>{
          return item.PFName.toLowerCase().startsWith(this.searchQuery.toLowerCase());
      })
      }else{
        return this.sortFunc();
      }
    }
  }
}
</script>
<style scoped>

</style>
