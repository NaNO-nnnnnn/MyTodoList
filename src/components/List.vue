<template>
    <div ref="listEl" class="list">
        <input type="checkbox" :id="list.text" v-model="list.isCompleted"></input>
        <label :for="list.text">{{ list.text }}</label>
        <button v-show="isHover" @click="deleteList(list)">×</button>
    </div>
</template>

<script setup>
import { ref } from 'vue';

let isHover = ref(true)
let props = defineProps(['list'])
const emits = defineEmits(['delete-list'])
let list = props.list
const listEl = ref()

// 删除淡出动画实现
const deleteList = (list) => {
    let opacity = 1
    const asFunc = async () => {
        await new Promise((resolve) => {
            let temp = setInterval(() => {
                opacity -= 0.15
                listEl.value.style = `opacity: ${opacity}`
                if (opacity <= -0.5) {
                    clearInterval(temp)
                    resolve()
                }
            }, 16)
        })
    }
    asFunc().then(() => emits('delete-list', list))
}
</script>

<style scoped>
.list{
    overflow: hidden;
}

label {
    word-break: break-all;
}

button {
    float: right;
    font-weight: bold;
    font-size: 20px;
    color: #b90505;
    background-color: #ffffff82;
    padding: 0 6px 2px;
    margin-right: 5px;
}

button:hover {
    background-color: #e9e7e781;
    border: solid 1px darkred;
}
</style>