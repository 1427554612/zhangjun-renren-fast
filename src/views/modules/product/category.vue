<template>
    <div>
        <el-tree
            :data="menus"
            :props="defaultProps"
        >
        </el-tree>   
    </div>
</template>
<script>
export default {
    data(){
        return {
            menus:[{
                name: '一级 1',
                children: [{
                        name: '二级 1-1',
                        children: [{
                        name: '三级 1-1-1'
                    }]
                }]}
            ],
            defaultProps:{
                children:"children",
                label:"name"
            }
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
            }).then(response=>{
                console.log(response.data)
                this.menus = response.data.data;
            })
        }
    }
    
}
</script>
