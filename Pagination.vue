<template>
    <div class="courseDetailBottom">
        <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-sizes="pageSizes"
            layout="total, sizes, prev, pager, next, jumper"
            :total="total">
        </el-pagination>
    </div>
</template>
<script>
export default {
    props:{
        currentPage,//当前页
        total,//总数
        optionSize//每页条数
    },
    data() {
        return {
            pageSizes:[10, 20, 30, 40, 50],
        }
    }  
}
</script>