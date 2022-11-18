# uniapp-issue-duplicated-regenerator

**问题描述**
我们在项目中，使用 yarn upgrade 例行升级依赖以后发现，最终打包尺寸增加了 30% 左右，超出了小程序限制无法上传。经过分析发现每一个用到 `async` 的 js 文件都被重复打包了一个 13kb 的 `regenerator-runtime`。最后定位到是因为 `@babel/plugin-transform-regenerator` 从 7.17.9 升级到了 7.18.6。

最后，我们发现在 `babel.config.js` 完全禁用掉 `@babel/plugin-transform-regenerator` 可以解决问题，并不会影响实际功能，似乎是因为小程序上传的时候会自己打包一份 `regenerator-runtime`。

**复现步骤**

1. 新建空白的 uniapp 项目 `vue create -p dcloudio/uni-preset-vue my-uniapp-project` 选择默认模版
2. 新建两个 js 文件

```js
// src/async-function.js
export default async function () {}

// src/normal-function.js
export default function () {}
```

3. 在首页引入

```js
// src/pages/index/index.vue
import asyncFunction from "../../async-function";
import normalFunction from "../../normal-function";
```

4. 添加 `webpack-bundle-analyzer` 插件方便查看打包大小

```
yarn add -D vue-cli-plugin-webpack-bundle-analyzer
```

4. 编译

```
yarn dev:mp-weixin
```

5. 查看打包尺寸

打开 <http://127.0.0.1:8888/>

**预期结果**

`src/async-function.js` 和 `src/normal-function.js` 的打包结果不应该差别太大。

**实际结果**

* `src/async-function.js` 打包尺寸 13.38KB
* `src/normal-function.js` 打包尺寸 109B

![image](https://user-images.githubusercontent.com/179978/202674948-067274ef-aae2-43ce-ab27-8d51e826b521.png)

**系统信息:**

* 发行平台: 微信小程序
* 操作系统 Mac
* HBuilderX版本 n/a
* uni-app版本 [如使用Vue-cli创建/运行项目，则提供`npm run info`的运行结果]
* 设备信息 n/a

```
uni-app v3.6.10
uni-app cli v2.0.1-alpha-36920221118002

Environment Info:

  System:
    OS: macOS 13.0
    CPU: (10) arm64 Apple M1 Pro
  Binaries:
    Node: 16.14.2 - ~/.nvm/versions/node/v16.14.2/bin/node
    Yarn: 1.22.18 - /opt/homebrew/bin/yarn
    npm: 8.5.0 - ~/.nvm/versions/node/v16.14.2/bin/npm
  Browsers:
    Chrome: 107.0.5304.110
    Firefox: 100.0
    Safari: 16.1
  npmPackages:
    @dcloudio/types: ^3.0.4 => 3.0.19
    @dcloudio/uni-app: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-app-plus: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-app-plus-nvue:  0.0.1
    @dcloudio/uni-app-plus-nvue-v8:  0.0.1
    @dcloudio/uni-automator: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-cli-i18n: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-cli-shared: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-h5: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-helper-json: * => 1.0.13
    @dcloudio/uni-i18n: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-migration: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-mp-360: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-mp-alipay: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-mp-baidu: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-mp-jd: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-mp-kuaishou: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-mp-lark: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-mp-qq: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-mp-toutiao: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-mp-vue: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-mp-weixin: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-mp-xhs: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-quickapp-native: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-quickapp-webview: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-stacktracey: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-stat: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/uni-template-compiler: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/vue-cli-plugin-hbuilderx: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/vue-cli-plugin-uni: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/vue-cli-plugin-uni-optimize: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/webpack-uni-mp-loader: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @dcloudio/webpack-uni-nvue-loader:  0.0.1
    @dcloudio/webpack-uni-pages-loader: ^2.0.1-alpha-36920221118002 => 2.0.1-alpha-36920221118002
    @hap-toolkit/dsl-vue:  0.6.13
    @vue/babel-helper-vue-jsx-merge-props:  1.4.0
    @vue/babel-helper-vue-transform-on:  1.0.2
    @vue/babel-plugin-jsx:  1.1.1
    @vue/babel-plugin-transform-vue-jsx:  1.4.0
    @vue/babel-preset-app:  4.5.19
    @vue/babel-preset-jsx:  1.4.0
    @vue/babel-sugar-composition-api-inject-h:  1.4.0
    @vue/babel-sugar-composition-api-render-instance:  1.4.0
    @vue/babel-sugar-functional-vue:  1.4.0
    @vue/babel-sugar-inject-h:  1.4.0
    @vue/babel-sugar-v-model:  1.4.0
    @vue/babel-sugar-v-on:  1.4.0
    @vue/cli-overlay:  4.5.19
    @vue/cli-plugin-babel: ~4.5.13 => 4.5.19
    @vue/cli-plugin-router:  4.5.19
    @vue/cli-plugin-vuex:  4.5.19
    @vue/cli-service: ~4.5.13 => 4.5.19
    @vue/cli-shared-utils:  4.5.19
    @vue/compiler-sfc:  2.7.14
    @vue/component-compiler-utils:  3.3.0 (3.3.0)
    @vue/composition-api:  1.7.0
    @vue/devtools-api:  6.0.0-beta.15
    @vue/preload-webpack-plugin:  1.1.2
    @vue/shared: ^3.0.0 => 3.2.45
    @vue/web-component-wrapper:  1.3.0
    mpvue-page-factory:  1.0.1
    mpvue-template-compiler:  1.0.13
    uni-h5-vue:  2.6.10
    uni-mp-vue:  2.6.10
    vue: ^2.6.11 => 2.7.14
    vue-cli-plugin-webpack-bundle-analyzer: ^4.0.0 => 4.0.0
    vue-hot-reload-api:  2.3.4
    vue-loader:  15.10.1 (16.8.3, 15.10.1)
    vue-router:  3.0.1
    vue-style-loader:  4.1.3 (4.1.3)
    vue-template-compiler: ^2.6.11 => 2.7.14 (2.7.14)
    vue-template-es2015-compiler:  1.9.1
    vue3:  1.0.0
    vuex: ^3.2.0 => 3.6.2 (3.6.2)
  npmGlobalPackages:
    @vue/cli: Not Found
```

**补充信息**
经过尝试，在 `babel.config.js` 完全禁用掉 `@babel/plugin-transform-regenerator` 可以解决，打包后任何文件都不再包含 `regenerator-runtime`。

```diff
const config = {
  presets: [
    [
      '@vue/app',
      {
        modules: webpack.version[0] > 4 ? 'auto' : 'commonjs',
        useBuiltIns: process.env.UNI_PLATFORM === 'h5' ? 'usage' : 'entry',
+        exclude: ['@babel/plugin-transform-regenerator'],
      }
    ]
  ],
  plugins
}

```

结果

* `src/async-function.js` 打包尺寸 988B
* `src/normal-function.js` 打包尺寸 109B

![image](https://user-images.githubusercontent.com/179978/202677052-da7e60c4-e88a-4204-923a-173a131d0b12.png)
