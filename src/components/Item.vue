<template>
  <q-item>
    <q-item-section side>
      <div class="priority" :style="{ background: p(item.priority) }"></div>
    </q-item-section>
    <q-item-section>{{ item.name }}</q-item-section>
    <q-item-section side>
      <q-btn flat round color="positive" icon="done" @click="done" v-if="!item.done"></q-btn>
    </q-item-section>
    <q-item-section side style="margin-left: -15px">
      <q-btn flat round color="negative" icon="delete" @click="$emit('garbage')"></q-btn>
    </q-item-section>
  </q-item>
</template>

<script>
export default {
  name: "Item",
  props: ["item"],
  methods: {
    done() {
      this.$emit("done");
    },
    p(p) {
      return p === "High" ? "red" : p === "Medium" ? "#efef42" : "green";
    }
  }
};
</script>

<style lang="scss">
.priority {
  width: 20px;
  height: 20px;
  border-radius: 50%;
}
</style>
