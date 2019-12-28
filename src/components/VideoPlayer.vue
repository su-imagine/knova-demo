<template>
    <div class="video-wrapper">
        <div class="video-wrapper" v-if="true">
           <video ref="videoPlayer" class="video-js"></video>
        </div>
        <div v-else class="device-off">
            <div>设备已下线</div>
            <div class="deviceName">幼儿园入口内（正）</div>
        </div>
    </div>
</template>
<style scoped>
    .device-off{
        position: relative;
        display: flex;
        justify-content: center;
        align-items: center;
        width: 100%;
        height: 100%;
        border:1px solid #979797;
        /*background: #000;*/
        /*color: #fff;*/
    }
    .video-wrapper{
        height: 100%;
    }
    .deviceName{
        position: absolute;
        top: 5px;
        left: 5px;
    }
</style>

<script>
import videojs from 'video.js';
import'video.js/dist/video-js.css';
export default {
    name: "VideoPlayer",
    props: {
        options: {
            type: Object,
            default() {
                return {};
            }
        }
    },
    watch: {
        player(val) {
            console.log('123123123123')
            if(this.player) {
                console.log(this.options.sources[0].src);
                this.player.src(this.options.sources[0].src); 
            }
        }
    },
    data() {
        return {
            player: null
        }
    },
    mounted() {
        console.log(this.options)
        this.player = videojs(this.$refs.videoPlayer, this.options, function() {
            this.on('error',() => {
                console.log('2222222222')
            })
        });

    },
    beforeDestroy() {
        if (this.player) {
            this.player.dispose()
        }
    }
}

</script>

