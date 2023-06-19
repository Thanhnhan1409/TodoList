<!-- eslint-disable vue/no-mutating-props -->
<template>
  <div class="task-container">
    <div class="overlay" @click="closePopup()"></div>
    <div class="content">
      <h2>{{ title }}</h2>
      <input v-model="task.task_name" type="text" placeholder="Title" />
      <textarea
        v-model="task.description"
        type="text"
        placeholder="Description"
      ></textarea>
      <flatPickr
        v-model="task.due_date"
        placeholder="Select date"
        :options="flatpickrOptions"
        name="date"
      />
      <button @click="addTask()" @fetchData="getAll()">{{ title }}</button>
      <svg
        xmlns="http://www.w3.org/2000/svg"
        height="1em"
        viewBox="0 0 384 512"
        @click="closePopup()"
      >
        <path
          d="M342.6 150.6c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L192 210.7 86.6 105.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3L146.7 256 41.4 361.4c-12.5 12.5-12.5 32.8 0 45.3s32.8 12.5 45.3 0L192 301.3 297.4 406.6c12.5 12.5 32.8 12.5 45.3 0s12.5-32.8 0-45.3L237.3 256 342.6 150.6z"
        />
      </svg>
    </div>
  </div>
</template>
<script>
import flatPickr from "vue-flatpickr-component";
require("flatpickr/dist/themes/material_green.css");
export default {
  components: {
    flatPickr
  },
  props: {
    title: {
      type: String,
      required: true,
      default: '',
    },
    task: {
      type: Object,
      required: true,
      default: () => ({ task_name: '', description: '', due_date: '' }),
    },
  },
  
  data() {
    return {
      flatpickrOptions: {
        dateFormat: 'Y-m-d', 
        minDate: 'today', 
        time_24hr: true,
      },
    }
  },
  computed: {
    formatInputDate(date) {
      const dateObject = new Date(date)
      // dateObject.setDate(dateObject.getDate() + 1);
      return dateObject.toISOString().split('T')[0]
    },
  },
  methods: {
    fetchData() {
      this.$emit('fectchData')
    },
    addTask() {
      this.$emit('addTask', 'task')
      this.closePopup()
    },
    closePopup() {
      this.$emit('closePopup')
    },
    // formatInputDate(date) {
    //   const dateObject = new Date(date);
    //   // dateObject.setDate(dateObject.getDate() + 1);
    //   return dateObject.toISOString().split('T')[0]
    // },
  },
}
</script>
<style scoped>
.task-container {
  width: 100%;
  position: absolute;
  top: 10%;
  left: 0;
  right: 0;
  bottom: 0;
}
@keyframes popup-visible {
  from {
    transform: scale(0.2);
    opacity: 0;
  }
  to {
    transform: scale(1);
    opacity: 1;
  }
}

.overlay {
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  opacity: 0.8;
  background-color: rgb(139, 142, 144);
  z-index: 9;
}
.content {
  box-shadow: 3px 3px 10px #424040;
  background-color: #ffff;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  width: 30%;
  margin: auto;
  z-index: 10;
  padding: 10px 20px;
  border-radius: 10px;
  text-align: center;
  animation: popup-visible 0.3s ease forwards;
}
h2 {
  padding: 10px;
  border-bottom: 1px solid black;
  margin-bottom: 20px;
}
input {
  width: 90%;
  padding: 5px 10px;
  margin: 5px 10px;
  border-radius: 7px;
  border: 1px solid black;
  word-wrap: break-word;
  overflow-wrap: break-word;
  white-space: pre-wrap;
}
textarea {
  width: 90%;
  height: auto;
  padding: 5px 10px;
  margin: 5px 10px;
  border-radius: 7px;
  border: 1px solid black;
  resize: none;
  height: auto;
  min-height: 80px;
  overflow: auto;
}
button {
  background-color: #dee6d9;
  border: 1px solid black;
  width: 90%;
  padding: 5px 10px;
  margin: 20px 10px;
  /* margin-bottom: 20px; */
  border-radius: 7px;
  cursor: pointer;
}
svg {
  position: absolute;
  right: 17px;
  top: 17px;
  z-index: 99;
  cursor: pointer;
}
</style>
