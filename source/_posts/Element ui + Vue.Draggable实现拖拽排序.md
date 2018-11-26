---
title: element ui + Vue.Draggable实现拖拽排序
date: 2018-03-28 00:01:56
categories: 
- 前端
tags:
- element-ui
- vue
---
最近在自己练习的一个采用前后分离项目中，前端使用的是element ui + vue来实现(本人从事于的是Java后端开发)，在菜单管理页面构想用拖拽的方式来对菜单进行排序操作，查阅资料后决定使用Vue.Draggable实现。<!--more-->
刚开始总是达不到想要的效果，最后回过头去对照文档，发现原来还是比较简单的。先上实现的效果图，通过拖拽菜单的顺序可以对菜单进行排序，并保存到后台
![](http://pic.yhen.me/15221672181063.jpg)

以下是实现的前端代码片段

```html
<el-card class="box-card">
  <div slot="header" class="clearfix">
    <span>二级菜单</span>
    <el-button style="float: right; padding: 3px 0" type="text" @click="handleCreateMenu">新建菜单</el-button>
  </div>
  <el-collapse accordion @change="fetchFunctionList">
    <draggable :list="subMenuList" :move="moveMenu" @end="moveEnd">
      <el-collapse-item v-for="menu in subMenuList" :name="menu.id" :key="menu.id">
        ...
      </el-collapse-item>
    </draggable>
  </el-collapse>
</el-card>
```

```javascript
export default {
    data() {
      return {
        subMenuList: [],
        loading: false,
        menu: {
          id: undefined,
          version: 0,
          name: '',
          url: '',
          parentId: 0,
          sort: 1
        },
        sortMenuList: []
      }
    },
    methods: {
      moveMenu({draggedContext, relatedContext}){
        this.sortMenuList = relatedContext.list
        return true
      },
      moveEnd(evt){
        this.sortMenuList.forEach(function(item, index){
          item.sort = index + 1
        })
        this.loading = true
        sortMenu(this.sortMenuList).then((response) => {
          this.loading = false
          if(response.success){
            this.$message.success(response.message)
            this.fetchMenuList(this.menu.parentId)
          }else {
            this.$message.error(response.message)
          }
        })
      }
    }
  }
```




