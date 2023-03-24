在 nvue 中，可以使用 flex 布局的 justify-content 和 align-items 属性来控制子元素的对齐方式，其中 justify-content 控制子元素在主轴上的对齐方式，而 align-items 控制子元素在侧轴上的对齐方式。

如果你想要在聊天气泡中控制气泡向左或者向右，可以根据聊天气泡所在的容器的方向，使用不同的 justify-content 属性值来实现。

例如，如果容器的方向为水平方向，向左对齐可以使用 justify-content: flex-start，向右对齐可以使用 justify-content: flex-end。

```js
<template>
  <view class="container" :style="{flexDirection: direction}">
    <view class="bubble" :style="{justifyContent: align}">
      <text>聊天气泡内容</text>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        direction: 'row', // 容器方向
        align: 'flex-start', // 对齐方式，初始为向左
      };
    },
    methods: {
      toggleAlign() { // 切换对齐方式
        this.align = this.align === 'flex-start' ? 'flex-end' : 'flex-start';
      },
    },
  };
</script>

<style>
  .container {
    height: 200px;
  }
  .bubble {
    width: 100px;
    height: 50px;
    background-color: #eee;
    border-radius: 5px;
    padding: 10px;
  }
</style>
```
