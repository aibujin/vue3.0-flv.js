<script setup lang="ts">
import flvjs from 'flv.js'
import { onMounted, ref, onUnmounted } from 'vue'

const flvPlayer: any = ref()

onMounted(() => {
    console.log('isSupported: ' + flvjs.isSupported())
    console.log('是否支持点播视频：' + flvjs.getFeatureList().mseFlvPlayback)
    console.log(
        '是否支持httpflv直播流：' + flvjs.getFeatureList().mseLiveFlvPlayback
    )
    initFlv()
})

/**
 * 创建 flvjs 实例
 */
const initFlv = () => {
    const ele = document.getElementById('video-contianer')
    flvPlayer.value = flvjs.createPlayer({
        type: 'flv', // 指定视频类型
        isLive: true, // 开启直播
        hasAudio: false, // 关闭声音
        cors: true, // 开启跨域访问
        url: 'https://mister-ben.github.io/videojs-flvjs/bbb.flv', // 指定流链接
    })
    // 将flvjs对象和DOM对象绑定
    flvPlayer.value.attachMediaElement(ele)
    play()
    flvEvent()
}

const play = () => {
    flvPlayer.value.load()
    flvPlayer.value.play()
}

// flvjs播放器事件侦听
const flvEvent = () => {
    // 视频错误信息回调
    flvPlayer.value.on(
        flvjs.Events.ERROR,
        (errorType: any, errorDetail: any, errorInfo: any) => {
            console.log(
                '类型:' + JSON.stringify(errorType),
                '报错内容' + errorDetail,
                '报错信息' + errorInfo
            )
        }
    )
    //【重要事件监听】http 请求建立好后，该事件会一直监听 flvjs 实例
    flvPlayer.value.on(
        flvjs.Events.STATISTICS_INFO,
        (errorType: any, errorDetail: any, errorInfo: any) => {
            console.log(
                '类型:' + JSON.stringify(errorType),
                '报错内容' + errorDetail,
                '报错信息' + errorInfo
            )
        }
    )
}

/**
 * 重新加载视频
 */
const load = () => {
    if (flvPlayer.value != null) {
        destory()
    }
    initFlv()
}
/**
 * 播放
 */
const start = () => flvPlayer.value.play()
/**
 * 暂停
 */
const pause = () => flvPlayer.value.pause()
/**
 * 销毁
 */
const destory = () => {
    flvPlayer.value.pause()
    flvPlayer.value.unload()
    flvPlayer.value.detachMediaElement()
    flvPlayer.value.destroy()
    flvPlayer.value = null
}
/**
 * 截图
 */
const screenshot = () => {
    const ele = document.getElementById('video-contianer') as HTMLVideoElement
    const canvas = document.createElement('canvas') as HTMLCanvasElement
    canvas.width = ele.clientWidth
    canvas.height = ele.clientHeight
    const ctx = canvas.getContext('2d') as CanvasRenderingContext2D
    ctx.drawImage(ele, 0, 0, ele.clientWidth, ele.clientHeight)
    viewPicture(canvas.toDataURL('image/jpeg'))
}
/**
 * 截图预览
 */
const viewPicture = (url: string) => {
    const id = 'viewPicture' + new Date().getTime()
    var container = document.createElement('div')
    container.id = id
    container.style.cssText =
        'position: fixed;right:0;bottom:0;height:200px;width:300px;transition: 3s;'
    var img = document.createElement('img')
    img.style.cssText = 'width:100%;height:100%;object-fit: inherit;'
    img.src = url
    container.appendChild(img)
    document.body.appendChild(container)

    setTimeout(() => {
        container.style.width = '0'
        container.style.height = '0'
    }, 3000)
    setTimeout(() => {
        document.body.removeChild(document.getElementById(id) as HTMLElement)
    }, 5000)
}
/**
 * 缩放
 */
const zoom = () => {
    if (!!isFullscreen()) exitFullScreen()
    else requestFullScreen()
}

const isFullscreen = () => {
    const documentScreenElement = document as Document & {
        mozFullScreenElement(): Promise<void>
        webkitFullscreenElement(): Promise<void>
        msFullscreenElement(): Promise<void>
    }
    return (
        documentScreenElement.fullscreenElement ||
        documentScreenElement.msFullscreenElement ||
        documentScreenElement.mozFullScreenElement ||
        documentScreenElement.webkitFullscreenElement ||
        false
    )
    // return (
    //     document.fullscreenElement ||
    //     document.msFullscreenElement ||
    //     document.mozFullScreenElement ||
    //     document.webkitFullscreenElement ||
    //     false
    // )
}
const requestFullScreen = () => {
    let documentRequestScreenElement: any = null
    documentRequestScreenElement = document.getElementById(
        'video-contianer'
    ) as HTMLElement & {
        webkitRequestFullScreen(): Promise<void>
        mozRequestFullScreen(): Promise<void>
        msRequestFullScreen(): Promise<void>
    }

    var requestMethod =
        documentRequestScreenElement.requestFullScreen ||
        documentRequestScreenElement.webkitRequestFullScreen ||
        documentRequestScreenElement.mozRequestFullScreen ||
        documentRequestScreenElement.msRequestFullScreen
    if (requestMethod) {
        requestMethod.call(documentRequestScreenElement)
    }

    // js 写法
    // const element = document.getElementById('video-contianer')
    // var requestMethod =
    //     element.requestFullScreen ||
    //     element.webkitRequestFullScreen ||
    //     element.mozRequestFullScreen ||
    //     element.msRequestFullScreen
    // if (requestMethod) {
    //     requestMethod.call(element)
    // } else if (typeof window.ActiveXObject !== 'undefined') {
    //     var wscript = new ActiveXObject('WScript.Shell')
    //     if (wscript !== null) {
    //         wscript.SendKeys('{F11}')
    //     }
    // }
}

const exitFullScreen = () => {
    const documentFullScreenElement = document as Document & {
        mozCancelFullScreen(): Promise<void>
        webkitExitFullscreen(): Promise<void>
        msExitFullscreen(): Promise<void>
    }
    var exitMethod =
        documentFullScreenElement.exitFullscreen ||
        documentFullScreenElement.mozCancelFullScreen ||
        documentFullScreenElement.webkitExitFullscreen ||
        documentFullScreenElement.msExitFullscreen
    if (exitMethod) {
        exitMethod.call(documentFullScreenElement)
    }

    // js 写法
    // var exitMethod =
    //     document.exitFullscreen ||
    //     document.mozCancelFullScreen ||
    //     document.webkitExitFullscreen ||
    //     document.msExitFullscreen
    // if (exitMethod) {
    //     exitMethod.call(document)
    // } else if (typeof window.ActiveXObject !== 'undefined') {
    //     var wscript = new ActiveXObject('WScript.Shell')
    //     if (wscript !== null) {
    //         wscript.SendKeys('{F11}')
    //     }
    // }
}

onUnmounted(() => {
    destory()
})
</script>

<template>
    <div class="mainContainer">
        <video id="video-contianer" autoplay muted controls width="1024" height="576">
            Your browser is too old which doesn't support HTML5 video.
        </video>
        <button @click="load">Load</button>
        <button @click="start">Start</button>
        <button @click="pause">Pause</button>
        <button @click="destory">Destory</button>
        <button @click="screenshot">Screenshot</button>
        <button @click="zoom">Zoom</button>
    </div>
</template>

<style scoped>
.read-the-docs {
    color: #888;
}
.mainContainer {
    display: block;
    width: 1024px;
    margin-left: auto;
    margin-right: auto;
}
</style>
