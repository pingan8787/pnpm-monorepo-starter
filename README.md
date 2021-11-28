# pnpm-monorepo-starter

## pnpm 构建 Monorepo 介绍

Monorepo 可以将原本一个项目的多个模块拆分成多个子包（packages），每个子包之间互相引用，也可以单独发布，极大解决多个项目直接代码无法复用的问题。

使用 [pnpm](https://pnpm.io/) 构建 Monorepo 项目，可以把多个项目的代码整理到一个 git 仓库维护，多个项目中有共享的代码也可以单独分包进行维护。

整个项目所有的包都在 packages 目录下维护，每个包都有自己的 `package.json` 配置。

## 与 Lerna 方案比较
[Lerna](https://lerna.js.org/)

## 一、pnpm-monorepo-starter 使用

### 1. 添加全局通用的依赖
在项目根目录执行命令：
```bash
pnpm install react react-dom -w
```

其中：`-w` 表示当前依赖安装在项目根目录，会被安装在 `<root>/node_modules` 下。
如果需要把这个依赖安装到所有 packages 中，可以使用 `-r` 替换 `-w`。

### 2. 创建新包
接下来以创建 vue2 项目为例。

在 `packages` 目录下，通过 `vue create vue` 命令一个新的 `vue2` 项目。

然后修改 `vue2` 项目中的 `package.json` 的 `name` 属性的值：

```json
{
    "name": "@pnpm-monorepo-starter/vue",
    /* ... */
}
```

这里的包名 `"@pnpm-monorepo-starter/vue"` 是接下来其他包引用或者安装依赖或者发包需要用到。

### 3. 为子包添加依赖
当我们需要为指定子包添加依赖，可以用下面命令：
```bash
pnpm i dayjs -r --filter @pnpm-monorepo-starter/vue
```
其中：`-filter` 的值表示要安装到 `@pnpm-monorepo-starter/vue` 包中。

### 4. 创建通用包
我们经常需要将通用业务（如 utils）抽取出来复用，我们可以在 `packages` 目录下创建 `utils` 目录和 `index.ts` 入口文件。

初始化 git 配置：
```bash
npm init
```

然后修改 `package.json` 中 `name` 字段:
```json
{
    "name": "@pnpm-monorepo-starter/utils",
    /* ... */
}
```

并为 `index.ts` 简单添加一点代码：
```ts
// packages/utils/index.ts
export const getUA = () => window.navigator.userAgent;
```

然后可以在前面创建 Vue2 项目中引入：
```vue
<script>
import HelloWorld from './components/HelloWorld.vue'
import { getUA } from '@pnpm-monorepo-starter/utils' // 引入依赖
export default {
  name: 'App',
  components: {
    HelloWorld
  },
  mounted(){
    console.log('[getUA]', getUA())
  }
}
</script>
```

### 5. 增加全局命令
通过 pnpm 可以实现全局启动指定包里面的命令，简单看下怎么配置，这边以前面创建的 Vue2 项目为例。

只需要在根目录上的 `package.json` 的 `scripts` 中配置：

```json
{
    "scripts": {
        "dev:vue": "pnpm serve --filter \"@pnpm-monorepo-starter/vue\"",
        "dev:vue3": "pnpm dev --filter \"@pnpm-monorepo-starter/vue3\""
    },
    /* ... */
}
```

其中 `dev:vue` 命令的内容包括：
- `pnpm serve` 为执行该包里面的命令，对应该子包中 `package.json` 中 `scripts` 的命令。
- `--filter \"@pnpm-monorepo-starter/vue\"` 表示只对指定的子包有效。

## 二、拓展内容
### 1. 项目增加 ESLint 配置
详细可以按照 [官方文档](https://eslint.org/docs/user-guide/getting-started) 配置。
```bash
// 按照 ESLint 依赖
pnpm install eslint --save-dev

// 初始化 ESLint 配置文件
npx eslint --init
```

## 三、拓展阅读
1. [使用 pnpm 构建 Monorepo 项目](https://zhuanlan.zhihu.com/p/373935751)
2. [为什么使用pnpm可以光速建立好用的monorepo（比yarn/lerna效率高）](https://blog.csdn.net/qq_21567385/article/details/118590143)