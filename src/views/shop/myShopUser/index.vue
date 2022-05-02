<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">会员昵称</label>
        <el-input v-model="query.username" clearable placeholder="会员昵称" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">会员电话</label>
        <el-input v-model="query.telephone" clearable placeholder="会员电话" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="会员昵称">
            <el-input v-model="form.username" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="会员密码">
            <el-input v-model="form.password" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="会员电话">
            <el-input v-model="form.telephone" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="会员存款">
            <el-input v-model="form.deposit" style="width: 370px;" />
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
        <el-table-column prop="username" label="会员昵称" />
        <el-table-column prop="password" label="会员密码" />
        <el-table-column prop="telephone" label="会员电话" />
        <el-table-column prop="deposit" label="会员存款" />
        <el-table-column prop="createdAt" label="创建时间" />
        <el-table-column v-if="checkPer(['admin','myShopUser:edit','myShopUser:del'])" label="操作" width="150px" align="center">
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
import crudMyShopUser from '@/api/myShopUser'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, username: null, password: null, telephone: null, deposit: null, createdAt: null }
export default {
  name: 'MyShopUser',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '商城会员', url: 'api/myShopUser', idField: 'id', sort: 'id,desc', crudMethod: { ...crudMyShopUser }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'myShopUser:add'],
        edit: ['admin', 'myShopUser:edit'],
        del: ['admin', 'myShopUser:del']
      },
      rules: {
        id: [
          { required: true, message: '会员id不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'username', display_name: '会员昵称' },
        { key: 'telephone', display_name: '会员电话' }
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
