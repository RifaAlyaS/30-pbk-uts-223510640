<template>
  <div>
    <nav>
      <q-btn-group push>
        <router-link to="/todos" class="router-link">
          <q-btn color="yellow" glossy text-color="white" push label="Todos" icon="Show " />
        </router-link>
        <router-link to="/posts" class="router-link">
          <q-btn color="dark-red" glossy text-color="white" push label="Posts" icon="Show " />
        </router-link>
        <router-link to="/albums" class="router-link">
          <q-btn color="dark-red" glossy text-color="white" push label="Albums" icon="Show " />
        </router-link>
      </q-btn-group>
    </nav>
    <router-view />
  </div>
</template>

<script setup>
</script>

<style scoped>
.router-link {
  text-decoration: none;
}
</style>
