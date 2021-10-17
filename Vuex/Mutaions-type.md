# Mutaions-type

用于mutation的规范命名

```js
export const ACCESS_TOKEN = 'Access-Token'
export const SIDEBAR_TYPE = 'SIDEBAR_TYPE'
export const DEFAULT_THEME = 'DEFAULT_THEME'
export const DEFAULT_LAYOUT_MODE = 'DEFAULT_LAYOUT_MODE'
export const DEFAULT_COLOR = 'DEFAULT_COLOR'
export const DEFAULT_COLOR_WEAK = 'DEFAULT_COLOR_WEAK'
export const DEFAULT_FIXED_HEADER = 'DEFAULT_FIXED_HEADER'
export const DEFAULT_FIXED_SIDEMENU= 'DEFAULT_FIXED_SIDEMENU'
export const DEFAULT_FIXED_HEADER_HIDDEN = 'DEFAULT_FIXED_HEADER_HIDDEN'
export const DEFAULT_CONTENT_WIDTH_TYPE = 'DEFAULT_CONTENT_WIDTH_TYPE'
export const DEFAULT_MULTI_PAGE = 'DEFAULT_MULTI_PAGE'
export const USER_NAME = 'Login_Username'
export const USER_INFO = 'Login_Userinfo'
export const USER_AUTH = 'LOGIN_USER_BUTTON_AUTH'
export const SYS_BUTTON_AUTH = 'SYS_BUTTON_AUTH'
export const ENCRYPTED_STRING = 'ENCRYPTED_STRING'
export const ENHANCE_PRE = 'enhance_'
export const UI_CACHE_DB_DICT_DATA = 'UI_CACHE_DB_DICT_DATA'
export const INDEX_MAIN_PAGE_PATH = '/dashboard/analysis'
export const TENANT_ID = 'TENANT_ID'
export const ONL_AUTH_FIELDS = 'ONL_AUTH_FIELDS'
export const ORG_TYPE='ORG_TYPE'
export const DEPART_NAME='DEPART_NAME'
export const DEPARTS='DEPARTS'
//路由缓存问题，关闭了tab页时再打开就不刷新 #842
export const CACHE_INCLUDED_ROUTES = 'CACHE_INCLUDED_ROUTES'

export const CONTENT_WIDTH_TYPE = {
  Fluid: 'Fluid',
  Fixed: 'Fixed'
}
```

使用的话就是在需要使用的vuex当中引入使用

such as

```js
import Vue from 'vue'
import { login, getInfo, logout } from '@/api/login'
import { ACCESS_TOKEN } from '@/store/mutation-types'
import { welcome } from '@/utils/util'
 
const user = {
  ...
  actions: {
    // 登录
    Login ({ commit }, userInfo) {
      return new Promise((resolve, reject) => {
        login(userInfo).then(response => {
          const result = response.result
          Vue.ls.set(ACCESS_TOKEN, result.token, 7 * 24 * 60 * 60 * 1000)
          commit('SET_TOKEN', result.token)
          resolve()
        }).catch(error => {
          reject(error)
        })
      })
    },
}
```

