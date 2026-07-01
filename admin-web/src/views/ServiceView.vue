<script setup lang="ts">
import { ref } from 'vue'

const active = ref(0)
const sessions = [
  { name: '林小姐', text: '订单一直没有送到…', time: '2分钟前', n: 2 },
  { name: '陈先生', text: '可以修改配送地址吗？', time: '8分钟前', n: 1 },
  { name: '周女士', text: '谢谢，问题解决了', time: '21分钟前', n: 0 }
]
</script>
<template><div class="service-page card"><aside><div class="service-title"><div><p class="eyebrow">HUMAN SERVICE</p><h2>客服工作台</h2></div><span>3 人排队</span></div><el-input placeholder="搜索用户或会话"/><div class="session" v-for="(s,i) in sessions" :key="s.name" :class="{active:active===i}" @click="active=i"><div class="avatar">{{s.name[0]}}</div><div><b>{{s.name}}</b><p>{{s.text}}</p></div><small>{{s.time}}</small><i v-if="s.n">{{s.n}}</i></div></aside><section class="service-chat"><div class="customer-head"><div class="avatar">{{sessions[active].name[0]}}</div><div><b>{{sessions[active].name}}</b><small>会话进行中 · 来自小程序</small></div><el-button>结束会话</el-button></div><div class="empty-conversation"><span>💬</span><h3>选择消息开始处理</h3><p>客服消息接口接通后，将支持历史记录与实时回复。</p></div><div class="reply"><el-input placeholder="输入回复内容…"/><el-button type="primary">发送</el-button></div></section><aside class="customer-info"><p class="eyebrow">CUSTOMER PROFILE</p><h3>用户信息</h3><dl><dt>用户昵称</dt><dd>{{sessions[active].name}}</dd><dt>历史订单</dt><dd>12 单</dd><dt>最近订单</dt><dd>#202607010018</dd><dt>服务标签</dt><dd><span class="soft-tag">配送咨询</span></dd></dl></aside></div></template>
