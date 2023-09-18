中文网

https://www.sass.hk/

定义变量

```scss
$namespace: 'xm' !default;
$block-sel: '-' !default;
$elem-sel: '__' !default;
$mod-sel: '--' !default;

@mixin bfc {
  height: 100%;
  overflow: hidden;
}

// block

@mixin b($block) {
  $B: #{$namespace + $block-sel + $block};
  .#{$B} {
    @content;
  }
}

@mixin e($el) {
  $selector: &;
  @at-root {
    #{$selector + $elem-sel + $el} {
      @content;
    }
  }
}

@mixin m($m) {
  $selector: &;
  @at-root {
    #{$selector + $mod-sel + $m} {
      @content;
    }
  }
}

```

vite.config.ts

```ts
export default defineConfig({
  plugins: [vue()],
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@import "./src/bem.scss";`
      }
    }
  }
})

```

使用方法

```scss
@include b(test) {
  color: red;

  @include e(inner) {
    color: blue;
  }

  @include m(success) {
    color: green;
  }
}

#app {
  @include bfc;
}
```

