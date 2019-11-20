# vue-image-preview

## Environment

This plugin is run with Vue2.

## Platform

Not only mobile but also web are work perfectly.

## Usage

Import component in your project, then write another a little bit code like below the component will work.

### In script

```
import VueImagePreview from "vue-image-preview";
export default {
  components:{ VueImagePreview },
  ......
  data() {
    return {
      options: {
        effect: "cube",
        cube: {
          shadow: true,
          slideShadows: true,
          shadowOffset: 20,
          shadowScale: 0.94
        }
      }
    };
  }
......
}
```

### In template

```vue
<vue-image-preview :options="options">
  <ul>
    <li v-for="item in list" :key="item.id">
      <img :src="item.src" />
    </li>
  </ul>
</vue-image-preview>
```

## Option

Options object is swiper options, for more infomation you can see swiper officail document:  https://swiperjs.com/api/.

| attribute  | type    | defalut value | descrition                                                   |
| ---------- | ------- | ------------- | ------------------------------------------------------------ |
| options    | Object  | {}            | swiper options                                               |
| attrAsign  | Boolean | false         | asign img to preview, if asign is get true value, you need to give img tag a 'preview' attribute that you want to preview, then component will include img that just have 'preview' attribute |
| autoUpdate | Boolean | true          | update swiper way (manual operation or auto)                 |
| counter    | Boolean | true          | show counter or not                                          |

Notice:  swiper thumbs attribute in optoin is not allow, because vue-image-preview component doesn't include thumbs elements.

If attrAsign attribute get true value, you have to asign 'preview' attribute to all images you want to preview

```vue
<vue-image-preview :attr-asign="true" :options="options">
  <ul>
    <img :src="avatar.png" />
    <li v-for="item in list" :key="item.id">
      <img preview :src="item.src" />
    </li>
  </ul>
</vue-image-preview>
```

In this example, first image will not be included in previewer, but images that with preview attribute are included in previewer, and this scenes is common.

If autoUpdate get false value, you need to call updateImage() method by youself in parent component, because image previewer won't call  updateImage()  method any longer, it is rarely needed unless you need to update images list at time, or you want to call this function after async request to update images list. But in generally, you are not necessary give component auto-update="false" and call updateImage() by youself.

A complate example:

```vue
<template>
  <vue-image-preview :attr-asign="true" :auto-update="false" ref="preview" :options="options">
    <ul>
      <li v-for="item in list" :key="item.id">
        <img preview :src="item.src" />
      </li>
    </ul>
  </vue-image-preview>
</template>
<script>
import VueImagePreview from "vue-image-preview";
export default {
  components: { VueImagePreview },
  name: "HelloWorld",
  data() {
    return {
      list: [{
         id: 1,
         src: "./images/1.jpg"
      }],
      options: {
        effect: "cube",
        cube: {
          shadow: true,
          slideShadows: true,
          shadowOffset: 20,
          shadowScale: 0.94
        }
      }
    };
  },
  mounted() {
    const _this = this
    setTimeout(() => {
      _this.list = [
        ..._this.list,
        ...[
          {
            id: 2,
            src: "./images/2.jpg"
          },
          { 
            id: 3, 
            src: "./images/3.jpg" }
        ]
      ];
      _this.$refs["preview"].updateImage()
    }, 2000);
  }
};
</script>
```

In this example we call updateImage() by ourself, after _this.list data change, and we also can let image preview component call updateImage() method itself by default.

## Event

@open:  when image previewer is open  this event will be triggered

@close: when image previewer is close this event will be triggered

## 中文文档

http://yunkus.com/post/5dd3b4edaa3c5bc8