<template>
  <el-data-table v-bind="tableConfig"></el-data-table>
</template>
<script>
import {formatDate} from '@/const/filter'
import {component} from '@/const/api'

export default {
  name: 'DataTable',
  data() {
    return {
      tableConfig: {
        url: component,
        /**
         * set status column style based on status
         */
        tableAttrs: {
          'cell-class-name': cell =>
            cell.row.status && cell.column.property === 'status'
              ? 'comp--active'
              : ''
        },
        columns: [
          {
            type: 'selection'
          },
          {
            prop: 'name',
            label: '组件名称'
          },
          {
            prop: 'category',
            label: '分类'
          },
          {
            prop: 'version',
            label: '版本'
          },
          {
            prop: 'lang',
            label: '开发语言'
          },
          {
            prop: 'updateTime',
            label: '最后更新时间',
            formatter: row => formatDate(row.updateTime * 1000, 'YYYY-MM-DD')
          },
          {
            prop: 'status',
            label: '状态',
            formatter: row => (row.status ? '上架' : '下架')
          }
        ],
        /**
         * hide default edit button
         */
        hasEdit: false,
        /**
         * DIY buttons
         */
        extraButtons: [
          {
            type: 'primary',
            text: '查看',
            atClick: this.handleView
          },
          {
            type: '',
            text: '编辑',
            atClick: this.handleEdit
          },
          {
            type: '',
            text: '下架',
            show: row => row.status,
            atClick: this.handleToggleStatus
          },
          {
            type: '',
            text: '上架',
            show: row => !row.status,
            atClick: this.handleToggleStatus
          }
        ],
        searchForm: [
          {
            $type: 'input',
            $id: 'name',
            label: '组件名称',
            $el: {
              placeholder: '请输入'
            }
          },
          {
            $type: 'select',
            $id: 'category',
            label: '分类',
            $el: {
              placeholder: '请选择'
            },
            $options: [
              {
                label: 'Java',
                value: 'Java'
              },
              {
                label: 'PHP',
                value: 'PHP'
              },
              {
                label: 'JavaScript',
                value: 'JavaScript'
              }
            ]
          },
          {
            $type: 'select',
            $id: 'status',
            label: '状态',
            $el: {
              placeholder: '请选择'
            },
            $options: [
              {
                label: '上架',
                value: 1
              },
              {
                label: '下架',
                value: 0
              }
            ]
          }
        ],
        form: [
          {
            $type: 'input',
            $id: 'name',
            label: '用户名',
            $el: {
              placeholder: '请输入'
            },
            rules: [
              {
                required: true,
                message: '请输入用户名',
                trigger: 'blur'
              }
            ]
          }
        ]
      }
    }
  },
  methods: {
    async handleView(row) {
      const h = this.$createElement
      this.$msgbox({
        title: '消息',
        message: h(
          'div',
          null,
          this.tableConfig.columns
            .filter(item => ['name', 'version'].indexOf(item.prop) !== -1)
            .map(item => h('p', null, `${item.label}: ${row[item.prop]}`))
        ),
        showCancelButton: true,
        confirmButtonText: '确定',
        showCancelButton: false
      })
      return false
    },
    async handleEdit(row) {
      this.$prompt('请输入组件名称', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        inputValidator: val => (val ? true : '不能为空')
      }).then(value => {
        this.$axios.$put(`${component}/${row.id}`, {name: value})
        this.$message({
          type: 'success',
          message: '修改成功'
        })
      })
      return false
    },
    async handleToggleStatus(row) {
      // should not change the params directly
      row.status = !row.status
      return false
    }
  }
}
</script>
<style lang="less">
.comp--active {
  color: rgb(160, 193, 86);
}
</style>
