# Box Hunter

---

![demo](/pics/image.png)

---

## 游戏规则介绍
每一方有大中小各两个棋子
每一回合每一方都可以在棋盘上放置一颗自己的棋子
如果棋盘上已经有棋子，则可以将自己的棋子放在比自己小的棋子上
- 如果比自己大，则将对方的棋子吃掉
- 如果比自己小，则不能放置

当任何一行或者一列是相同颜色时
该颜色的一方获胜

## 定义URL前缀
你可以在`vite.config.js`文件的第8行修改URL前缀，如果您不需要任何前缀，请直接删除改文件的第8行

## Recommended IDE Setup
[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur) + [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin).

## Customize configuration

See [Vite Configuration Reference](https://vitejs.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```
