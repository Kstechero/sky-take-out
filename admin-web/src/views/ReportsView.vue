<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { getOrderReport, getTop10Report, getTurnoverReport, getUserReport } from '@/api/admin'
const pad=(n:number)=>String(n).padStart(2,'0');const fmt=(d:Date)=>`${d.getFullYear()}-${pad(d.getMonth()+1)}-${pad(d.getDate())}`
const end=new Date(),begin=new Date(Date.now()-6*86400000);const dates=ref<[string,string]>([fmt(begin),fmt(end)])
const loading=ref(false);const data=ref<any>({turnover:{},orders:{},users:{},top:{}})
async function load(){loading.value=true;try{const [turnover,orders,users,top]:any[]=await Promise.all([getTurnoverReport(...dates.value),getOrderReport(...dates.value),getUserReport(...dates.value),getTop10Report(...dates.value)]);data.value={turnover:turnover.data,orders:orders.data,users:users.data,top:top.data}}finally{loading.value=false}}
onMounted(load)
</script>
<template><div v-loading="loading"><section class="page-intro"><div><p class="eyebrow">BUSINESS ANALYTICS</p><h2>数据统计</h2><p>营业额、订单、用户与销量排名均来自后端报表接口</p></div><div><el-date-picker v-model="dates" type="daterange" value-format="YYYY-MM-DD"/><el-button type="primary" @click="load">查询</el-button></div></section><section class="report-grid"><article class="card"><p class="eyebrow">TURNOVER</p><h3>营业额趋势</h3><strong>{{ data.turnover.turnoverList || '暂无数据' }}</strong><small>{{ data.turnover.dateList }}</small></article><article class="card"><p class="eyebrow">ORDERS</p><h3>订单统计</h3><strong>{{ data.orders.totalOrderCount || 0 }} 单</strong><small>有效订单 {{ data.orders.validOrderCount || 0 }}</small></article><article class="card"><p class="eyebrow">USERS</p><h3>用户增长</h3><strong>{{ data.users.totalUserList || '暂无数据' }}</strong><small>新增 {{ data.users.newUserList }}</small></article><article class="card"><p class="eyebrow">TOP 10</p><h3>销量排名</h3><strong>{{ data.top.nameList || '暂无数据' }}</strong><small>{{ data.top.numberList }}</small></article></section></div></template>
