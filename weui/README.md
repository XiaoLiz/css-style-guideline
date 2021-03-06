## WeUI 介绍

#### 本次讲解weui重要点：


##### 1、weui 结构简介；
 
##### 2、项目实例分析；
 
 

### 目录结构
![](./weui_demo/image/WechatIMG237.jpeg)


### Config gulpfile 	


#### gulp实现原理（基于node文件流实现）

1、文件输入 → Gulp 插件处理 → 文件输出

![](./weui_demo/image/pipe.jpeg)


`src <stream.Readable> 输出到目标可写流（writable）的源流（source stream）`

`在可读流（readable stream）上调用 stream.pipe() 方法，并在目标流向 (destinations) 中添加当前可写流 ( writable ) 时，将会在可写流上触发 'pipe' 事件。`

```js

const writer = getWritableStreamSomehow();  //可写流
const reader = getReadableStreamSomehow();  //可读流
writer.on('pipe', (src) => {
  console.error('something is piping into the writer');
  assert.equal(src, reader);
});
reader.pipe(writer);

```


#### 相关工具

`yargs: 命令行解析器，方便管理自定义的多个任务;`

`sourcemaps: 就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，所对应的转换前的位置;`

`postcss-discard-comments: 清除编译后css中的注释;`
 
`gulp-header: 给文本文件头部追加内容;`

`gulp-cssnano: 优化范围从压缩颜色和删除注释到丢弃覆盖的规则，归一化unicode范围描述符，甚至调整渐变参数以获得较小的输出值！ 另外，在转换的过程中，我们添加了Browserslist以提供不同的输出;`



```js

// using data from package.json 
gulp.task('style', function() {
    var version = [
        '/*!',
        ' * QUI v<%= pkg.version %> (<%= pkg.author %>)',
        ' * Copyright <%= new Date().getFullYear() %> ',
        ' * Licensed under the <%= pkg.license %> license',
        ' */',
        ''
    ].join('\n');

    return gulp.src('./style/*.less')
        .pipe(less())
        .pipe(postcss([autoprefixer(['iOS >= 7', 'Android >= 4.1']), comments()]))
        .pipe(header(version, { pkg: pkg }))
        .pipe(cssnano({
            zindex: false
         }))
        .pipe(gulp.dest('./dist/style'));
});
```

 -[WeUi-Demo](./weui_demo)


#### 组件栗子分析（weui-check 单选框 或者复选为例）

##### 1、WeUI组件命名借鉴 BEM（block-element-modifier）规范，形成WeUI独特的风格；

##### 2、前缀-组件名 && 前缀-组件名__修饰名

##### 3、一个组件或者dom类名称最多不超过三个；


```html
<div class="weui-cells weui-cells_radio">
    <label class="weui-cell weui-check__label" for="x11">
        <div class="weui-cell__bd"></div>
        <div class="weui-cell__ft">
            <input type="radio" class="weui-check" name="radio1" id="x11" checked="checked">
            <span class="weui-icon-checked"></span>
        </div>
    </label>

    <label class="weui-cell weui-check__label" for="x12">
        <div class="weui-cell__bd"></div>
        <div class="weui-cell__ft">
            <input type="radio" class="weui-check" name="radio1" id="x12">
            <span class="weui-icon-checked"></span>
        </div>
    </label>
</div>

```

##### 3、 css技巧

label 与 input 关联绑定:

优点：`不需要额外js控制页面交互效果，减少与js耦合`

```html

<div class="weui-cells__title">复选列表项</div>
<div class="weui-cells weui-cells_checkbox">
    <label class="weui-cell weui-check__label" for="s11">
        <div class="weui-cell__hd">
            <input type="checkbox" class="weui-check" name="checkbox1" id="s11" checked="checked"/>
            <i class="weui-icon-checked"></i>
        </div>
        <div class="weui-cell__bd">
            <p>standard is dealt for u.</p>
        </div>
    </label>
</div>

```

less

```less
.weui-check {
    // radio
    .weui-cells_checkbox & {
        &:checked {
            & + .weui-icon-checked {
                &:before {
                    display: block;
                    content: '\EA08';
                    color: #09BB07;
                    font-size: 16px;
                }
            }
        }
    }
}
```

编译后的css
```css
.weui-cells_checkbox .weui-check:checked + .weui-icon-checked:before {
    display:block;
    content:'\EA08';
    color:#09BB07;
    font-size:16px;
} 
```


##### 4、巧用相邻兄弟选择器， 
###### 优点: 减少类名称，保证保证 dom 简洁


```html
<div class="weui-cells__title">复选列表项</div>
<div class="weui-cells weui-cells_radio"></div>
```	

```	less
.weui-cells__title{
    margin-top: .77em;
    
    & + .weui-cells {
        margin-top: 0;
    }
}
```

编译后

```css

.weui-cells__title{
    margin-top: .77em;
 }
    
.weui-cells__title + .weui-cells {
        margin-top: 0;
    }
}
```


#### 知识拓展

- [PostCSS 简介](../css/postcss/README.md)


#### 参考资料

- [Web 前端规范文档](http://alloyteam.github.io/CodeGuide/) -腾讯AlloyTeam
- [yargs-API](https://github.com/yargs/yargs/blob/master/docs/api.md)-Github







