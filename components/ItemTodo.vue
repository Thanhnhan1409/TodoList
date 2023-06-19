<template>
  <div class="Item--container">
    <div v-for="(task, id) in tasks" :key="id" class="task">
      <div class="content"  @click.prevent="showPopup(task)">
        <h3>{{ task.task_name }}</h3>
        <p>{{ task.description }}</p>
        <div class="icon-edit">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            height="1em"
            viewBox="0 0 512 512"
          >
            <path
              d="M362.7 19.3L314.3 67.7 444.3 197.7l48.4-48.4c25-25 25-65.5 0-90.5L453.3 19.3c-25-25-65.5-25-90.5 0zm-71 71L58.6 323.5c-10.4 10.4-18 23.3-22.2 37.4L1 481.2C-1.5 489.7 .8 498.8 7 505s15.3 8.5 23.7 6.1l120.3-35.4c14.1-4.2 27-11.8 37.4-22.2L421.7 220.3 291.7 90.3z"
            />
          </svg>
        </div>
      </div>
      <div class="due--date" :class="{ isDone: task.status === 'done' }">
        <input
          id="checkbox"
          v-model="isChecked[task.task_id]"
          :checked="isChecked[task.task_id]"
          type="checkbox"
          name=""
          @click="updateStatus(task)"
        />
        {{ formatDate(task.due_date) }}
      </div>
      <svg
        xmlns="http://www.w3.org/2000/svg"
        height="0.7em"
        viewBox="0 0 384 512"
        class="icon-close"
        @click="deleteTask(task)"
      >
        <path
          d="M342.6 150.6c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L192 210.7 86.6 105.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3L146.7 256 41.4 361.4c-12.5 12.5-12.5 32.8 0 45.3s32.8 12.5 45.3 0L192 301.3 297.4 406.6c12.5 12.5 32.8 12.5 45.3 0s12.5-32.8 0-45.3L237.3 256 342.6 150.6z"
        />
      </svg>
    </div>
    <AddOrUpdatePopup
      v-show="isShowPopup"
      :title="'Update Task'"
      :task="taskCurrent"
      @addTask="updateTask(taskCurrent)"
      @closePopup="closePopup()"
    />
    
  </div>
</template>
<script>
import AddOrUpdatePopup from './AddOrUpdatePopup.vue'
export default {
  components: { AddOrUpdatePopup },
  props: {
    tasks: {
      type: Array,
      required: true,
    }
  },
  data() {
    return {
      isChecked: [],
      status: 'undone',
      isShowPopup: false,
      taskCurrent: {},
      dueDate:[]
    }
  },
  watch: {
    tasks: {
      immediate: true,
      handler(newTask) {
        for(const task of newTask){
          this.isChecked[task.task_id] = task.status === 'done';
          this.dueDate[task.task_id] = task.due_date
        }
        
      }
    }
  },
  mounted() {
    this.fetchData()
  },
  methods: {
    formatDate(date) {
      const newDate = new Date(date)
      return newDate.toLocaleDateString('en-GB')
    },
    fetchData() {
      this.$emit('fetchData')
    },
    async deleteTask(task) {
      try {
        await this.$axios.delete(`/todos/${task.task_id}`).then(() => {
          this.fetchData()
        })
      } catch (error) {
        console.log(error)
      }
    },
    async updateStatus(task) {
      if (this.isChecked[task.task_id]) {
        this.status = 'undone'
      } else {
        this.status = 'done'
      }
      try {
        await this.$axios
          .put(`/todos/${task.task_id}/status`, {
            status: this.status,
          })
          .then(() => {
            this.fetchData()
          })
      } catch (error) {
        console.log(error)
      }
    },
    async updateTask(task) {
      this.formatInputDate(task.due_date)
      await this.$axios.put(`/todos/${task.task_id}`, task).then(() => {
        this.fetchData()
      })
    },
    closePopup() {
      this.isShowPopup = false
    },
    showPopup(task) {
      this.taskCurrent = task;
      this.taskCurrent.due_date =  this.formatInputDate(this.taskCurrent.due_date)
      localStorage.setItem('taskCurrent',JSON.stringify(task))
      this.isShowPopup = true;
    },
    formatInputDate(date) {
      const dateObject = new Date(date);
      dateObject.setDate(dateObject.getDate() + 1);
      return dateObject.toISOString().split('T')[0]
    },
  },
}
</script>
<style scoped>
.Item--container {
  width: 95%;
  border-radius: 10px;
}
.task {
  border-radius: 10px;
  padding: 10px;
  background-color: #ffff;
  margin: 10px;
  position: relative;
}
.content {
  width: 100%;
  height: 80%;
  background-color: #ffff;
}
.content p {
  color: #6e7b8b;
  margin: 20px 0;
}
h3 {
  margin: 0;
}
.due--date {
  font-weight: 550;
  padding-top: 10px;
  display: flex;
  align-items: center;
  justify-content: flex-end;
  font-size: 14px;
  color: #8b5913;
}
.due--date input {
  margin-right: 10px;
  cursor: pointer;
}
.icon-close {
  position: absolute;
  right: 10px;
  top: 10px;
  fill: #6e7b8b;
  cursor: pointer;
}
.isDone {
  color: #1f845a;
  font-weight: 650;
}
.icon-edit {
  position: absolute;
  border-radius: 50%;
  cursor: pointer;
  right: 10px;
  top: 50px;
  padding: 3px;
  height: 28px;
  transition: all 0.4s ease;
}
.icon-edit svg {
  width: 13px;
  margin: 3px;
  fill: #6e7b8b;
}
.icon-edit:hover {
  background-color: #c8c9ca;
}

</style>
