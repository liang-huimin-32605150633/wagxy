<template>
  <div>
    <h3>用户详情</h3>
    <p>用户ID：{{ $route.params.id }}</p>
  </div>
</template>

<script>
export default {
  name: 'User'
}
</script>

<style scoped>
.page {
  background: var(--card, #fff);
  border: 1px solid var(--border);
  border-left: 4px solid var(--accent);
  border-radius: var(--radius);
  box-shadow: var(--shadow);
  padding: 14px 18px;
}

.big {
  font-size: 22px;
  color: var(--primary-dark);
}

code {
  background: #f0f1ff;
  border-radius: 4px;
  padding: 0 6px;
  font-family: Consolas, Monaco, monospace;
}

.muted {
  color: var(--muted);
}
</style>