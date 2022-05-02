<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">商品id</label>
        <el-input v-model="query.id" clearable placeholder="商品id" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">商品名称</label>
        <el-input v-model="query.name" clearable placeholder="商品名称" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">分类名称</label>
        <el-input v-model="query.category" clearable placeholder="分类名称" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="商品名称">
            <el-input v-model="form.name" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="分类名称">
            <el-input v-model="form.category" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商品价格">
            <el-input v-model="form.price" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商品库存">
            <el-input v-model="form.inventory" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商品描述">
            <el-input v-model="form.description" style="width: 370px;" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.status.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
        <el-table-column prop="id" label="商品id" />
        <el-table-column prop="name" label="商品名称" />
        <el-table-column prop="category" label="分类名称" />
        <el-table-column prop="price" label="商品价格" />
        <el-table-column prop="inventory" label="商品库存" />
        <el-table-column prop="createdAt" label="创建时间" />
        <el-table-column prop="modifiedAt" label="修改时间" />
        <el-table-column prop="description" label="商品描述" />
        <el-table-column v-if="checkPer(['admin','myShopProduct:edit','myShopProduct:del'])" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudMyShopProduct from '@/api/myShopProduct'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, name: null, category: null, price: null, inventory: null, createdAt: null, modifiedAt: null, description: null }
export default {
  name: 'MyShopProduct',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '商城商品', url: 'api/myShopProduct', idField: 'id', sort: 'id,desc', crudMethod: { ...crudMyShopProduct }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'myShopProduct:add'],
        edit: ['admin', 'myShopProduct:edit'],
        del: ['admin', 'myShopProduct:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'id', display_name: '商品id' },
        { key: 'name', display_name: '商品名称' },
        { key: 'category', display_name: '分类名称' }
      ]
    }
  },
  methods: {
    // 钩子：在获取表格数据之前执行，false 则代表不获取数据
    [CRUD.HOOK.beforeRefresh]() {
      return true
    }
  }
}
</script>

<style scoped>

</style>
