<template>
    <div class="">
        <el-scrollbar wrap-class="scrollbar-wrapper">
            <el-menu
                :default-active="this.$route.path" 
                router
                mode="vertical"
                background-color="#20222a"
                text-color="#fff"
                active-text-color="#009688">
                <template v-for="(item,index) in menuList">
                <template v-if="item.children.length == 0">
                        <el-menu-item :index="item.url" :key="index">
                        <i :class="item.icon"></i>
                        {{item.name}}
                        </el-menu-item>
                </template>
                <el-submenu v-else :index="item.name" :key="index">
                <template slot="title" >
                    <i :class="item.icon"></i>
                    {{item.name}}
                </template>
                <el-menu-item  v-for="child in item.children" :key="child.id" :index="child.url">
                    <i :class="child.icon"></i>
                    {{child.name}}
                </el-menu-item>
                </el-submenu>
                </template>
            </el-menu>
        </el-scrollbar>
    </div>
</template>
<script>
export default {
  name:'NavMenu',
  props:{
    menuList: {
      type: Array,
      required: true
    }
  }
/*
    [
        {
            "id": 1,
            "name": "学校管理",
            "url": "",
            "ismenu": 1,
            "level": 1,
            "pid": 0,
            "sort": 0,
            "icon": "el-icon-s-custom",
            "children": [
                {
                "id": 101,
                "name": "学校列表",
                "url": "/mangageschool",
                "ismenu": 1,
                "level": 2,
                "pid": 2,
                "sort": 0,
                "icon": "el-icon-school",
                "children": []
                }
            ]
        },

    ]
*/ 
}
</script>
<style lang="scss" scoped>
.el-menu{
    border: none;
}
</style>