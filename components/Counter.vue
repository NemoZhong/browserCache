<script lang="ts">
import { ref } from 'vue';
import { Typing } from './typing';
import caching from '../public/caching.png';

export default {
  name: 'Typing',
  setup() {
    function onClick() {
      setTimeout(() => {
        document.getElementById('img1').style.display = 'block';
      }, 10000);
      const bg = document.getElementById('bg_audio');
      bg.volume = 0.2;
      bg.play();

      document.getElementById('btn').style.display = 'none';
      var typing = new Typing({
        source: document.getElementById('source'),
        output: document.getElementById('output'),
        delay: 160,
        done: function () {
          console.log(this);
          console.log('done');
          bg.pause();
        },
        cb: () => {
          const audio = document.getElementById('audio');
          audio.volume = 0.3;
          audio.play();
        },
      });
      typing.start();
    }
    return { onClick };
  },
};
</script>

<template>
  <div class="container">
    <div id="source">
      2022年4月8日
      <br />景红同学悄悄咪咪地打开chrome访问了老版售电系统，浏览器发现服务器资源上次更新的时间是2021年11月<br />因此此次请求的资源缓存有效期为：(2022年4月-2021年11月)/10，约等于18天过期。
      <br /><br />2022年4月13日
      <br />新版售电系统发布，景红同学再次访问售电系统，由于缓存没有失效，因此看到的还是老版资源。
      <br />
      <br />售电项目访问根路径/会重定向到/home，在/home刷新，服务器通过try_files字段，
      返回index.html，并且这份index.html不会更新根路径/下的index.html，浏览器认为属于不同的资源。所以删除/home之后又变成老版本的内容了。
    </div>
    <button
      id="btn"
      p="2"
      font="mono"
      hover:bg="gray-400 opacity-20"
      @click="onClick"
    >
      总结
    </button>
    <div id="typingjs">
      <span id="output"></span>
      <span class="typing-cursor-white">|</span>
    </div>

    <div id="img1"></div>
    <audio id="audio"><source src="/biu.mp3" /></audio>
    <audio id="bg_audio"><source preload src="/seek.mp3" /></audio>
  </div>
</template>

<style scoped>
#img1 {
  display: none;
  position: absolute;
  top: 0;
  right: 0;
  width: 400px;
  height: 160px;
  border: 1px solid #fff;
  background: url('/cache-disk-demo.jpg') 0 -60px no-repeat;
  background-size: cover;
}
.container {
  position: relative;
  width: 100%;
  height: 100%;
  background: linear-gradient(rgba(0, 0, 0, 0.7), rgba(0, 0, 0, 0.7)),
    url('/case.jpeg');
  background-size: cover;
  background-color: rgba(0, 0, 0, 0.3);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-end;
}
#source {
  display: none;
}
#typingjs {
  width: 850px;
  height: 310px;
  border: 1px solid #08b439;
  color: #08b439;
  padding: 10px 20px;
  line-height: 1.5;
  word-spacing: 4px;
  margin-bottom: 20px;
  border-radius: 4px;
}
#btn {
  width: 100px;
  height: 40px;
  border-radius: 10px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: #666;
}
@keyframes blink {
  0% {
    opacity: 1;
  }
  50% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

@-webkit-keyframes blink {
  0% {
    opacity: 1;
  }
  50% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

.typing-cursor,
.typing-cursor-black,
.typing-cursor-white {
  opacity: 1;
  font-weight: bold;
  -webkit-animation: blink 0.7s infinite;
  animation: blink 0.7s infinite;
}

.typing-cursor,
.typing-cursor-black {
  color: #000;
}

.typing-cursor-white {
  color: #08b439;
}
</style>
