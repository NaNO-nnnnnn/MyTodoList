<template>
    <div id="root">
        <div class="header">
            <input ref="inputEl" v-model="newList.text" @keyup.enter="addList()"></input>
            <button @click="addList()">Add</button>
            <button @click="save">Save</button>
        </div>
        <div ref="centerEl" class="center">
            <p v-show="visibleLists.length === 0">No tasks yet</p>
            <div class="lists" v-for="list in visibleLists" :key="list.id">
                <List :list="list" @delete-list="deleteList(list)"></List>
            </div>
        </div>
        <div class="footer">
            <button class="option" @click="showContent = 'all'">All</button>
            <button class="option" @click="showContent = 'active'">Active</button>
            <button class="option" @click="showContent = 'completed'">Completed</button>
        </div>
    </div>
</template>

<script setup>
import { ElMessage } from 'element-plus'
import List from './List.vue';
import { ref, reactive, computed } from 'vue';

// 获取上一次保存的任务列表
const storage = localStorage.getItem('lists') || []

let count = ref(0)
let newList = reactive({ text: '', isCompleted: false })
let allLists = reactive([])
let showContent = ref('all')

// 要显示的任务列表
const visibleLists = computed(() => {
    if (showContent.value === 'all') {
        return allLists
    } else if (showContent.value === 'active') {
        return allLists.filter((list) => list.isCompleted === false)
    } else if (showContent.value === 'completed') {
        return allLists.filter((list) => list.isCompleted === true)
    }
})

// 将localStorage中的数据赋值给 allLists 变量
if (storage.length !== 0) {
    for (let item of JSON.parse(storage)) {
        allLists.push(item)
    }
    count.value = allLists[allLists.length - 1].id + 1
}

// 新增任务的方法
const addList = () => {
    if (newList.text !== '') {
        newList.id = count.value++
        let list = {}
        Object.assign(list, newList)
        allLists.push(list)
        newList.text = ''
    } else {
        // 输入框为空时无法新增，给出提醒
        ElMessage.warning('Please input something ! ! !')
    }
}
// 永久保存任务列表
const save = () => {
    localStorage.setItem('lists', JSON.stringify(allLists))
    ElMessage.success('Saved successfully !')
}
// 删除任务
const deleteList = (list) => {
    const index = allLists.indexOf(list)
    allLists.splice(index, 1)
}
</script>

<style scoped>
#root {
    overflow: hidden;
    width: 90%;
    margin: auto;
}

.header {
    float: left;
    display: flex;
    margin-bottom: 5px;
}

p{
    color: rgb(165, 165, 165);
}

input {
    padding: 7px;
    width: 100%;
    border-radius: 6px;
    border: solid 2px gainsboro;
}

input:hover {
    border-color: black;
}

button {
    font-size: 0.9rem;
    padding: 5px;
    margin-left: 10px;
    border: solid 1.5px grey;
}

button:hover {
    background-color: #e2e2e2;
}

.center {
    clear: both;
    overflow-y: auto;
    border-bottom: solid 2px #8080802c;
}

.lists {
    padding-top: 10px;
    text-align: left;
    padding-bottom: 10px;
    border-bottom: solid 1.5px #8080802c;
}

.lists:hover {
    background-color: #e6e6e6;
}

.lists:last-child {
    border-bottom: none;
}

.option {
    font-size: 0.9rem;
    margin: 0;
    margin-right: 10px;
    padding: 0 9px;
    height: 25px;

}

.footer {
    float: left;
    margin-top: 6px;
}
</style>