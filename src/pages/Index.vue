<template>
  <q-page class="flex flex-center column">
    <div class="column row-md wrap justify-evenly" style="width: 90%">
      <div class="col-4">
        <q-card>
          <q-card-section>
            <div class="text-h6">Not Completed</div>
          </q-card-section>

          <q-list separator bordered>
            <Item
              v-for="item in tasks.filter(x => !x.done)"
              :key="item.id"
              :item="item"
              @done="done(item.id)"
              @garbage="delId = item.id; del = true"
            ></Item>
          </q-list>
        </q-card>
      </div>
      <div class="col-4">
        <q-card>
          <q-card-section>
            <div class="text-h6">Completed</div>
          </q-card-section>

          <q-list separator bordered>
            <Item
              v-for="item in tasks.filter(x => x.done)"
              :key="item.id"
              :item="item"
              @garbage="delId = item.id; del = true"
            ></Item>
          </q-list>
        </q-card>
      </div>
    </div>
    <q-btn color="purple-8" label="Add task" style="margin-top: 20px" @click="modal = true" />
    <q-dialog v-model="modal" persistent transition-show="scale" transition-hide="scale">
      <q-card>
        <q-card-section>
          <div class="text-h6">Add Task</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-form ref="form" class="q-gutter-md">
            <q-input
              v-model="name"
              label="Name"
              dense
              style="margin-bottom: 15px"
              :rules="[x => !!x.trim() || 'Please provide a name.']"
            />
            <q-select
              v-model="priority"
              :options="['High', 'Medium', 'Low']"
              label="Priority"
              dense
              :rules="[x => !!x.trim() || 'Please provide a priority.']"
            />
          </q-form>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="CANCEL" color="primary" v-close-popup />
          <q-btn flat label="OK" color="primary" @click="add" />
        </q-card-actions>
      </q-card>
    </q-dialog>
    <q-dialog v-model="del" persistent>
      <q-card>
        <q-card-section class="row items-center">
          <span class="q-ml-sm">Are you sure you want to delete this task?</span>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="NO" color="primary" v-close-popup />
          <q-btn flat label="YES" color="primary" v-close-popup @click="garbage(delId)" />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<style lang="scss">
.q-item {
  // hacky
  min-height: initial !important;
}

.col-4 {
  margin-top: 20px;
}
</style>

<script>
import Item from "../components/Item.vue";
export default {
  name: "PageIndex",
  data() {
    return {
      modal: false,
      name: "",
      priority: "",
      tasks: [],
      del: false,
      delId: null
    };
  },
  components: { Item },
  methods: {
    add() {
      this.$refs.form.validate().then(s => {
        if (s) {
          const p = x => ["High", "Medium", "Low"].indexOf(x);
          this.tasks = [
            ...this.tasks,
            {
              name: this.name,
              priority: this.priority,
              id: Date.now().toString(36),
              done: false
            }
          ].sort((a, b) => p(a.priority) - p(b.priority));
          this.modal = false;
          this.name = this.priority = "";
          this.$q.notify({
            message: "Successfully added task.",
            icon: "done",
            color: "positive",
            timeout: 2000
          });
        }
      });
    },
    done(id) {
      const ind = this.tasks.findIndex(x => x.id === id);
      this.tasks = this.tasks.map((x, i) =>
        i === ind ? { ...x, done: true } : x
      );
      this.$q.notify({
        message: "Successfully marked task as completed.",
        icon: "done",
        color: "positive",
        timeout: 2000
      });
    },
    garbage(id) {
      const a = [...this.tasks];
      a.splice(
        this.tasks.findIndex(x => x.id === id),
        1
      );
      this.tasks = a;
      this.$q.notify({
        message: "Successfully deleted task.",
        icon: "done",
        color: "negative",
        timeout: 2000
      });
    }
  }
};
</script>
