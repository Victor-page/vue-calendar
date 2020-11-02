<template>
  <div class="wrapper">
    <calendar
      @clickedDate="callModal($event)"
      @chosenDay="saveChosenDay($event)"
    >
    </calendar>

    <modal title="Create note" v-show="modal" @close="modal = false">
      <div slot="body">
        <form @submit.prevent="submitForm">
          <p class="chosenDate">Date: {{ chosenDateText }}</p>
          <textarea
            name="note"
            id="note"
            required
            rows="10"
            v-model.lazy="input"
          ></textarea>
          <button class="btn">
            Create!
          </button>
        </form>
      </div>
    </modal>
  </div>
</template>

<script>
import Calendar from "./components/Calendar.vue";
import Modal from "./components/Modal.vue";

export default {
  data: function() {
    return {
      modal: false,
      input: "",
      chosenDateText: "",
      chosenDay: ""
    };
  },
  components: {
    calendar: Calendar,
    Modal
  },
  methods: {
    callModal($event) {
      this.modal = !this.modal;
      this.chosenDateText = $event;
      this.input = "";
    },
    submitForm() {
      this.chosenDay.notes.push(this.input);
      this.modal = false;
    },
    saveChosenDay($event) {
      this.chosenDay = $event;
    }
  }
};
</script>

<style>
* {
  box-sizing: border-box;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
    "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
    "Helvetica", "Arial", sans-serif;
  color: #2c3e50;
}

textarea[name="note"] {
  display: block;
  width: 100%;
  margin-bottom: 10px;
  resize: vertical;
}

ol {
  margin: 0;
  padding-left: 10px;
}

.btn {
  display: block;
  margin-left: auto;
  padding: 8px 18px;
  background-color: #3b86ff;
  border-radius: 4px;
  border: 1px solid #d7dae2;
  box-shadow: 0 2px 3px rgba(0, 0, 0, 0.05);
  font-size: 13px;
  color: #fff;
  transition: all 0.3s ease-in-out;
  cursor: pointer;
}
</style>
