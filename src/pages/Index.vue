<template>
  <q-page class="flex flex-center">
    <q-btn
      color="primary"
      label="Add a worry"
      size="xl"
      @click="isAddingWorry = true"
    />

    <q-btn
      v-if="worries.length"
      color="accent"
      label="Start worry time"
      size="xl"
      @click="isInWorryTime = true"
    />

    <q-dialog v-model="isAddingWorry" persistent>
      <q-card style="min-width: 400px">
        <q-card-section>
          <div class="text-h6">What are you worrying about?</div>
        </q-card-section>

        <q-card-section>
          <q-input
            v-model="newWorry"
            dense
            autofocus
            required
            @keyup.enter="registerWorry"
          />
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="Cancel" v-close-popup />
          <q-btn
            :disabled="!newWorry"
            flat
            label="Add worry"
            v-close-popup
            @click="registerWorry"
          />
        </q-card-actions>
      </q-card>
    </q-dialog>

    <q-dialog v-model="isInWorryTime" persistent>
      <q-card style="min-width: 400px">
        <q-card-section>
          <div class="text-h6">Worries</div>
        </q-card-section>

        <q-card-section>
          <template v-for="worry in worries">
            <q-list bordered separator v-if="worries[0] == worry" :key="worry">
              <q-slide-item @left="clearWorry(worry)">
                <template v-slot:left>
                  <q-icon name="done" />
                </template>

                <q-item>
                  <q-item-section>{{ worry }}</q-item-section>
                </q-item>
              </q-slide-item>
            </q-list>
          </template>
        </q-card-section>

        <q-card-actions align="center" class="text-primary">
          <q-btn
            flat
            label="Finish worrying early"
            v-close-popup
            @click="isInWorryTime = false"
          />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
export default {
  name: "PageIndex",

  data() {
    return {
      isAddingWorry: false,
      isInWorryTime: false,
      newWorry: "",
      worries: []
    };
  },

  methods: {
    registerWorry() {
      this.worries = [...this.worries, this.newWorry];
      this.newWorry = "";
      this.isAddingWorry = false;
    },

    clearWorry(worry) {
      this.worries = this.worries.filter(w => w !== worry);
      if (!this.worries.length) {
        this.isInWorryTime = false;
      }
    }
  }
};
</script>
