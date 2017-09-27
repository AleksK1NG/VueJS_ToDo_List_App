<template>
  <div class="hero">
    <div class="container">
      <div id="app" class="col-sm-10 col-sm-offset-1">
        <br>
        <div class="panel panel-success">
          <div class="panel-heading">
            <h3 class="text-center">Admin panel</h3>
          </div>
          <div class="panel-body">
            <div class="col-sm-4">
              <label>Отображать заголовок</label>
              <input type="checkbox" v-model="displayTitle">
              <br>
              <label>Отображать новые задачи</label>
              <input type="checkbox" v-model="displayTasks">
              <br>
              <label>Отображать статус задач</label>
              <input type="checkbox" v-model="displayTaskStats">
            </div>
            <div class="col-sm-4">
              <label>Отображать задачи</label>
              <input type="checkbox" v-model="displayTasks">
              <br>
              <label>Отображать Progress Bar</label>
              <input type="checkbox" v-model="displayProgressBar">
            </div>
            <div class="col-sm-4">
              <label>Изменить заголовок приложения</label>
              <input type="text" :value="appTitle" v-on:input="changeAppTitle">
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="row">
        <br>

        <h1 v-if="displayTitle"> {{ this.appTitle}} </h1>

        <div class="panel panel-info" v-if="displayAddTasks">
          <div class="panel-heading">
            <h3 class="text-center">Добавить новую задачу</h3>
          </div>

          <div class="panel-body">
            <form @submit="addTask">
              <div class="col-sm-8">
                <input type="text" class="form-control" v-model="tasks.name">
              </div>

              <div class="col-sm-4">
                <input type="submit" class="btn btn-primary btn-block" value="Добавить">
              </div>
            </form>
          </div>
        </div>

        <div class="panel panel-success" v-if="displayProgressBar">
          <div class="panel-heading">
            <h3 class="text-center">Completion Progress Bar</h3>
          </div>

          <div class="panel-body">
            <div class="col-sm-8 col-sm-offset-2">
              <div class="completionProgressGreyBar">
                <div class="completionProgressGreenBar text-center" :style="{ width: percentageOfTasksCompleted + '%' }">{{ Math.round(percentageOfTasksCompleted) }}%</div>
              </div>
            </div>
          </div>
        </div>

        <div class="panel panel-info" v-if="displayTaskStats">
          <div class="panel-heading">
            <h3 class="text-center">Статистика задач</h3>
          </div>

          <div class="panel-body">
            <div class="col-sm-6">
              <p @mouseover="changeTotalTasks">Всего задач: {{tasks.length}} </p>
              <p @mouseover="changeLeftToDo">Осталось выполнить: {{leftToDo}}</p>
              <p @mouseover="changeCheckMarked">Выполнено: {{checkMarkedTasks}}</p>
              <p @mouseover="changeDeleted">Удалено: {{this.deletedTasks}}</p>
            </div>
            <div class="col-sm-6">
              <h3>{{displayedTasksStatView}}</h3>
              <h3 style="padding: 10px;"  v-bind:class="manageable" class="green">{{ leftToDo < 10 ? 'Ок :)' : 'Слишком много задач' }}</h3>
            </div>
          </div>
        </div>
        <table class="table" v-if="displayTasks">
          <thead>
          <th>Выполнено</th>
          <th>Название</th>
          <th>Удалить</th>
          </thead>

          <tbody>
          <tr v-for="task in tasks">
            <td><input type="checkbox" v-model="task.done"></td>
            <td><span :class="{ taskDone: task.done }"> {{task.name}} </span></td>
            <td><button class="btn btn-danger btn-block" @click="deleteTask(task)">Удалить</button></td>
          </tr>
          </tbody>
        </table>
        <h3 class="text-center" v-else>Нет текущих задач</h3>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'app',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      appTitle: 'Список задач',
      displayTitle: true,
      displayTasks: true,
      displayProgressBar: true,
      displayAddTasks: true,
      displayTaskStats: true,
      displayedTasksStat: 'totalTasks',
      deletedTasks: 0,
      tasks: [
        {name: 'Написать приложение на Vue', done: false},
      ]
    }
  },
  methods:{
    addTask(event){
      event.preventDefault();
      if (this.tasks.name !== '' && this.tasks.name !== undefined){
        this.tasks.push({
          name: this.tasks.name,
          done: false,
        });
      }
    },
    deleteTask(task){
      this.tasks.splice(task, 1);
      this.deletedTasks++;
    },
    changeTotalTasks() {
      this.displayedTasksStat = 'totalTasks';
    },
    changeTotalTasks() {
      this.displayedTasksStat = 'totalTasks';
    },
    changeLeftToDo() {
      this.displayedTasksStat = 'leftToDo';
    },
    changeCheckMarked() {
      this.displayedTasksStat = 'checkMarked';
    },
    changeDeleted() {
      this.displayedTasksStat = 'deletedTasks';
    },
    changeAppTitle(event){
      this.appTitle = event.target.value;
    }
  },
  computed:{
    checkMarkedTasks(){
      let count = 0;
      for (let i = 0; i < this.tasks.length; ++i){
        if (this.tasks[i].done === true){
          count++;
        }
      }
      return count;
    },
    leftToDo(){
      return this.tasks.length - this.checkMarkedTasks;
    },
    displayedTasksStatView() {
      if (this.displayedTasksStat == 'totalTasks') {
        return 'Total Tasks: ' + this.tasks.length;
      } else if (this.displayedTasksStat == 'leftToDo') {
        return 'Tasks Left: ' + this.leftToDo;
      } else if (this.displayedTasksStat == 'checkMarked') {
        return 'Check Marked Tasks: ' + this.checkMarkedTasks;
      } else if (this.displayedTasksStat == 'deletedTasks') {
        return 'Deleted Tasks: ' + this.deletedTasks;
      }
    },
    manageable(){
      if (this.leftToDo < 10){
        return 'green';
      } else {
        return 'red';
      }
    },
    percentageOfTasksCompleted(){
      if (this.tasks.length === 0){
        return 0;
      } else {
        return (this.checkMarkedTasks / this.tasks.length) * 100;
      }
    }
  }
}
</script>

<style>
  .taskDone {
    text-decoration: line-through;
  }
  .completionProgressGreyBar {
    background-color: lightsteelblue;
  }
  .completionProgressGreenBar {
    width: 80%;
    height: 30px;
    background-color: limegreen;
  }
  .green {
    background-color: springgreen;
  }
  .red {
    background-color: indianred;
  }
</style>
