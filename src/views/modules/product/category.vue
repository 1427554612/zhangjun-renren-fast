<template>
    <div>
        <el-tree :data="menus" :props="defaultProps" :expand-on-click-node="false" show-checkbox node-key = "catId" :default-expanded-keys="expandedKys"> 
            <span class="custom-tree-node" slot-scope="{ node, data }">
                <span>{{ node.label }}</span>
                <span>
                    <el-button v-if="node.level<=2" type="text" size="mini" @click="() => append(data)">添加</el-button>
                    <el-button type="text" size="mini" @click="() => update(data)">修改 </el-button>
                    <el-button v-if="node.childNodes.length==0" type="text" size="mini" @click="() => remove(node, data)">删除 </el-button>
                </span>
            </span>
        </el-tree>
        <!-- 添加菜单对话框 -->
        <el-dialog title="分类" :visible.sync="dialogFormVisible">
            <el-form :model="category">
                <el-form-item label="分类名称">
                    <el-input v-model="category.name" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="分类icon">
                    <el-input v-model="category.icon" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="计量单位">
                    <el-input v-model="category.productUnit" autocomplete="off"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="addOrUpdateCategory">确 定</el-button>
            </div>
        </el-dialog>   
    </div>
</template>
<script>
export default {
    data(){
        return {
            menus:[],
            dialogType:"",
            category:{
                catId:null,
                name:"",
                parentCid:0,
                catLevel:0,
                sort:0,
                showStatus:1,
                productUnit:"",
                icon:"",
                productCount:0
            },
            dialogFormVisible:false,
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

        // 添加或者修改数据
        addOrUpdateCategory(){
            if(this.dialogType === "add"){
                this.addCategory()
            }else{
                console.log("执行修改。。。")
                console.log(this.category)
                this.updateCategory()
            }
        },
        // 打开添加分类弹框
        append(data){
            this.dialogType = "add"
            this.category.catLevel = data.catLevel *1 +1
            this.category.parentCid = data.catId
            this.category.name = ""
            this.category.productUnit = ""
            this.category.icon = ""
            this.showStatus = 1
            this.dialogFormVisible = true
        },
        
    
        
        //修改分类菜单
        update(data){
            this.dialogType = "update"
            this.category.name = data.name
            this.category.catId = data.catId
            // TODO:回显数据
            this.$http({
                url: this.$http.adornUrl(`/gulimallproduct/category/info/${data.catId}`),
                method: 'get'
                }).then(({data}) => {
                    console.log(data.category)
                    this.category.name = data.category.name
                    this.category.catId = data.category.catId
                    this.category.productUnit = data.category.productUnit
                    this.category.icon = data.category.icon
                    this.category.parentCid = data.category.parentCid
                }
            )
            this.dialogFormVisible = true
        },

        // 添加菜单节点
        addCategory(){
            console.log("添加数据")
            this.$http({
                    url: this.$http.adornUrl('/gulimallproduct/category/save'),
                    method: 'post',
                    data: this.$http.adornData(this.category, false)
                }).then(({data}) => {
                    this.$message({
                        type: "success",
                        message: '菜单添加成功'
                    });
                    this.dialogFormVisible=false
                    this.getCategoryTree()
                    this.expandedKys = [this.category.parentCid]
                })
        },

        // 修改菜单节点
        updateCategory(){
            // 结构category对象，是的对象属性变为多个变量
            var { catId,name,icon,productUnit } = this.category
            this.$http({
                    url: this.$http.adornUrl('/gulimallproduct/category/update'),
                    method: 'post',
                    data: this.$http.adornData({ catId,name,icon,productUnit }, false)
                }).then(({data}) => {
                    this.$message({
                        type: "success",
                        message: '菜单修改成功'
                    });
                    this.dialogFormVisible=false
                    this.getCategoryTree()
                    this.expandedKys = [this.category.parentCid]
                })
        }
    }
    
}
</script>
