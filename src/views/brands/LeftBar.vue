<template>
  <aside class="menu is-hidden-mobile">
    <p class="menu-label">
      品牌
    </p>
    <ul class="menu-list">
      <li>
        <router-link to="/brands/list" :class="$route.fullPath === '/brands/list' ? 'is-active': ''">品牌列表
        </router-link>
      </li>
      <li>
        <router-link to="/brands/new" :class="$route.fullPath === '/brands/new' ? 'is-active': ''">添加新品牌
        </router-link>
      </li>
    </ul>
  </aside>
</template>

<script>
    export default {}
</script>
