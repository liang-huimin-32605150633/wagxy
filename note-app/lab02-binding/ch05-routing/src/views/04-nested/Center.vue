<template>
  <div class="page">
    <h2>用户中心（外壳）</h2>
    <p class="muted">访问 <code>/center/xxx</code> 时我（外壳）不动，我肚子里的 router-view 换子组件的出口。</p>

    <nav class="sub-nav">
      <router-link to="/center/profile">个人资料</router-link>
      <router-link to="/center/orders">我的订单</router-link>
    </nav>

    <!-- 补全：子路由出口组件 -->
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'Center'
}
</script>

<style scoped>

</style>