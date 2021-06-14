## 权限管理

权限控制是中后台系统中常见的需求之一，我们提供的 路由权限 和 指令权限，实现一些基本的权限控制功能。

### 路由和默认权限控制

我们提供了两套权限实现方案，其中默认方案为前端固定路由表和权限配置，由后端提供用户权限标识，来识别是否拥有该路由权限。另一套方案为后端提供权限和路由信息结构接口，动态生成权限和菜单。

默认实现方式是通过获取当前用户的权限去比对路由表，生成当前用户具有的权限可访问的路由表，通过 router.addRoutes 动态挂载到 router 上。

整体流程可以看这张图:

![vue路由](http://sleepclound.ltd:9000/docs/architect/vue/router-permission.png)

步骤如下:

1. 判断是否有 `AccessToken` 如果没有则跳转到登录页面
2. 获取用户信息和拥有权限`store.dispatch('GetInfo')`
3. 用户信息获取成功后, 调用 `store.dispatch('GenerateRoutes', userInfo)` 根据获取到的用户信息构建出一个已经过滤好权限的路由结构
> TODO (`src/store/modules/permission.js`)
> 4. 将构建的路由结构信息利用 Vue-Router 提供的动态增加路由方法 router.addRoutes 加入到路由表中
> 5. 加入路由表后将页面跳转到用户原始要访问的页面,如果没有 redirect 则进入默认页面 (/dashboard/workplace)


> 这里可以看出把 登录 和 获取用户信息 分成了两个接口，主要目的在于当用户刷新页面时，可以根据登录时获取到的身份令牌（cookie/token）等，去获取用户信息，从而避免刷新需要调用登录接口
> 
> Pro 实现的路由权限的控制代码都在 @/permission.js 中，如果想修改逻辑，直接在适当的判断逻辑中 next() 释放钩子即可。
> 两套权限实现 均使用 @/permission.js （路由守卫）来进行控制。