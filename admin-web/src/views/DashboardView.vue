<script setup lang="ts">
import { computed, onMounted, reactive, ref } from 'vue'
import { getBusinessData, getDishOverview, getDishPage, getOrderOverview, getOrderPage } from '@/api/admin'
const range = ref('今日')
const real = reactive({ turnover: 0, validOrderCount: 0, newUsers: 0, orderCompletionRate: 0, waitingOrders: 0, sold: 0 })
const metrics = computed(() => [
  { label: '营业额', value: `¥ ${real.turnover.toLocaleString()}`, trend: '实时', tone: 'lime' }, { label: '有效订单', value: String(real.validOrderCount), trend: '实时', tone: 'orange' },
  { label: '新增用户', value: String(real.newUsers), trend: '今日', tone: 'blue' }, { label: '订单完成率', value: `${(real.orderCompletionRate * 100).toFixed(1)}%`, trend: '实时', tone: 'purple' }
])
const orders = ref<any[]>([]); const dishes = ref<any[]>([])
const statusName = (status:number) => ['','待付款','待接单','已接单','派送中','已完成','已取消'][status] || String(status)
onMounted(async () => {
  const [business, order, dish, orderPage, dishPage]: any[] = await Promise.all([getBusinessData(), getOrderOverview(), getDishOverview(), getOrderPage({page:1,pageSize:4}), getDishPage({page:1,pageSize:3})])
  Object.assign(real, business.data, order.data, dish.data)
  orders.value = orderPage.data.records || []; dishes.value = dishPage.data.records || []
})
</script>
<template>
  <div class="dashboard">
    <section class="welcome"><div><p class="eyebrow">WEDNESDAY · JUL 01</p><h2>晚上好，今天也在稳稳增长。</h2><p>AI 为你整理了今日经营摘要：午高峰订单同比提升 14%，香辣类菜品表现突出。</p></div><el-segmented v-model="range" :options="['今日','本周','本月']" /></section>
    <section class="metric-grid"><article v-for="m in metrics" :key="m.label" :class="['metric-card', m.tone]"><div class="metric-head"><span>{{ m.label }}</span><i></i></div><strong>{{ m.value }}</strong><small><b>{{ m.trend }}</b> 较上一周期</small></article></section>
    <section class="content-grid">
      <article class="card revenue"><div class="card-title"><div><p class="eyebrow">REVENUE PULSE</p><h3>营业趋势</h3></div><span class="soft-tag">实时数据</span></div><div class="chart"><div class="y-labels"><span>20k</span><span>15k</span><span>10k</span><span>5k</span><span>0</span></div><svg viewBox="0 0 700 220" preserveAspectRatio="none"><defs><linearGradient id="fill" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="#b8f34b" stop-opacity=".35"/><stop offset="1" stop-color="#b8f34b" stop-opacity="0"/></linearGradient></defs><path class="area" d="M0 190 C70 180,80 130,145 145 S230 80,290 108 S390 45,445 70 S540 25,590 60 S650 38,700 18 L700 220 L0 220Z"/><path class="line" d="M0 190 C70 180,80 130,145 145 S230 80,290 108 S390 45,445 70 S540 25,590 60 S650 38,700 18"/></svg><div class="x-labels"><span>08:00</span><span>10:00</span><span>12:00</span><span>14:00</span><span>16:00</span><span>18:00</span><span>20:00</span></div></div></article>
      <article class="card ai-brief"><div class="spark">✦</div><p class="eyebrow">LIVE BUSINESS BRIEF</p><h3>今日经营摘要</h3><p>数据来自后端工作台接口，AI 分析将在 Agent 后端接通后生成。</p><ul><li><span>待接单</span><b>{{ real.waitingOrders }} 单</b></li><li><span>在售菜品</span><b>{{ real.sold }} 项</b></li><li><span>订单完成率</span><b>{{ (real.orderCompletionRate*100).toFixed(1) }}%</b></li></ul><router-link to="/agent">与 Agent 深入分析 →</router-link></article>
      <article class="card recent"><div class="card-title"><div><p class="eyebrow">LATEST ORDERS</p><h3>最新订单</h3></div><router-link to="/orders">查看全部</router-link></div><table><tbody><tr v-for="o in orders" :key="o.id"><td><b>{{ o.orderDishes || '订单明细' }}</b><small>#{{ o.number }}</small></td><td>¥ {{ o.amount }}</td><td><span :class="['status', statusName(o.status)]">{{ statusName(o.status) }}</span></td></tr></tbody></table></article>
      <article class="card dish-rank"><div class="card-title"><div><p class="eyebrow">ACTIVE DISHES</p><h3>在售菜品</h3></div></div><div v-for="(d,i) in dishes" :key="d.id" class="rank"><em>0{{ i+1 }}</em><div><b>{{ d.name }}</b><span><i :style="{width: (88-i*17)+'%'}"></i></span></div><strong>¥ {{ d.price }}</strong></div></article>
    </section>
  </div>
</template>
