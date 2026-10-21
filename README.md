# 企业员工绩效管理系统 (Employee Management System)

基于 Vue3 + Vite + Element Plus 开发的 B 端员工管理后台，包含登录鉴权、数据增删改查、分页搜索及权限控制功能。

## 🛠 技术栈
- 前端框架：Vue3 (Composition API)
- 构建工具：Vite
- 路由管理：Vue Router
- UI 组件库：Element Plus

## ✨ 核心功能
1. **登录与路由守卫**：基于 localStorage 模拟 Token 存储，拦截未登录访问。
2. **员工列表**：支持关键字搜索、前端分页展示。
3. **数据操作**：支持新增、编辑、删除员工信息（带表单正则校验与二次确认弹窗）。
4. **RBAC 权限模拟**：根据登录角色（管理员/普通员工）动态控制删除按钮的显隐。
5. **细节优化**：表单校验状态清理、删除最后一页数据自动回退上一页。

## 🚀 本地运行指南
```bash
# 克隆项目
git clone https://github.com/hailunyegeer-sudo/employee-management-system.git

# 安装依赖
npm install

# 启动项目
npm run dev
