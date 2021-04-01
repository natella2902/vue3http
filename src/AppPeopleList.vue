<template>
  <div v-if="people.length !== 0" class="card line" v-for="person in people" :key="person.id">
    <h3> {{ person.firstName }} </h3>
    <button
        class="btn danger"
        @click="$emit('remove', person.id)"
    >Удалить</button>
  </div>
  <div class="card center" v-else>
    <h3>Нет людей, создайте</h3>
    <button class="btn primary"
            @click="$emit('load')"
    > Загрузить список </button>
  </div>
</template>

<script>
export default {
 props: ['people'],
 emits: ['load', 'remove']
}
</script>

<style scoped>
.line {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>