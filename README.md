# vue-j-scroll

> A Vue.js project

## 一个循环滚动列表插件

该插件为 vue-seamless-scroll 的升级版 ，可以自动滚动也可以手动滚动。dom 中的事件也保存了下来。
推荐数据量小于 1000，以保证插件的性能。

```bash
npm install @david-j/vue-j-scroll --save-dev 来安装


在项目中使用
import VueScroll from '@david-j/vue-j-scroll';
Vue.use(VueScroll);
```

```bash

示例：

    <vue-j-scroll
      class="list-style"
      :data="youData"
      :steep="1"
      scrollDirection="bottom"
      :isRoller="true"
      :rollerScrollDistance="50"
    >
        <div
          v-for="(item, index) in newData"
          :key="'t' + index"
          class="list-item"
        >
          <p>{{ item + "rrrrssserrdd" }}</p>
        </div>
    </vue-j-scroll>

```

| 属性   | 属性名称 | 类型   | 可选值 |
| ------ | -------- | ------ | ------ |
| steep  | 滚动的速率   | number | 为正数即可 |
| scrollDirection | 滚动的方向   | string | top ,bottom,left,right |
| isRoller | 是否可以使用滚轮滚动   | boolean |true,false |
| rollerScrollDistance | 滚轮滚动的速率   | number |（isRoller 必须为 true）为正数即可  |
| data | 接收异步数据   | array |同步任务可不传 |

<font color=#ff0000>注：如没有显示亲为嵌入的标签设置字体大小</font>
<br>
<font color=#ff0000>注： 当scrollDirection 为top或bottom时，一定要为 vue-j-scroll 组件设置高度。 当scrollDirection 为left或right时，一定要为 vue-j-scroll 组件设置宽度。并为嵌入vue-j-scroll中的标签设置样式为display:inline-block; 示例样式名为list-item可以更改为其他类名。</font>

<font color=#ff0000>注：  当scrollDirection 为left或right时,是基于行内元素的“white-space: nowrap;” 来控制强制不换行的。有可能会影响其内部的文字排列。可以在list-item 层添加  white-space: normal; 来处理给问题。并为其添加固定宽度，以保证文字可以正常换行及插件的正确计算与显示。如果没有为其添加固定宽度，会造成插件获取父容器层的宽度值错误，导致显示混乱</font>
