<template>
  <div>
    <div v-for="p in weatherNow" :key="p.id">
      <h2>Санкт-Петербург</h2>
      <h3>
        {{p.description}}
      </h3>
      <h1>
        {{p.temp}} &deg;
      </h1>
    </div>
    <div class="weather_blocks">
      <div v-for="p in addPost()" :key="p.id" class="block">
        <div>
          {{p.date}}
        </div>
        <div>
          <img :src="p.icon" alt="" class="img">
        </div>
        <div>
          {{p.temp}} &deg;
        </div>
        <div class="wind">
          <p>ветер</p>
          {{p.speed}}
        </div>
      </div>
    </div>
  </div>
</template>

<script>

import axios from 'axios'

  export default {
    data() {
      return {
        post: [],
        weatherNow: [],
        theme: false,
      }
    },
    methods: {
      addPost(){
        const newPost = [];
        for(let i = 0; i < this.post.length; i++){
          const date = new Date(this.post[i].dt_txt).getHours();
          let post = {
            date: String(date).padStart(2, '0'),
            temp: Math.round(this.post[i].main.temp - 273),
            icon: `http://openweathermap.org/img/wn/${this.post[i].weather[0].icon}@2x.png`,
            speed: this.post[i].wind.speed + ' м/с',
            description: this.post[i].weather[0].description,
          }
          newPost.push(post);
        }
        
        if(newPost[0]){
          newPost[0].date = 'Сейчас';
          if(this.weatherNow.length < 1){
            this.weatherNow.push(newPost[0]);
          }
        }
        return newPost;
      },
    },

    created() {
      axios
        .get('https://api.openweathermap.org/data/2.5/forecast?q=Sankt-Peterburg&local_names&limit=4&lang=ru&appid=3fd04b1c269ee29de1857ca08f976601')
        .then(response => this.post = response.data.list.splice(0,6))
    },
  }
 
</script>

<style>
#app {
  padding: 10px;
}
body {
  background:  linear-gradient(rgb(5, 5, 8), rgb(91, 164, 223));
}

.weather_blocks {
  display: flex;
  flex-direction: row;
}

.block {
  display: flex;
  font-size: 15px;
  width: 100px;
  font-size: 14px;
  padding: 5px;
  flex-direction: column;
}
.wind {
  position: relative;
  margin: 0 auto;
  margin-top: 15px;

}
.wind::before {
  position: absolute;
  content: '';
  top: -10px;
  left: 0;
  bottom: 0;
  right: 0;
  width: 35px;
  height: 12px;
  margin: 0 auto;
  border-top: 1px solid rgb(247, 247, 247);
}

.wind p {
  margin: 0;
}
.img {
  filter: brightness(0) invert(1);
}

@media screen and (max-width: 750px) {
  .img {
    width: 80px;
  }
  .weather_blocks {
    padding-bottom: 15px;
    overflow: auto;
    width: 600px;
    scrollbar-width: thin;
    scrollbar-color: hsl(0 0% 50%);
  }
}

@media screen and (max-width: 600px) {
  .weather_blocks {
    width: 400px;
  }
}

@media screen and (max-width: 450px) {
  .weather_blocks {
    width: 400px;
  }
}

@media screen and (max-width: 421px) {
  .weather_blocks {
    width: 300px;
  }
}
</style>