<template>
    <div>
        <div v-bind:key="todo.id" v-for="todo in todoslist">
            <TodoItem :key="todo.id" :todo="todo" @delete-todo="$emit('delete-todo', todo.id)" @update-todo="updateTodo"
                @update-todo-tarea="updateTodoTarea" />
        </div>

    </div>
</template>

<script>
import TodoItem from './TodoItem';

export default {
    name: 'TodosList',
    props: ['todoslist'],
    components: { TodoItem },
    methods: {
        updateTodo(updatedTodo) {
            const index = this.todos.findIndex(todo => todo.id === updatedTodo.id);
            if (index !== -1) {
                // Actualizar título y descripción del proyecto
                this.todos[index].title = updatedTodo.title;
                this.todos[index].description = updatedTodo.description;
                // No olvides copiar los todos actualizados a copyTodos
                this.copyTodos = [...this.todos];
            }
        },
        updateTodoTarea(updatedTodoTarea) {
            const projectIndex = this.todos.findIndex(todo => todo.id === updatedTodoTarea.todoId);
            if (projectIndex !== -1) {
                const tareaIndex = this.todos[projectIndex].tarea.findIndex(tarea => tarea.titleTarea === updatedTodoTarea.tareaId);
                if (tareaIndex !== -1) {
                    // Actualizar título y descripción de la tarea
                    this.todos[projectIndex].tarea[tareaIndex].titleTarea = updatedTodoTarea.title;
                    this.todos[projectIndex].tarea[tareaIndex].descriptionTarea = updatedTodoTarea.description;
                    this.todos[projectIndex].tarea[tareaIndex].estado = updatedTodoTarea.estado;
                    // No olvides copiar los todos actualizados a copyTodos
                    this.copyTodos = [...this.todos];
                }else {
                    console.log("Cerrando el Segundo IF");
                }
            }else{
                console.log("Cerrando el primer If");
             }
        },
    }
};
</script>

<style></style>