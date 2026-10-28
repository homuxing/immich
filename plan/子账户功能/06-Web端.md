# Web 端

> 所属功能：子账户

## 子账户管理页

位置：`用户设置 > 子账户`

页面只提供：

- 子账户列表。
- 创建子账户。
- 修改名称。
- 删除子账户。
- 切换到子账户。

不提供密码、邮箱、头像、配额、共享或权限设置。

名称输入会去除前后空格。服务端返回重名错误时，在当前输入框附近显示提示。

## 账户切换器

在现有账户菜单中增加：

- 当前主账户。
- 当前主账户的全部子账户。
- 当前账户选中状态。
- “管理子账户”入口。

进入子账户后，导航栏显示子账户名称。

## 切换流程

1. 如果正在上传，提示先完成或取消上传。
2. 调用切换账户接口。
3. 清理 TanStack Query 缓存。
4. 清理时间线、文件夹树、照片多选状态和旧照片页面。
5. 断开旧实时连接。
6. 重新读取 `/users/me` 和偏好设置。
7. 跳转到新账户的照片首页。

切换完成后不能继续显示旧账户的照片、文件夹或相册。

服务端切换接口必须按登录会话 ID 断开旧实时连接，客户端再建立新连接。

同一浏览器中的多个标签页共用登录 Cookie。切换账户后通过 `BroadcastChannel` 通知其他标签页刷新账户状态，避免一个标签页显示子账户、另一个仍显示主账户。

## 页面权限

子账户模式隐藏：

- 修改密码。
- OAuth。
- Passkey。
- API Key。
- 设备管理。
- 子账户管理。
- 相册共享。
- Partner。
- 人物共享。
- 分享链接。

子账户模式保留：

- 上传。
- 时间线。
- 文件夹。
- 相册。
- 搜索。
- 人物。
- 标签。
- 回忆。
- 收藏、归档和回收站。
- 地图和探索。

管理员入口按登录主账户权限显示，子账户本身不会增加管理员权限。

服务端必须拒绝子账户调用账号资料、设备会话、API Key、Passkey、OAuth、PIN、License 和子账户管理接口。Web 隐藏入口只用于避免误操作。

## 代码改动

新增：

```text
web/src/routes/(user)/user-settings/SubaccountSettings.svelte
web/src/lib/components/shared-components/navigation-bar/AccountSwitcher.svelte
web/src/lib/modals/CreateSubaccountModal.svelte
web/src/lib/modals/DeleteSubaccountModal.svelte
```

修改：

```text
web/src/routes/(user)/user-settings/+page.svelte
web/src/lib/managers/auth-manager.svelte.ts
web/src/lib/managers/timeline-manager/
web/src/lib/stores/folders.svelte.ts
web/src/lib/managers/AssetCacheManager.svelte.ts
web/src/lib/managers/asset-multi-select-manager.svelte.ts
web/src/lib/stores/websocket.ts
web/src/lib/components/shared-components/navigation-bar/AccountInfoPanel.svelte
web/src/lib/components/shared-components/navigation-bar/NavigationBar.svelte
```

## 文案

至少完成中文和英文：

- 子账户
- 创建子账户
- 修改子账户名称
- 切换到子账户
- 返回主账户
- 删除子账户
- 当前子账户

## 验收

- 主账户可以创建、改名和删除自己的子账户。
- 重名提示准确。
- 切换后时间线、文件夹、收藏、地图、探索和搜索只显示子账户数据。
- 切回主账户后没有子账户残留数据。
- 刷新页面后保持当前账户。
- 管理员主账户在子账户中仍可看到管理员入口。
- 窄屏下账户菜单没有文字溢出或遮挡。
