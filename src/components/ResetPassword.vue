<script lang="ts" setup>
import { NButton, NInput, NGrid, NGi, NSpace, useMessage } from 'naive-ui'
import { onUnmounted, ref } from 'vue'
import { ContentType, saveData } from '../lib/decrypt'
const props = defineProps<{
    unlock: (password: string, tips?: boolean) => void,
    psd: string,
    content: string,
    type: ContentType
}>()
const message = useMessage()
const newpassword = ref("")
const buttonDisable = ref(false)

const interval = ref<null | any>(null)
const applyNew = async () => {
    if (newpassword.value === "") {
        message.error("password can not be blank! ")
        return
    }
    buttonDisable.value = true
    await saveData(props.content, newpassword.value, props.type)
    props.unlock(newpassword.value, false)
}

onUnmounted(() => {
    if (interval.value != null) clearInterval(interval.value)
})

const cancel = () => {
    props.unlock(props.psd)
}
const handlePassword = (str: string) => {
    newpassword.value = str
}
const handleKeyUp = (p: KeyboardEvent) => {
    if (p.key === "Enter") {
        applyNew()
    }
}
</script>
<template>
    <n-grid x-gap="12" cols="2" :style="{
        margin: 'auto'
    }">
        <n-gi span="2">
            <div :style="{
                marginLeft: '10px',
                marginRight: '10px'
            }">
                <n-input :value="newpassword" :on-update:value="handlePassword" placeholder="Please enter a new password"
                    @keyup="handleKeyUp" />
            </div>
        </n-gi>
        <n-gi span="2">
            <div :style="{
                marginTop: '10px',
                marginLeft: '20px',
                marginRight: '20px'
            }">Note: Modifying the password will only modify the password of the current block and will not affect other blocks!</div>
        </n-gi>
        <n-gi span="2">
            <n-space justify="space-around" :style="{
                marginTop: '10px'
            }">
                <n-button :disabled="buttonDisable" @click="applyNew" ghost type="primary">modify</n-button>
                <n-button :disabled="buttonDisable" @click="cancel" ghost type="error">Cancel</n-button>
            </n-space>
        </n-gi>
    </n-grid>
</template>
