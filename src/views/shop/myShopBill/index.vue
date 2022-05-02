<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="用户名称" prop="username">
            <el-input v-model="form.username" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="账单标题" prop="billName">
            <el-input v-model="form.billName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="明细种类" prop="kind">
            <el-input v-model="form.kind" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="账单数目" prop="amount">
            <el-input v-model="form.amount" style="width: 370px;" />
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
        <el-table-column prop="id" label="账单id" />
        <el-table-column prop="username" label="用户名称" />
        <el-table-column prop="createdTime" label="创建时间" />
        <el-table-column prop="billName" label="账单标题" />
        <el-table-column prop="kind" label="明细种类" />
        <el-table-column prop="amount" label="账单数目" />
        <el-table-column v-if="checkPer(['admin','myShopBill:edit','myShopBill:del'])" label="操作" width="150px" align="center">
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
import crudMyShopBill from '@/api/myShopBill'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, username: null, createdTime: null, billName: null, kind: null, amount: null }
export default {
  name: 'MyShopBill',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '商城账单', url: 'api/myShopBill', idField: 'id', sort: 'id,desc', crudMethod: { ...crudMyShopBill }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'myShopBill:add'],
        edit: ['admin', 'myShopBill:edit'],
        del: ['admin', 'myShopBill:del']
      },
      rules: {
        username: [
          { required: true, message: '用户名称不能为空', trigger: 'blur' }
        ],
        billName: [
          { required: true, message: '账单标题不能为空', trigger: 'blur' }
        ],
        kind: [
          { required: true, message: '明细种类不能为空', trigger: 'blur' }
        ],
        amount: [
          { required: true, message: '账单数目不能为空', trigger: 'blur' }
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
