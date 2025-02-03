### 搭建

- 启动

  ```
  cd 项目dir
  npm install 或 npm i (安装js依赖)
  npm run dev
  ```

### Vue项目开发

- .vue结尾的文件就是vue页面, 我们也把它称为vue组件

- vue组件一般是三部分: 

  - <template>: html要展示的内容

  - <script>: javascript代码

  - <style>: css样式

### 引入element-plus组件

- 安装element-plus

  --save 是将安装包信息写入package.json的dependencies中, 在dependencies中, 项目打包会依赖到该模块

  --save-dev 是将安装包信息写入devDepencencies下, 表示项目开发时需要依赖该模块, 打包发布则不需要

  ```
  npm install element-plus --save
  ```

  