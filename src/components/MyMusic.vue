<template>
<el-row id="lkx" style="height: 100%;" type="flex" justify="center" align="middle">
    <el-row>
         <el-col style="width: 1000px; height: 720px;">
            <el-container>
                <!-- 头部 -->
                <el-header style="background-color: black; ">
                    <el-row>
                        <el-col :span="3">
                            <div class="MusicPlayerName">随便听听</div>
                        </el-col>              
                        <el-col :span="6" :offset="14">
                            <div class="search">
                                <el-input placeholder="搜索歌曲" prefix-icon="el-icon-search" v-model="keyWords" @keyup.enter.native="searchMucis"></el-input>
                            </div>
                        </el-col>
                    </el-row>
                </el-header>
                <!-- 歌曲列表主体 -->
                <el-main>
                    <el-row style="background-color: pink;">
                        <el-col :span="8" style="height: 600px;">
                            <div class="scrollDiv">
                                <ul>
                                    <li v-for="item in musicList" :key="item.id">
                                        <i class="el-icon-video-play" @click="plarMusic(item.id,item.name)"></i>
                                        <span style="width: 10px"> </span>
                                        <span>{{item.name}}</span>
                                        <span style="width: 10px"> </span>
                                        <i v-if="item.mvid!=0" class="el-icon-video-camera" @click="playMv(item.mvid)"></i>
                                    </li>
                                </ul>
                            </div>
                        </el-col>
                        <!-- 歌曲封面 -->
                        <el-col :span="8" class="cover">
                            <div style="height: 100%">
                                <el-row>
                                    <el-col :span="24"><div style="height: 150px;"></div></el-col>
                                </el-row>
                                <el-row type="flex" justify="center" style="height: 350px;">
                                    <img id="coverImg" class="coverImg" :class="{animation:isPlaying}" :src="musicCover" alt="歌曲封面">
                                </el-row>
                                <el-row type="flex" justify="center" align="bottom" style="height: 100px">
                                    <div>
                                        <span>{{musicName}}</span>
                                    </div>
                                </el-row>
                            </div>
                        </el-col>

                        <el-col :span="8" style="height: 600px;">
                            <div class="scrollDiv">
                                <h3>热门评论</h3>
                                <dl v-for="item in musicComment" :key="item.id">
                                    <dt><img class="user-head-portrait" :src="item.user.avatarUrl" alt="评论用户的头像"></dt>
                                    <dd class="userName">{{item.user.nickname}}</dd>
                                    <dd>{{item.content}}</dd>
                                </dl>
                            </div>
                        </el-col>
                    </el-row>
                </el-main>
                <el-footer class="footer">
                    <audio ref="audio" :src="musicUrl" @play="play" @pause="pause" controls autoplay loop></audio> 
                </el-footer>
            </el-container>
        </el-col>
    </el-row>

    <!-- mv遮罩层 -->
    <div id="videoView" v-show="isShow" >
        <video :src="mvUrl" controls autoplay loop></video> 
        <div class="shadow" @click="closeMV"></div>
    </div>

</el-row>
</template>
<script>
import axios from 'axios' 

export default {
    name: 'MyMusic',
    data: function(){
        return{
            // 搜索关键字
            keyWords: "许嵩",
            // 搜索列表
            musicList: [],
            // 音乐地址
            musicUrl: "",
            // mv地址
            mvUrl: "",
            // 歌曲封面
            musicCover: require("../assets/images/play.jpeg"),
            // 歌曲已有评论
            musicComment: [],
            // 当前播放歌曲名
            musicName: "",
            // 当前是否在播放
            isPlaying: false,
            // mv是否在播放
            isShow: false
        }
    },
    methods: {
        searchMucis:function() {
            var that = this; 
            axios.defaults.withCredentials = true;
            axios.get('http://musicapi.leanapp.cn/search?keywords=' + this.keyWords).then(
                function(response){
                    that.musicList = response.data.result.songs;
                },
                function(error){
                    console.log('发送请求出错啦' + error); 
                }
            )
        },

        plarMusic:function(musicId, musicName){
            var that = this;
            axios.defaults.withCredentials = true;
            // 歌曲地址
            axios.get('https://autumnfish.cn/song/url?id=' + musicId).then(
                function(response){
                    that.musicUrl = response.data.data[0].url;
                    that.musicName = musicName;
                },
                function(error){
                    console.log('发送请求出错啦' + error); 
                }
            )

            // 歌曲详情-封面
            axios.get('https://autumnfish.cn/song/detail?ids=' + musicId).then(
                function(response){
                    that.musicCover = response.data.songs[0].al.picUrl;
                },
                function(error){
                    console.log('发送请求出错啦' + error); 
                }
            )

            // 歌曲评论
            axios.get('https://autumnfish.cn/comment/hot?type=0&id=' + musicId).then(
                function(response){
                    that.musicComment = response.data.hotComments;
                },
                function(error){
                    console.log('发送请求出错啦' + error); 
                }
            )
        },
    
        play:function(){
            this.isPlaying = true;
        },

        pause:function(){
            this.isPlaying = false;
        },  

        playMv:function(mvId){
            // 暂停放歌
            if(this.isPlaying){
                this.$refs.audio.pause();
            }
            var that = this;
            axios.defaults.withCredentials = true;
            // mv地址
            axios.get('https://autumnfish.cn/mv/url?id=' + mvId).then(
                function(response){
                    that.mvUrl = response.data.data.url;
                    that.isShow = true;
                },
                function(error){
                    console.log('发送请求出错啦' + error); 
                } 
            )
        },

        closeMV:function(){
            this.mvUrl = "";
            this.isShow = false;
        }
    }
}
</script>
<style scoped>
   .MusicPlayerName {
       line-height: 60px; 
       color:white; 
       font-size:24px; 
       text-align: center;
   }

   .search {
       height: 60px; 
       display: flex;
       align-items: center;
   }

   .el-main, .el-footer {
       padding: 0;
   }

    i {
        float: right;
    }

   .cover {
       border: solid 2px burlywood;
       border-top: none;
       border-bottom: none;
       height: 600px;
   }
    
    audio {
        width: 100%;
        height: 60px;
    }

    .footer {
        background-color: #eff1f2;
    }
    
    .scrollDiv {
        overflow: scroll; 
        height: 100%;
    }

    li {
        list-style: none;
        line-height: 35px;
        display: flex;
        align-items: center;
    }

    #playImg {
        width: 30px;
        height: 30px;
        border-radius: 50%;
    }

    .coverImg {
        position: relative;
        width: 300px;
        height: 300px;  
        border-radius: 50%;  
    }

    .animation {
         /* 旋转动画 infinite无限 */
         animation: coverAnimation 10s infinite;
    }
    @keyframes coverAnimation{
        100%{
            transform: rotate(360deg);
        }
    }

    .user-head-portrait {
        width: 40px;
        height: 40px;
        border-radius: 50%;
    }

    dl {
        width: 100%;
        height: auto;
    }
    dt {
        float: left;
        height: 100%;
    }

    .userName {
        color: #f56c6c;
    }

    #videoView {
        position: fixed;
        width: 100%;
        height: 100%;
        top: 0;
        left: 0;
        z-index: 997;
    }

    video {
        position: absolute;
        height: 720px;
        top: 50%;  
        left: 50%;
        transform: translate(-50%,-50%);
        z-index: 999;
    }
    .shadow {
        position: absolute;
        width:100%;
        height:100%;
        position: fixed;
        left:0;
        top:0;
        z-index:998;
        background-color:#000;
        opacity:0.8;
        display:true;
    }
</style>