<template>
  <div class="fondo">
    <div id="header">
      <Search v-on:query-change="querySearch" />
    </div>

    <div id="main-container">
      <h2>
        <i class="fas fa-tasks icono-titulo"></i> Listado de proyectos
      </h2>
      <TodoAdd v-on:add-todo="addTodo" />
      <Todos :todoslist="copyTodos" @delete-todo="deleteTodo" @update-todo="updateTodo"
        @update-todo-tarea="updateTodoTarea" />

    </div>
  </div>
</template>

<script>
import Search from './components/Search';
import Todos from './components/Todos';
import TodoAdd from './components/TodoAdd';


export default {
  name: 'App',
  components: {
    Todos,
    TodoAdd,
    Search,
  },
  methods: {
    deleteTodo(id) {
      this.todos = this.todos.filter(todo => todo.id !== id);
      this.copyTodos = [...this.todos];
    },
    addTodo(todo) {
      const exists = this.todos.some(existingTodo => existingTodo.title === todo.title);
      if (exists) {
        alert('Ya existe el proyecto.');
        return;
      }
      this.todos.push(todo);
      this.copyTodos = [...this.todos];
    },
    updateTodo(updatedTodo) {

      const exists = this.todos.some(existingTodo => existingTodo.title === updatedTodo.title);
      if (exists) {
        alert('Ya existe el Proyecto.');
        return;
      }
      const index = this.todos.findIndex(todo => todo.id === updatedTodo.id);
      if (index !== -1) {
        // Solo actualizar título y descripción
        this.todos[index].title = updatedTodo.title;
        this.todos[index].description = updatedTodo.description;
        // No actualizar estado ni completado aquí
        this.copyTodos = [...this.todos];
      }
    },
    updateTodoTarea(updatedTodoTarea) {
      const { todoId, tareaId, title, description } = updatedTodoTarea;

      const projectIndex = this.todos.findIndex(todo => todo.id === todoId);
      if (projectIndex === -1) {
        console.error('No se encontró el proyecto con la ID proporcionada.');
        return;
      }

      const tareaIndex = this.todos[projectIndex].tarea.findIndex(tarea => tarea.titleTarea === tareaId);
      if (tareaIndex === -1) {
        console.error('No se encontró la tarea con la ID proporcionada dentro del proyecto.');
        return;
      }

      // Actualizar título y descripción de la tarea
      this.todos[projectIndex].tarea[tareaIndex].titleTarea = title;
      this.todos[projectIndex].tarea[tareaIndex].descriptionTarea = description;

      // Actualizar la copia de los todos
      this.copyTodos = [...this.todos];
    },

    querySearch(query) {
      if (query.trim() === '') {
        this.copyTodos = [...this.todos];
      } else {
        const filteredTodos = this.todos.filter(todo => todo.title.includes(query));
        this.copyTodos = [...filteredTodos];
      }
    }
  },
  data() {
    return {
      todos: [
        {
          id: 0, title: 'Terminar Prueba Técnica', description: 'Hacer prueba en Vuej',
          tarea: [
            {
              titleTarea: "Tarea 1",
              descriptionTarea: "Es una tarea de practica",
              estado: "Completada",
              completed: false
            }
          ]
        },
        {
          id: 1, title: 'Subir proyectos a github', description: 'Subir proyectos de cada lenguaje', tarea: [
            {
              titleTarea: "Tarea 2",
              descriptionTarea: "Es una tarea de practica",
              estado: "Pendiente",
              completed: false
            }
          ]
        },
        {
          id: 2, title: 'Ir a Judo', description: 'Ascender a cinturón marrón', tarea: [
            {
              titleTarea: "Tarea 3",
              descriptionTarea: "Es una tarea de practica",
              estado: "En progreso",
              completed: false
            }
          ]
        },
        {
          id: 3, title: 'Avanzar en proyectos propios', description: 'Terminar Sistema Pos', tarea: [
            {
              titleTarea: "Tarea 4",
              descriptionTarea: "Es una tarea de practica",
              estado: "Pendiente",
              completed: false
            }
          ]
        }
      ],
      copyTodos: []
    };
  },
  created() {
    this.copyTodos = [...this.todos];
  }
};
</script>

<style>
@import "~@fortawesome/fontawesome-free/css/all.css";


.icono-titulo {
  color: black;
  margin-right: 10px;
}


* {
  box-sizing: border-box;
}

body {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 1.5em;
  padding: 0;
  margin: 0;
}

#main-container {
  border: solid 1px #ccc;
  width: 600px;
  margin: 100px auto;
}

#header {
  background: black;
  padding: 10px;
}

h2 {
  padding: 0 10px;
}
</style>
