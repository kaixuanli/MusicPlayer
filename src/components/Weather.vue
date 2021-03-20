<template>
    <div id="weather">
        <h2>天气查询</h2>
        <div>
            <input v-model="searchWord" @keyUp.enter="searchWeather" type="text" placeholder="请输入查询天气的地名">
            <button @click="searchWeather">搜索</button>
        </div>
        <div>
            <a href="#" @click="changeCity('北京')">北京</a>&nbsp;
            <a href="#" @click="changeCity('上海')">上海</a>&nbsp;
            <a href="#" @click="changeCity('广州')">广州</a>&nbsp;
            <a href="#" @click="changeCity('深圳')">深圳</a>&nbsp;
        </div>
        <ul>
            <li v-for="item in weartherList" :key="item.date">
                <span>{{item.type}}</span>
                <span>{{item.low}}</span>
                <span>{{item.high}}</span>
                <span>{{item.date}}</span>
            </li>
        </ul>
    </div>          
</template>
<script>
import axios from 'axios' 

export default {
    data() {
        return {
             searchWord:"北京",
            weartherList:[]  
        }
    },
    methods: {
        searchWeather:function() {
            if(this.searchWord==""){
                alert("请输入城市名");
                return;
            }
            var that = this;
            axios.get('http://wthrcdn.etouch.cn/weather_mini?city=' + this.searchWord).then(
                function(response){
                    that.weartherList = response.data.data.forecast;
                },
                function(error){
                    alert("请输入正确的城市名哦" + error);
                }
            )  
        },

        changeCity:function(city) {
            this.searchWord = city;
            this.searchWeather();
        }
    }
}
</script>
<style scoped>
  span {
      display: inline-block;
      width: 90px;
  }            
</style>