---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
---

## 从售电项目发版的缓存问题上谈谈浏览器缓存

---

# 售电项目 v2.4 版本发现的问题

- 问题：<br>
  项目发版后，访问 yongdian.test.in.chinawyny.com，页面的资源始终是上个版本的内容。
  重定向到/home，再执行刷新，才显示当前版本。但是 url 上删除/home，访问根路径，页面资源又变回上个版本的资源了。如果不清除浏览器缓存，始终是该表现。 数字化能源系统（用电项目）同样有此问题
  <br>
  <br>

---

# demo 演练(初版操作)

---

# 从 network 面板看缓存

- Service Worker [✈️](https://squoosh.app)
- Memory Cache [✈️](https://zhuanlan.zhihu.com/p/54314093)
- Disk Cache
- Push Cache

<v-click>
<br />
<br/>
<div     class=" text-[#2B90B6] -z-1"
>如果开启了 Service Worker 首先会从 Service Worker 中拿<br><br>
如果新开一个以前打开过的页面缓存会从 Disk Cache 中拿（前提是命中强缓存）<br><br>
刷新当前页面时浏览器会根据当前运行环境内存来决定是从 Memory Cache 还是 从 Disk Cache 中拿</div>
</v-click>

---

# 浏览器缓存策略

> 强缓存

1. 不会向服务器发送请求，直接从缓存中读取资源
2. 状态码 200，size 显示 from disk cache 或 from memory cache
3. HTTP Header:

- Expires =未来的时间
- Cache-Control max-age=未来的时间

> 协商缓存

1. 会向服务器请求资源，对比资源是否改变.
2. 状态码 304，表示资源没有修改，返回缓存，更新缓存信息
3. HTTP Header:

- Cache-Control max-age=0,...[✈️](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control)
- Pragma no-cache[✈️](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Pragma)

<v-click>
<div>优先级: Pragma > Cache-Control > Expires
</div>
</v-click>

```

```

---

# tips

- nginx location / 根路径代理的基础配置

```
  root 资源的根路径
  index 访问根域名返回的 index.html 文件
  try_files 访问的资源不存在时返回的资源。
  单页面应用中，在/home路径下刷新，实质上是去服务器找根路径下，名为home的文件。
  如果不配置try_files，会报404，找不到资源。配置了刷新后，服务器返回目标资源
```

<br>

- 浏览器刷新

```
  // devtools open
  正常重新加载 = request Header 上增加 Cache-Control: max-age=0
  硬性重新加载 = request Header 上增加 Cache-Control: no-cache
  清空缓存并硬性重新加载 = request Header 上增加 Cache-Control: no-cache + 清除所有资源，包括通过js加载的资源（exp:路由加载的js）
```

---

# 浏览器缓存过程

<img class="img" src="/chrome-cache.png"  />

<v-click>
<img class="img1" src="/chrome-cache1.png"  />

</v-click>

<style>

  .img,.img1{
    position:absolute;
    height:80%;
    text-align:center;
  }
  .img1{
    z-index:100;
  }
</style>

---

# 新鲜度

https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching#heuristic_freshness_checking

<v-click>
<br/>
<br/>
<h4>freshnessLifetime = (Date - Last-Modified) / 10</h4>
</v-click>
<v-click>
<p>> freshnessLifetime 新鲜度，缓存是否有效</p> 
</v-click>
<v-click>
<p>> Date 响应资源的时间</p> 
</v-click>
<v-click>
<p>> Last-Modified 服务器更新资源的时间 </p>

</v-click>

---

# demo 演示 2

---

# 售电项目 v2.4 版本发现的问题

- 问题：<br>
  项目发版后，访问 yongdian.test.in.chinawyny.com，页面的资源始终是上个版本的内容。
  重定向到/home，再执行刷新，才显示当前版本。但是 url 上删除/home，访问根路径，页面资源又变回上个版本的资源了。如果不清除浏览器缓存，始终是该表现。 数字化能源系统（用电项目）同样有此问题
  <br>
  <br>

<div grid="~ cols-2" m="-t-2">
<img
      src="/shoudian_cache.png"
    />
<img
      src="/shoudian_refresh.png"
    />
</div>

---

# 设计原因

<br/>
为什么浏览器要这样设计默认的缓存算法？
<br/>
<br/>

<v-click>

<p>> Date 服务器响应资源的时间 - LastModify资源上次修改的时间的差值越大，说明服务器资源越久没有更新，说明这个文件越稳定，再次请求资源越应该使用缓存，过期时间也相对更久一点</p> 
</v-click>

---

<!-- ./components/Counter.vue -->
<Counter  />

---

# 解决方案

- nginx 配置不缓存 html 文件
- 通知客户清除浏览器缓存

---

# 清除缓存

1. 更多工具 -> 清除浏览数据
2. 清除应用缓存

---

<div class="w-60 relative mt-6">
  <div class="relative w-40 h-40">
    <img
      v-motion
      :initial="{ x: 800, y: -100, scale: 1.5, rotate: -50 }"
      :enter="final"
      class="absolute top-0 left-0 right-0 bottom-0"
      src="https://sli.dev/logo-square.png"
    />
    <img
      v-motion
      :initial="{ y: 500, x: -100, scale: 2 }"
      :enter="final"
      class="absolute top-0 left-0 right-0 bottom-0"
      src="https://sli.dev/logo-circle.png"
    />
    <img
      v-motion
      :initial="{ x: 600, y: 400, scale: 2, rotate: 100 }"
      :enter="final"
      class="absolute top-0 left-0 right-0 bottom-0"
      src="https://sli.dev/logo-triangle.png"
    />
  </div>

  <div
    class="text-5xl absolute top-14 left-40 text-[#2B90B6] -z-1"
    v-motion
    :initial="{ x: -80, opacity: 0}"
    :enter="{ x: 0, opacity: 1, transition: { delay: 2000, duration: 1000 } }">
    Thanks
  </div>
</div>

<!-- vue script setup scripts can be directly used in markdown, and will only affects current page -->
<script setup lang="ts">
const final = {
  x: 0,
  y: 0,
  rotate: 0,
  scale: 1,
  transition: {
    type: 'spring',
    damping: 10,
    stiffness: 20,
    mass: 2
  }
}
</script>

<div
  v-motion
  :initial="{ x:35, y: 40, opacity: 0}"
  :enter="{ y: 0, opacity: 1, transition: { delay: 3500 } }">

</div>
