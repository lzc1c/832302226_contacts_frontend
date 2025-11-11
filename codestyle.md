# 前端代码规范

> 基于 Vue.js 官方风格指南和 Element Plus 最佳实践

来源：

- Vue.js 官方风格指南: https://v3.vuejs.org/style-guide/
- Element Plus 组件规范
- Airbnb JavaScript 风格指南

## 目录结构规范

- 组件文件使用 PascalCase (例如：ContactsView.vue)
- 工具函数使用 camelCase
- 常量使用 UPPER_SNAKE_CASE

## Vue 组件规范

- 使用 Composition API
- 组件名为多单词，避免与 HTML 元素冲突
- Prop 定义尽量详细，包含类型和默认值
- 使用 scoped CSS

## JavaScript 规范

- 使用 ES6+ 语法
- 优先使用 const 和 let
- 使用箭头函数
- 模板字符串替代字符串拼接

## CSS 规范

- 使用 scoped 样式
- 类名使用 kebab-case
- 避免使用 !important
