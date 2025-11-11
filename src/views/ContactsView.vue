<template>
  <div class="contacts-container">
    <el-container>
      <el-header>
        <h1>通讯录管理系统</h1>
      </el-header>

      <el-main>
        <!-- 添加/编辑联系人表单 -->
        <el-card class="form-card">
          <template #header>
            <span>{{ editingContact ? '编辑联系人' : '添加联系人' }}</span>
          </template>

          <el-form :model="contactForm" :rules="rules" ref="formRef" label-width="80px">
            <el-form-item label="姓名" prop="name">
              <el-input v-model="contactForm.name" placeholder="请输入姓名" clearable></el-input>
            </el-form-item>
            <el-form-item label="电话" prop="phone">
              <el-input v-model="contactForm.phone" placeholder="请输入电话号码" clearable></el-input>
            </el-form-item>
            <el-form-item label="邮箱">
              <el-input v-model="contactForm.email" placeholder="请输入邮箱（可选）" clearable></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="handleSubmit">
                {{ editingContact ? '更新' : '添加' }}
              </el-button>
              <el-button @click="handleReset" v-if="editingContact">取消</el-button>
            </el-form-item>
          </el-form>
        </el-card>

        <!-- 联系人列表 -->
        <el-card class="table-card">
          <template #header>
            <span>联系人列表</span>
          </template>

          <el-table :data="contacts" stripe style="width: 100%" v-loading="loading">
            <el-table-column prop="name" label="姓名" width="180"></el-table-column>
            <el-table-column prop="phone" label="电话"></el-table-column>
            <el-table-column prop="email" label="邮箱"></el-table-column>
            <el-table-column label="操作" width="200">
              <template #default="scope">
                <el-button size="small" @click="handleEdit(scope.row)">编辑</el-button>
                <el-button size="small" type="danger" @click="handleDelete(scope.row.id)">
                  删除
                </el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-card>
      </el-main>
    </el-container>
  </div>
</template>

<script>
import { ElMessage, ElMessageBox } from 'element-plus'

export default {
  name: 'ContactsView',
  data() {
    return {
      contacts: [],
      contactForm: {
        name: '',
        phone: '',
        email: ''
      },
      editingContact: null,
      loading: false,
      rules: {
        name: [
          { required: true, message: '请输入姓名', trigger: 'blur' },
          { min: 2, max: 10, message: '姓名长度在 2 到 10 个字符', trigger: 'blur' }
        ],
        phone: [
          { required: true, message: '请输入电话号码', trigger: 'blur' },
          { pattern: /^1[3-9]\d{9}$/, message: '请输入正确的手机号码', trigger: 'blur' }
        ]
      }
    }
  },
  async mounted() {
    await this.loadContacts()
  },
  methods: {
    // 加载联系人列表
    async loadContacts() {
      this.loading = true
      try {
        const response = await fetch('https://eight32302226-contacts-backend.onrender.com/api/contacts')
        if (response.ok) {
          this.contacts = await response.json()
        } else {
          ElMessage.error('加载联系人失败')
        }
      } catch (error) {
        ElMessage.error('网络错误，请检查后端服务是否启动')
      } finally {
        this.loading = false
      }
    },

    // 提交表单（添加或更新）
    async handleSubmit() {
      try {
        // 表单验证
        await this.$refs.formRef.validate()

        const url = 'https://eight32302226-contacts-backend.onrender.com/api/contacts'
        const method = this.editingContact ? 'PUT' : 'POST'
        const fullUrl = this.editingContact ? `${url}/${this.editingContact.id}` : url

        const response = await fetch(fullUrl, {
          method: method,
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(this.contactForm)
        })

        if (response.ok) {
          ElMessage.success(this.editingContact ? '更新成功' : '添加成功')
          await this.loadContacts()
          this.handleReset()
        } else {
          ElMessage.error('操作失败')
        }
      } catch (error) {
        if (error.errors) {
          // 表单验证失败
          return
        }
        ElMessage.error('网络错误')
      }
    },

    // 编辑联系人
    async handleEdit(contact) {
      try {
        // 关键：从后端重新获取数据，不使用缓存
        const response = await fetch(`https://eight32302226-contacts-backend.onrender.com/api/contacts/${contact.id}`)
        if (response.ok) {
          this.editingContact = await response.json()
          this.contactForm = { ...this.editingContact }
        } else {
          ElMessage.error('获取联系人信息失败')
        }
      } catch (error) {
        ElMessage.error('网络错误')
      }
    },

    // 删除联系人
    async handleDelete(id) {
      try {
        await ElMessageBox.confirm(
          '确定要删除这个联系人吗？',
          '警告',
          {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning',
          }
        )

        const response = await fetch(`https://eight32302226-contacts-backend.onrender.com/api/contacts/${id}`, {
          method: 'DELETE'
        })

        if (response.ok) {
          ElMessage.success('删除成功')
          await this.loadContacts()
        } else {
          ElMessage.error('删除失败')
        }
      } catch (error) {
        if (error === 'cancel') {
          return // 用户取消删除
        }
        ElMessage.error('网络错误')
      }
    },

    // 重置表单
    handleReset() {
      this.contactForm = { name: '', phone: '', email: '' }
      this.editingContact = null
      this.$refs.formRef?.clearValidate()
    }
  }
}
</script>

<style scoped>
.contacts-container {
  height: 100vh;
  background-color: #f5f5f5;
}

.el-header {
  background-color: #409eff;
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
}

.el-main {
  padding: 20px;
  max-width: 1000px;
  margin: 0 auto;
}

.form-card, .table-card {
  margin-bottom: 20px;
}

h1 {
  margin: 0;
  font-size: 24px;
}
</style>
