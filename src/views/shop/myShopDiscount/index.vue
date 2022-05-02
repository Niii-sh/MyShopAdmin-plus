<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="优惠名称" prop="discountName">
            <el-input v-model="form.discountName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="优惠类型" prop="discountType">
            <el-input v-model="form.discountType" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="发布数量 " prop="discountAmount">
            <el-input v-model="form.discountAmount" style="width: 370px;" />
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
        <el-table-column prop="id" label="优惠券id" />
        <el-table-column prop="discountName" label="优惠名称" />
        <el-table-column prop="discountType" label="优惠类型" />
        <el-table-column prop="createdTime" label="发布时间" />
        <el-table-column prop="discountAmount" label="发布数量 " />
        <el-table-column v-if="checkPer(['admin','myShopDiscount:edit','myShopDiscount:del'])" label="操作" width="150px" align="center">
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
import crudMyShopDiscount from '@/api/myShopDiscount'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, discountName: null, discountType: null, createdTime: null, discountAmount: null }
export default {
  name: 'MyShopDiscount',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '商城优惠券', url: 'api/myShopDiscount', idField: 'id', sort: 'id,desc', crudMethod: { ...crudMyShopDiscount }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'myShopDiscount:add'],
        edit: ['admin', 'myShopDiscount:edit'],
        del: ['admin', 'myShopDiscount:del']
      },
      rules: {
        id: [
          { required: true, message: '优惠券id不能为空', trigger: 'blur' }
        ],
        discountName: [
          { required: true, message: '优惠名称不能为空', trigger: 'blur' }
        ],
        discountType: [
          { required: true, message: '优惠类型不能为空', trigger: 'blur' }
        ],
        discountAmount: [
          { required: true, message: '发布数量 不能为空', trigger: 'blur' }
        ]
      }    }
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
