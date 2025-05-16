<template>
  <div class="login-warp flex-center">
    <div class="login-container w-full h-full">
      <el-row class="container w-full h-full">
        <el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24" class="full-container flex-center">
          <el-dropdown trigger="click" type="primary" class="lang" v-if="lang">
            <template #dropdown>
              <el-dropdown-menu style="width: 180px">
                <el-dropdown-item
                  v-for="(lang, index) in langList"
                  :key="index"
                  :value="lang.value"
                  @click="changeLang(lang.value)"
                  class="flex-between"
                >
                  <span :class="lang.value === user.getLanguage() ? 'primary' : ''">{{
                    lang.label
                  }}</span>

                  <el-icon
                    :class="lang.value === user.getLanguage() ? 'primary' : ''"
                    v-if="lang.value === user.getLanguage()"
                  >
                    <Check />
                  </el-icon>
                </el-dropdown-item>
              </el-dropdown-menu>
            </template>
          </el-dropdown>
          <slot></slot>
        </el-col>
      </el-row>
    </div>
  </div>
</template>
<script setup lang="ts">
import { computed } from 'vue'
import { getThemeImg } from '@/utils/theme'
import useStore from '@/stores'
import { useLocalStorage } from '@vueuse/core'
import { langList, localeConfigKey, getBrowserLang } from '@/locales/index'
defineProps({
  lang: {
    type: Boolean,
    default: true
  }
})
defineOptions({ name: 'LoginLayout' })
const { user } = useStore()

const changeLang = (lang: string) => {
  useLocalStorage(localeConfigKey, getBrowserLang()).value = lang
  window.location.reload()
}

const currentLanguage = computed(() => {
  return langList.value?.filter((v: any) => v.value === user.getLanguage())?.[0]?.label
})

const fileURL = computed(() => {
  if (user.themeInfo?.loginImage) {
    if (typeof user.themeInfo?.loginImage === 'string') {
      return user.themeInfo?.loginImage
    } else {
      return URL.createObjectURL(user.themeInfo?.loginImage)
    }
  } else {
    return ''
  }
})

const loginImage = computed(() => {
  if (user.themeInfo?.loginImage) {
    return `${fileURL.value}`
  } else {
    return new URL(`../../assets/theme/${getThemeImg(user.themeInfo?.theme)}.jpg`, import.meta.url)
      .href
  }
})
</script>
<style lang="scss" scoped>
.login-warp {
  height: 100vh;
  background-color: #f5f7fa; /* 修改背景颜色 */
  //background-image: url('../../assets/theme/default.jpg'); /* 修改背景图片 */
  background-repeat: no-repeat;
  background-position: center;
  background-size: cover;

  .login-container {
    width: 100%;
    height: 100%;
  }

  .full-container {
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .lang {
    position: absolute;
    right: 20px;
    top: 20px;
  }
}
</style>