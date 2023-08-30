<template>

    <div v-html="str"></div>
    <button v-on:click="func">static</button>
    <button @click ="func">sss</button>
    <button @[event]="func">dynamic</button>

    <div :key="item" v-for="item in arr">{{ item }}</div>
        
</template>
    
<script setup lang='ts'>

const event = 'click'

const a:number[] = [1,2,3,4,5]
const arr = ['a','b']
arr.splice(2,0,"ppp")
const str:string = '<section style="color:red">Test File'

const func = () => {
    
}
    
</script>
    
<style>
    
</style>