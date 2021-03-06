<template>
  <q-page class="flex flex-center">
    <div class="q-pa-md">
      <div class="column items-center" style="height: 400px">
        <div class="col-4 text-center">
          <h3>Worry time</h3>
        </div>

        <div class="col">
          <q-btn
            color="accent"
            label="Add a worry"
            rounded
            size="lg"
            @click="isAddingWorry = true"
          />
        </div>

        <div class="col">
          <q-btn
            v-if="worries.length"
            color="primary"
            label="Start worry time"
            rounded
            size="lg"
            @click="startWorryTime"
          />
        </div>

        <div class="col text-center q-pa-md">
          Add worries throughout the day. When you're ready, start worry time.
          We'll notify you when you've been worrying for 10 minutes.
        </div>
      </div>
    </div>

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
        <q-card-section class="text-center">
          <div class="text-h6">Your worries</div>
          <small>Swipe to move on to the next worry</small>
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
            v-if="hasWorriedForLongEnough"
            color="accent"
            label="You've worried for 10 minutes - stop now"
            class="q-mb-md"
            v-close-popup
            @click="finishWorrying"
          />

          <q-btn
            v-else
            flat
            label="Finish worrying early"
            v-close-popup
            @click="finishWorrying"
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
    let worries = localStorage.getItem("worries");
    worries = worries ? JSON.parse(worries) : [];
    return {
      isAddingWorry: false,
      isInWorryTime: false,
      hasWorriedForLongEnough: false,
      newWorry: "",
      worries: worries,
      worryTimer: undefined
    };
  },

  watch: {
    worries() {
      localStorage.setItem("worries", JSON.stringify(this.worries));
    }
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
    },

    startWorryTime() {
      this.isInWorryTime = true;
      this.worryTimer = setTimeout(
        () => (this.hasWorriedForLongEnough = true),
        10 * 60 * 1000
      );
    },

    finishWorrying() {
      this.isInWorryTime = false;
      this.hasWorriedForLongEnough = false;
      clearTimeout(this.worryTimer);
    }
  }
};
</script>
