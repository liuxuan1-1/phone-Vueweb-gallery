## 效果

![这里写图片描述](http://img.blog.csdn.net/20171130225146772?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY19raXRl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

<br>

![这里写图片描述](http://img.blog.csdn.net/20171130225205073?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY19raXRl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

由于图片大小限制, 画质和帧数较差, 图片那些看起来比较可爱, 也就没有计较比例问题~

## 思路

整体图片flex布局, 结合Vue和移动touch事件实现, 具体细节代码上有注释, 想学习的朋友可以看一看.

## 代码


```html
//index.html
  <div :class="classObject" :style="styleObject" id="gallery" @click="preview" @touchstart="moveStart" @touchmove="moveing" @touchend="moveEnd" ref="gallery">
    <photo v-for="img in imgs" :key="img.alt" :img="img" ></photo>
  </div>
```

```javascript
//index.js
Vue.component('photo', {
  template: `
        <div class="item">
          <img :src="img.src" :alt="img.alt" :data-index="img.alt">
        </div>
      `,
  props: ['img'],
  data: function () {
    return {

    }
  }
})


let gallery = new Vue({
  el: '#gallery',
  data: {
    imgs: [{
        src: './images/1.gif',
        alt: '1',
      }, {
        src: './images/2.gif',
        alt: '2',
      }, {
        src: './images/3.gif',
        alt: '3',
      }, {
        src: './images/4.gif',
        alt: '4',
      }, {
        src: './images/5.gif',
        alt: '5',
      }, {
        src: './images/6.gif',
        alt: '6',
      }, {
        src: './images/7.gif',
        alt: '7',
      }, {
        src: './images/8.gif',
        alt: '8',
      }, {
        src: './images/9.gif',
        alt: '9',
      }],
      classObject: {
        gallery: true,
        preview: false,
        animation: false,
      },
      styleObject: {
        width: '100vw',
        transform: '',
      },
      startOffsetX: '',
      isTouchStart: false,
      screenWidth: 0,//获取当前页面尺寸
      index: 0,
  },
  methods: {
    preview: function (event) {
      if (!this.classObject.preview) {//查看状态
        this.classObject.preview = true;
        this.styleObject.width = `${this.imgs.length * 100}vw`;
        this.index = event.target.dataset.index-1;
        this.moveBy();
      } else {//预览状态
        this.classObject.preview = false;
        this.styleObject.width = `100vw`;
        this.styleObject.transform = `translate(0, 0)`;
      }
    },
    moveStart: function (event) {
      //触摸开始时, 记住当前手指的位置
      this.startOffsetX = event.changedTouches[0].pageX;
      //手指滑动的时候, 禁止动画
      this.classObject.animation = false;

    },
    moveing: function (event) {
      this.isTouchStart = true;
      //计算手指的水平移动量
      let dx = event.changedTouches[0].pageX - this.startOffsetX;
      //调用move方法, 设置gallery元素的transform, 移动图片
      this.move(dx);
    },
    moveEnd: function (event) {
      if (this.isTouchStart) {
        //移动图片的时候, 需要动画, 动画采用css3的transition实现
        this.classObject.animation = true;
        let dx = event.changedTouches[0].pageX - this.startOffsetX;
        //console.log(event);
        if (Math.abs(dx) > this.screenWidth / 2) {
          //处理临界值
          if (this.index == 0) {//第一张图片
            if (dx < 0) {//右滑
                this.index += 1;
            }
          } else if (this.index > 0 && this.index < this.imgs.length-1) {//在中间部分
            if (dx < 0) {
              this.index += 1;
            } else {
              this.index -= 1;
            }
          } else if (this.index == this.imgs.length-1){//在最后
            if (dx > 0) {
              this.index -= 1;
            }
          }
        }
        //不超过临界值不处理
        this.moveBy();
      }
      this.isTouchStart = false;
    },
    move: function (dx) {//移动动画
      let offset = -(this.index * this.screenWidth) + dx;
      this.styleObject.transform = `translate(${offset}px, 0)`;
    },
    moveBy: function () {
      this.styleObject.transform = `translate(${-this.index * 100}vw, 0)`;
      console.log(this.index)
    }
  },
  mounted: function () {
    this.screenWidth = this.$refs.gallery.clientWidth;
  }
})
```