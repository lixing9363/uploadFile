## Vue-cli 使用图片时的引入

- 当图片文件放在public文件夹内

  > 放在public文件夹时,如果不使用导入方式引用,在run build时,不会分类到静态文件类

```html
<template>
    <img :src="big_logo" style="width: 100px;position: relative;top: -2px">
</template>
```

```html
<script>
    import big_logo from '../../public/big_logo.png'

    export default {
        name: "Name",
        data() {
            return {
                big_logo: big_logo
            };
        },
    };
</script>
```

- 当图片放在src/assert文件夹内

  > 直接引用,在run build时也会分类到静态文件类中

```vue
<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/testpng.png">
  </div>
</template>
```
