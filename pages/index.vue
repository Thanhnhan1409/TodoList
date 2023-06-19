<template>
  <div class="container">
    <h1>List to do</h1>
    <input v-model="date" class="filter-input" type="date" @input="filter()" />
    <div class="box">
      <div class="row">
        <h3>All Tasks</h3>
        <div class="items-todo">
          <ItemTodo
            :tasks="date === '' ? tasks : tasksFilter"
            @fetchData="getAll()"
          >
          </ItemTodo>
        </div>
      </div>
      <div class="row">
        <h3>Todo</h3>
        <div class="items-todo">
          <ItemTodo
            :tasks="date === '' ? taskUndone : taskUndoneFilter"
            @fetchData="getAll()"
          >
          </ItemTodo>
        </div>
      </div>
      <div class="row">
        <h3>Done</h3>
        <div class="items-todo">
          <ItemTodo
            class="test"
            :tasks="date === '' ? taskDone : taskDoneFilter"
            @fetchData="getAll()"
          >
          </ItemTodo>
        </div>
      </div>

      <AddOrUpdatePopup
        v-show="isShowPopup === 'add'"
        :title="'Add task'"
        :task="task"
        @addTask="addTask"
        @closePopup="closePopup()"
      />
    </div>
    <div class="add-task" @click="isShowPopup = 'add'">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        height="1em"
        viewBox="0 0 448 512"
      >
        <path
          d="M256 80c0-17.7-14.3-32-32-32s-32 14.3-32 32V224H48c-17.7 0-32 14.3-32 32s14.3 32 32 32H192V432c0 17.7 14.3 32 32 32s32-14.3 32-32V288H400c17.7 0 32-14.3 32-32s-14.3-32-32-32H256V80z"
        />
      </svg>

      Add task
    </div>
  </div>
</template>

<script>
import ItemTodo from '../components/ItemTodo.vue'
import AddOrUpdatePopup from '../components/AddOrUpdatePopup.vue'
export default {
  components: { ItemTodo, AddOrUpdatePopup },
  data() {
    return {
      tasks: [],
      taskUndone: [],
      taskDone: [],
      isShowPopup: '',
      task: {},
      date: '',
      tasksFilter: [],
      taskUndoneFilter: [],
      taskDoneFilter: [],
    }
  },
  mounted() {
    this.getAll()
  },
  methods: {
    async getAll() {
      await this.$axios.get(`/todos`).then((res) => {
        // console.log("aaaaa"+ res);
        this.tasks = res.data.data
        console.log(this.tasks)
        this.getByStatus('done')
        this.getByStatus('undone')
      })
    },
    async getByStatus(status) {
      try {
        await this.$axios.get(`/todos/byStatus/${status}`, {}).then((res) => {
          if (status === 'done') {
            this.taskDone = res.data.data
          } else {
            this.taskUndone = res.data.data
          }
        })
      } catch (error) {
        console.log(error)
      }
    },
    async getByDate() {
      try {
        await this.$axios.get(`/todos/byDate/${this.date}`).then((res) => {
          this.tasksFilter = res.data.data
        })
      } catch (error) {
        console.log(error)
      }
    },
    async getByStatusAndDate(status) {
      try {
        await this.$axios
          .get(`/todos/Status/${status}/Date/${this.date}`)
          .then((res) => {
            if (status === 'done') {
              this.taskDoneFilter = res.data.data
            } else {
              this.taskUndoneFilter = res.data.data
            }
          })
      } catch (error) {
        console.log(error)
      }
    },
    async addTask() {
      try {
        await this.$axios.post(`/todos`, this.task).then(() => {
          this.getAll()
          this.closePopup()
        })
      } catch (error) {
        console.log(error)
      }
    },
    closePopup() {
      this.isShowPopup = ''
    },
    formatInputDate(date) {
      const dateObject = new Date(date)
      return dateObject.toISOString().split('T')[0]
    },
    filter() {
      this.getByDate()
      this.getByStatusAndDate('done')
      this.getByStatusAndDate('undone')
    },
    showUpdatePopup() {
      console.log("nhan");
      this.isShowPopup = 'update'
      this.task = JSON.parse(localStorage.getItem('taskCurrent'))
      this.task.due_date = this.task.due_date.slice(0,10)
    },
    async updateTask() {
      console.log("test day"+this.task.due_date);
      await this.$axios
        .put(`/todos/${this.task.task_id}`, this.task)
        .then(() => {
          this.getAll()
        })
    },
  },
}
</script>
<style src="../static/css/styles.css"></style>
<style scoped>
h1 {
  text-align: center;
  margin-top: 40px;
  color: #ffff;
}
.box {
  width: 80%;
  height: 80%;
  margin: 40px auto;
  margin-top: 80px;
  background-color: rgb(243, 242, 242);
  box-shadow: 3px 3px 3px 10px rgb(243, 242, 242);
  display: flex;
  justify-content: space-around;
  border-radius: 10px;
  padding: 20px;
  position: relative;
  /* background-image: url('../static/image/background.avif'); */
}
.row {
  width: 30%;
  height: fit-content;
  min-height: 70px;
  max-height: 650px;
  background-color: #dee6d9;
  padding: 10px;
  margin-bottom: 20px;
  border-radius: 10px;
  overflow: hidden;
  box-sizing: border-box;
}
.row h3 {
  border-bottom: 1px solid black;
  padding-bottom: 15px;
  text-align: center;
}
.items-todo {
  padding-bottom: 20px;
  margin: 20px 0;
  height: fit-content;
  max-height: 550px;
  overflow-y: auto;
  overflow-x: hidden;
  border-radius: 10px;
}
* {
  scrollbar-width: auto;
  scrollbar-color: #1e3a8a #ffffff;
}
.add-task {
  position: absolute;
  right: 10%;
  top: 80px;
  /* height: 30px; */
  padding: 6px 30px 6px 10px;
  background-color: #ffff;
  box-shadow: 1px 1px 1px rgb(245, 243, 243);
  /* border: 1px solid black; */
  display: flex;
  align-items: center;
  border-radius: 10px;
  cursor: pointer;
}
.add-task svg {
  padding-right: 10px;
}

.filter-input {
  position: absolute;
  right: 10%;
  top: 120px;
  z-index: 8;
  width: 140px;
  padding: 4px 10px;
  border-radius: 8px;
  border: none;
  box-shadow: 2px 2px 5px rgb(180, 179, 179);
}
/* Chrome, Edge, and Safari */
*::-webkit-scrollbar {
  width: 8px;
}

*::-webkit-scrollbar-track {
  background: #7f9d69;
  width: 90%;
  border-radius: 10px;
  margin: 10px 0;
  /* padding-right:3px ; */
}

*::-webkit-scrollbar-thumb {
  background-color: rgb(243, 242, 242);
  border-radius: 10px;
  border: 1px solid #7e8082;
}
</style>
