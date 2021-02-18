## Vue-cli 使用图片时的引入

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

