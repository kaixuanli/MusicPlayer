<template>
<div id="timeCompement">
    <span>{{ weekday }}</span>&nbsp;&nbsp;
    <span>{{ time }}</span>
</div>
</template>
<script>
export default {
    data() {
        return {
            time: new Date().toLocaleString(),
            weekday: "星期一",
        }
    },
    
    mounted() {
        let _this = this;
        this.timer = setInterval(function(){
            _this.time = new Date().toLocaleString();
            let aweek = new Array("星期天", "星期一", "星期二", "星期三", "星期四", "星期五", "星期六");
            _this.weekday = aweek[new Date().getDay()]
        }, 1000);
    },

    beforeDestroy() {
        if (this.timer) {
            // 在Vue实例销毁前，清除我们的定时器
            clearInterval(this.timer);
        }
    }
}
</script>
<style scoped>
#timeCompement {
    height: 100px;
    width: auto;
    font-size: 80px;
}

</style>