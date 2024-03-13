# 秒懂現代前端框架核心 state management

在現代網頁開發中，前後端分離已成趨勢，前端要負擔的部分相較以前重要許多，隨著 Web 應用程式的發展，複雜性和功能需求不斷增加，狀態管理 ( state management ) 的出現使得前端開發更容易維護程式碼

# 什麼是狀態

狀態 ( state ) 指的是應用程式中數據的狀態，像是輸入欄位的值、登入的使用者資料或是彈跳視窗的顯示或隱藏等等，如何管理這些數據狀態的儲取和傳遞則稱為狀態管理 ( state management )

# 狀態管理的出現

隨著單頁應用程式 ( SPA ) 的普及和前端框架的發展，組件化逐漸成為開發的主流模式。但應用程式開發越複雜，可能會面臨跨組件的狀態傳遞問題

沒有使用狀態管理工具的狀態傳遞

![image](https://github.com/evechiu/fronend-state-management/blob/main/stateframe1.png)

如圖左下角的 B 組件如果要傳遞至右下的 D 組件，必須層層 emit 上去再 props 下去，這樣可能會有以下問題

- 狀態分散 : 狀態存在各組件中，變得零散且難以管理和追蹤，後續維護成本高
- 不易共享 : 會減少組件間的關聯且可能造成程式碼重複
- 效能問題 : 傳遞狀態的過程可能有許多組件並沒有使用到該狀態，但卻需要寫 emit 和 props，每次狀態更新都需遍歷每個經過傳遞的組件

使用狀態管理工具後

![image](https://github.com/evechiu/fronend-state-management/blob/main/stateframe2.png)

使用狀態管理工具後，左下的 B 組件可以把狀態存在 store，右下的 D 組件可以直接存取，這樣的方法有以下這些好處

- 方便管理 : 相關的狀態儲存在同一個地方，更易於管理和使用
- 效能提升 : 可以有效的免去組件間不必要的狀態傳遞，優化效能
- 組件模組化 : 將狀態從組件抽離出來，讓組件彈性更大、容易複用且更容易去做測試，實現模組化

最早提出這個設計概念是 Facebook 提出的 Flux ，為了解決 React 狀態管理的問題，此設計開創前端狀態管理的發展

# 常見的工具

上述的 Flux 啟發許多狀態管理工具的發展，例如 React 的 Redux ，Vue 的 Pinia 等等，還有專門處理從伺服器端取得的資料狀態 TanStack Query

### Pinia

配合組合式 API Vue3 出現的狀態管理工具，用於跨組件或頁面共享狀態

比方說現在畫面的框架是這樣

![image](https://github.com/evechiu/fronend-state-management/blob/main/layoutframe.png)

想要控制側邊欄 drawer 的開關，用 Pinia 可以這樣寫

```tsx
// stores/user.ts
import { defineStore } from "pinia";

export const useLayoutStore = defineStore("layout", {
  state: () => {
    return { drawer: true };
  },
  actions: {
    setDrawer(value: Boolean) {
      this.drawer = value;
    },
  },
});
```

就可以在任意組件設置 drawer 的狀態

```tsx
// views/Overview.vue
<template>
	<v-btn @click="handleDrawer">
		開關 Drawer
	</v-btn>
</template>

<script setup>
import { useLayoutStore } from '@/stores/user'

const layoutStore= useLayoutStore()
const handleDrawer = () => {
	layoutStore.setDrawer(!layoutStore.drawer)
}
</script>
```

若今天沒有使用 Pinia 去處理，兩個組件又差很遠，程式碼可能就會長這樣

```tsx
// Layout.vue
<template>
	<Drawer :isOpen="drawerIsOpen" />
	<Overview @setDrawer="setDrawer" />
</template>

<script setup>
import { ref } from 'vue'
import Drawer from './Drawer.vue'
import Overview from './Overview.vue'

const drawerIsOpen = true
const setDrawer = () => {
	drawerIsOpen.value = !drawerIsOpen.value
}
</script>

// Drawer.vue
<template>
	<div v-if="isOpen">
		drawer 內容
	</div>
</template>

<script setup>
const props = defineProps({
	isOpen: {
		type: boolean
	}
})
</script>

// Overview.vue
<template>
	<v-btn @click="handleDrawer">
		開關 Drawer
	</v-btn>
</template>

<script setup>
const emit = defineEmits(['setDrawer'])
const handleDrawer = () => {
	emit('setDrawer')
}
</script>
<template>
```

由範例可發現 Overview 想開關 drawer 的狀態還需經過 Layout，若使用 Pinia 則可免去這個問題，且因為開關狀態放在 store 裡面，也容易被存取和追蹤

# 結論

在現代前端開發中，狀態管理已成為不可或缺的一部分。通過使用狀態管理工具，開發人員能夠更有效地管理應用程序中的數據狀態，並提高代碼的可維護性和可擴展性。本文介紹了狀態管理的基本概念、出現的原因以及常見的工具，並討論了其對前端開發的重要性。隨著前端技術的不斷演進和創新，狀態管理將繼續扮演著重要的角色，我們期待著更多的發展和創新，以滿足不斷變化的開發需求。
