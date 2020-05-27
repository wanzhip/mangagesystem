<template>
    <div>
        <el-button style="margin-left:1.5rem" type="primary" @click="goBack"> 返回</el-button>
    </div>
</template>
<script>
export default {
    methods:{
        goBack(){
            this.$router.go(-1);    
        }
    }
}
</script>