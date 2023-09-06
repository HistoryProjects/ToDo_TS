<template>
  <div class="app">
     <div class="container">
        <div class="head"> <h2>Todo list</h2> </div>

        <div class="todos">
          <div class="todos-completed">
            completed {{ doneCount }} / {{todos.length}}
          </div>
          
          <div class="todos-add">
             <button @click="addTask(taskName)">+</button>
             <input 
                type="text"
                placeholder="add task"
                v-model="taskName"
                @keyup.enter="addTask(taskName)"
             >
          </div>
          <div class="task-list">
              <ul v-for="task, index in filteredTodos" :key="index">
                  <li :key="task.id" :class="{ done: task.done, urgent: task.urgently }">
                      <div>
                          <input 
                          type="checkbox" 
                          id="checkbox" 
                          v-model="task.done"
                          style="cursor: pointer; width: 18px; height: 18px"  
                          />
                      
                          {{ task.name }}
                      </div>

                      <div class="starOrDel">
                          <Star 
                              v-if="task.urgently === true" 
                              @click="starActive(task.id)"
                              style="color: #FFA800"
                          />
                          <StarOutline 
                              v-else  @click="starActive(task.id)" 
                              style="color: #FFA800"
                          />
          
                          <Delete 
                              @click="removeTask(task)" 
                              style="color: #E3002B"
                          />
                      </div>
                  </li>
              </ul>
          </div>

          <div class="footer">
                <ul>
                   <li 
                      @click="selectedTab = 'All'"
                      :class="{ active: selectedTab === 'All' }"
                   >All</li>
                   <li 
                      @click="selectedTab = 'Active'"
                      :class="{ active: selectedTab === 'Active' }"
                   >Active</li>
                   <li 
                      @click="selectedTab = 'Priority'"
                      :class="{ active: selectedTab === 'Priority' }"
                   >Priority</li>
                </ul>

                <button @click="deleteCompletedTasks">Delete completed</button>
          </div>

        </div>

     </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Delete from 'vue-material-design-icons/Delete.vue';
import Star from 'vue-material-design-icons/Star.vue';
import StarOutline from 'vue-material-design-icons/StarOutline.vue';

export default defineComponent({
  name: 'App',
  components: {
    Delete,
    Star,
    StarOutline
  },
  data(){
     return{
        selectedTab: 'All' as 'All' |  'Active' | 'Priority',
        taskName: "" as string,
        todos: [
          {id: 1, name: "Сходить в магазин", urgently: false, done: true},
          {id: 2, name: "Сдать экзамен", urgently: true, done: false},
          {id: 3, name: "Покормить кота", urgently: false, done: false},
        ] as { id: number; name: string; urgently: boolean; done: boolean }[]
     }
  },
  computed: {
      filteredTodos(): { id: number; name: string; urgently: boolean; done: boolean }[] {
          if (this.selectedTab === 'All') {
            return this.todos;
          } else if (this.selectedTab === 'Active') {
            return this.todos.filter((task) => !task.done);
          } else if (this.selectedTab === 'Priority') {
            return this.todos.filter((task) => task.urgently);
          } else {
            return [];
          }
      },

      doneCount(): number {
        return this.todos.reduce((acc, task) => acc + (task.done ? 1 : 0), 0);
      }
  },
  methods:{
    starActive: function(id: number){
      this.todos.find(task => {
          if(task.id === id){
             task.urgently = !task.urgently;
          }
        }
      )
    },

    generateUniqueId(): string{
        return Math.random().toString(36).substr(2, 9);
    },

    addTask: function(taskName: string){
        if (this.taskName === "") {
            // Если поле ввода пустое, выдаем сообщение
            alert("Введите текст задачи");
        } else {
            const newTask = {
                id: Number(this.generateUniqueId()),
                name: taskName,
                urgently: false,
                done: false
            };
            this.todos.push(newTask);
            this.taskName = ""; 
        }
    },

    removeTask: function(task: object){
      this.todos = this.todos.filter(todo => todo !== task);
    },

    deleteCompletedTasks: function() {
      this.todos = this.todos.filter((task) => !task.done);
    },
  }
});
</script>

<style>
@import  './assets/style.css';

.done{ text-decoration: line-through; }
.urgent{
    font-size: 20px;
    font-weight: 900 !important;


}
</style>
