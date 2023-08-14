<template>
    <div>
        <el-tree
            :data="data"
            show-checkbox
            node-key="id"
            :default-expanded-keys="[2, 3]"
            :default-checked-keys="[5]"
            :props="defaultProps">
        </el-tree>   
    </div>
</template>
<script>
export default {
    data(){
        return {
            data:[]
        }
    },
    created(){
        this.getCategoryTree();
    },
    methods:{
        // 获取分类树
        getCategoryTree(){
            this.$http({
                url: this.$http.adornUrl('/gulimallproduct/category/list/tree'),
                method: 'get'
            })
        }
    }
    
}
</script>
