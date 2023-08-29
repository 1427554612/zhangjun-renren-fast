<template>
    <div>
        <el-tree :data="menus" :props="defaultProps" :expand-on-click-node="false" show-checkbox node-key = "catId" :default-expanded-keys="expandedKys"> 
            <span class="custom-tree-node" slot-scope="{ node, data }">
                <span>{{ node.label }}</span>
                <span>
                    <el-button v-if="node.level<=2" type="text" size="mini" @click="() => append(data)">添加</el-button>
                    <el-button v-if="node.childNodes.length==0" type="text" size="mini" @click="() => remove(node, data)">删除 </el-button>
                </span>
            </span>
        </el-tree>   
    </div>
</template>
<script>
export default {
    data(){
        return {
            menus:[],
            expandedKys:[],  // 默认展开节点
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
        },
        // 删除菜单节点
        remove(node,data){
            let ids =[data.catId]
            this.$confirm(`是否删除【${data.name}】菜单？`,"提示",{
                confirmButtonText:"确定",
                cancelButtonText:"取消",
                type:"warning"
            }).then(()=>{
                this.$http({
                    url: this.$http.adornUrl('/gulimallproduct/category/delete'),
                    method: 'post',
                    data: this.$http.adornData(ids, false)
                }).then(({data}) => {
                    this.$message({
                        type: "success",
                        message: '成功删除'
                    });
                    this.getCategoryTree()
                    this.expandedKys = [node.parent.data.catId]
                })
            }).catch(()=>{

            })
            
        },
        // 添加菜单节点
        append(node,data){

        }
    }
    
}
</script>
