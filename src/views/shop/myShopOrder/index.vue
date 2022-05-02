<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">订单号</label>
        <el-input v-model="query.orderId" clearable placeholder="订单号" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="订单号">
            <el-input v-model="form.orderId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="支付价格">
            <el-input v-model="form.payment" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="支付状态">
            <el-input v-model="form.status" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="用户名称">
            <el-input v-model="form.username" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单描述">
            <el-input v-model="form.descrpition" style="width: 370px;" />
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
        <el-table-column prop="id" label=" 订单id" />
        <el-table-column prop="orderId" label="订单号" />
        <el-table-column prop="payment" label="支付价格" />
        <el-table-column prop="status" label="支付状态" />
        <el-table-column prop="username" label="用户名称" />
        <el-table-column prop="descrpition" label="订单描述" />
        <el-table-column prop="createdAt" label="创建时间" />
        <el-table-column prop="modifiedAt" label="修改时间" />
        <el-table-column v-if="checkPer(['admin','myShopOrder:edit','myShopOrder:del'])" label="操作" width="150px" align="center">
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
import crudMyShopOrder from '@/api/myShopOrder'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, orderId: null, payment: null, status: null, username: null, descrpition: null, createdAt: null, modifiedAt: null }
export default {
  name: 'MyShopOrder',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '商城订单', url: 'api/myShopOrder', idField: 'id', sort: 'id,desc', crudMethod: { ...crudMyShopOrder }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'myShopOrder:add'],
        edit: ['admin', 'myShopOrder:edit'],
        del: ['admin', 'myShopOrder:del']
      },
      rules: {
        id: [
          { required: true, message: ' 订单id不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'orderId', display_name: '订单号' }
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
