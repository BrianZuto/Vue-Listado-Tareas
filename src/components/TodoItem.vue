<template>
  <div class="todo">
    <div class="todo-header">
      <span v-if="!editing" @dblclick="startEditing('title')"
        :class="{ 'todo-title': true, 'completed': localCompleted }">
        {{ todo.title }}
      </span>
      <input v-else type="text" class="edit-input" v-model="editedTitle" @keyup.enter="saveEdit" @blur="saveEdit" />

      <p v-if="!editing" class="todo-description" style="font-family: Arial, Helvetica, sans-serif;"
        :class="{ 'completed-description': localCompleted }" @dblclick="startEditing('description')">
        {{ todo.description }}
      </p>
      <textarea v-else class="edit-input todo-description-edit" rows="3" v-model="editedDescription"
        @keyup.enter="saveEdit" @blur="saveEdit"></textarea>

      <div class="todo-actions">

        <button @click="openModalList" class="btnEditar" style="background-color: green;">Crear Actividad</button>
        <button @click="$emit('delete-todo', todo.id)" class="btnEliminar">Eliminar</button>
        <button @click="openModal" class="btnEditar">Editar</button>
      </div>
    </div>

    <div class="todo-body">
      <ul v-if="todo.tarea && todo.tarea.length > 0" class="todo-tareas">
        <li v-for="(tarea, index) in todo.tarea" :key="index" class="todo-tarea">

          <div class="todo-header">

            <span class="tarea-estado" :class="{
              'estado-completo': tarea.estado === 'Completada',
              'estado-pendiente': tarea.estado === 'Pendiente',
              'estado-progreso': tarea.estado === 'En progreso'
            }">
              {{ tarea.estado }}
            </span>


            <span :class="{ 'todo-title': true, 'completed': localCompleted }">
              {{ tarea.titleTarea }}
            </span>

            <p v-if="!editing" class="todo-description" style="font-family: Arial, Helvetica, sans-serif;"
              :class="{ 'completed-description': localCompleted }" @dblclick="startEditing('description')">
              {{ tarea.descriptionTarea }}
            </p>


          </div>
        </li>
      </ul>

      <div class="todo-actions">
        <button class="btnEditar btnEditarTarea" @click="editTarea(tarea)">Editar</button>
      </div>

    </div>


    <!-- Ventana modal -->
    <div v-if="showModal" class="modal">
      <div class="modal-content">
        <span @click="closeModal" class="close">&times;</span>
        <h2>Editar Proyecto</h2>
        <form @submit.prevent="saveModalEdit">
          <div class="input-group">
            <label>Título:</label>
            <input type="text" v-model="editedTitle" required />
          </div>
          <div class="input-group">
            <label>Descripción:</label>
            <textarea v-model="editedDescription" style="font-family: Arial, Helvetica, sans-serif;"
              required></textarea>
          </div>
          <div class="button-group">
            <button type="submit" class="btnGuardar">Guardar</button>
            <button type="button" @click="closeModal" class="btnCancelar">Cancelar</button>
          </div>
        </form>
      </div>
    </div>





    <!-- Crear Tarea -->
    <div v-if="showModalTarea" class="modal">
      <div class="modal-content">
        <span @click="closeModalTarea" class="close">&times;</span>
        <h2>Crear Tarea</h2>
        <form @submit.prevent="saveTarea">
          <div class="input-group">
            <label>Título:</label>
            <input type="text" v-model="editedTitleTarea" required />
          </div>
          <div class="input-group">
            <label>Descripción:</label>
            <textarea v-model="editedDescriptionTarea" style="font-family: Arial, Helvetica, sans-serif;"
              required></textarea>
          </div>

          <div class="input-group">
            <label for="description">Estado:</label>
            <select v-model="selectedOptionTarea" id="description" required
              style="font-family: Arial, Helvetica, sans-serif; padding: 8px; border: 1px solid #ccc; border-radius: 4px; width: 100%; box-sizing: border-box;">
              <option disabled value="">Selecciona una opción</option>
              <option value="opcion1">Completada</option>
              <option value="opcion2">En progreso</option>
              <option value="opcion3">Pendiente</option>
            </select>
          </div>
          <div class="button-group">
            <button type="submit" class="btnGuardar">Guardar</button>
            <button type="button" @click="closeModalTarea" class="btnCancelar">Cancelar</button>
          </div>
        </form>
      </div>
    </div>
  </div>

</template>

<script>
export default {
  name: 'TodoItem',
  props: ['todo'],
  data() {
    return {
      localCompleted: this.todo.completed,
      editing: false,
      editedTitle: '',
      editedDescription: '',
      showModal: false,
      showModalTarea: false
    };
  },
  methods: {
    startEditing(field) {
      this.editing = true;
      if (field === 'title') {
        this.editedTitle = this.todo.title;
      } else if (field === 'description') {
        this.editedDescription = this.todo.description;
      }
    },
    saveEdit() {
      if (this.editedTitle.trim() !== '' && this.editedDescription.trim() !== '') {
        if (this.isTitleUnique(this.editedTitle)) {

          this.$emit('update-todo', {
            id: this.todo.id,
            title: this.editedTitle,
            description: this.editedDescription,
            completed: this.localCompleted
          });

          this.editing = false;

        } else {
          alert('El título ya existe. Por favor, elija otro título.');
        }
      } else {
        alert('No puede haber campos vacíos');
      }
    },

    isTitleUnique(newTitle) {
      // Verificar si el nuevo título es único entre los otros todos
      return this.todos.every(todo => todo.title !== newTitle || todo.id === this.todo.id);
    },
    openModal() {
      this.showModal = true;
      this.editedTitle = this.todo.title;
      this.editedDescription = this.todo.description;
    },
    openModalList() {
      this.showModalTarea = true;
      this.editedTitleTarea = this.todo.tarea.titleTarea;
      this.editedDescriptionTarea = this.todo.tarea.descriptionTarea;
      this.selectedOptionTarea = this.todo.tarea.estado;
    },
    closeModalTarea() {
      this.showModalTarea = false;
    },
    closeModal() {
      this.showModal = false;
    },
    saveModalEdit() {
      if (this.editedTitle.trim() !== '' && this.editedDescription.trim() !== '') {
        this.$emit('update-todo', {
          id: this.todo.id,
          title: this.editedTitle,
          description: this.editedDescription,
          completed: this.localCompleted
        });
        this.showModal = false;
      }
    },
    saveTarea() {
      if (this.editedTitleTarea.trim() !== '' && this.editedDescriptionTarea.trim() !== '') {
        this.$emit('update-todo-tarea', {
          todoId: this.todo.id,
          title: this.editedTitleTarea,
          description: this.editedDescriptionTarea,
          estado: this.selectedOptionTarea,
          completed: this.localCompleted
        });
        this.showModalTarea = false;
      } else {
        alert('No puede haber campos vacíos');
      }
    },

  }
};
</script>

<style scoped>
/* Estilos para la modal de edición */
.modal {
  display: block;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-content {
  background-color: #fefefe;
  margin: 15% auto;
  padding: 20px;
  border-radius: 5px;
  width: 60%;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
  cursor: pointer;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}

form {
  display: flex;
  flex-direction: column;
}

.input-group {
  margin-bottom: 10px;
}

input,
textarea {
  padding: 10px;
  outline: none;
  border: solid 1px #ccc;
  border-radius: 5px;
  width: 100%;
}

.button-group {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

button {
  padding: 8px;
  margin-left: 10px;
  border-radius: 5px;
  cursor: pointer;
  border: none;
}

.btnGuardar {
  background-color: #4caf50;
  color: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.btnCancelar {
  background-color: #f44336;
  color: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

button:hover {
  opacity: 0.8;
}

.todo {
  border: 1px solid #ccc;
  padding: 10px;
  margin-bottom: 10px;
  cursor: pointer;
}

.todo:hover {
  background-color: #eee;
}

.todo-body {
  margin-bottom: 10px;
}

.todo-header {
  margin-bottom: 10px;
  justify-content: space-between;
  /* Alinear los elementos en los extremos */
  align-items: center;
  /* Centrar verticalmente los elementos */

}

.todo-actions {
  margin-top: 10px;
  display: flex;
  align-items: center;
  justify-content: flex-end;
}

.todo-tarea {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 5px;
}



.todo-tarea p {
  margin-top: 5px;
}

.todo-tarea span::before {
  content: '*';
  /* Agregar asterisco */
  margin-right: 5px;
  /* Espacio después del asterisco */
}

.todo-tarea-details {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.tarea-estado {
  margin-left: 10px;
  color: #999;
}

.btnEliminar,
.btnEditar {
  border: none;
  border-radius: 3px;
  padding: 5px 10px;
  margin-right: 5px;
  cursor: pointer;
}

.btnEliminar {
  background-color: #e93a3a;
  color: white;
}

.btnEliminar:hover {
  background-color: #e41a1a;
}

.btnEditar {
  background-color: #4e2ce4;
  color: white;
}

.btnEditar:hover {
  background-color: #1519dd;
}


.btnEditarTarea:hover {
  background-color: #1519dd;
}

.todo-title-container {
  display: flex;
  align-items: center;
}

.todo-title {
  text-decoration: none;
}

.completed .todo-title {
  text-decoration: line-through;
  color: #ccc;
}

.todo-description {
  margin-top: 5px;
  color: #999;
  font-size: 14px;

}

.todo-description-edit {
  margin-top: 5px;
  color: #333;
  font-size: 14px;
  border: none;
  resize: none;
  outline: none;
}

.todo-header+.todo-body {
  border-top: 1px solid #ccc;
  padding-top: 10px;
  /* Opcional: ajusta el espaciado superior según sea necesario */
}

.todo-tareas {
  list-style: none;
  padding-left: 0;
  font-family: 'Arial', Helvetica, sans-serif;
  /* Usar la fuente deseada */
}

.todo-tarea {
  display: flex;
  align-items: center;
  margin-top: 5px;
}

.todo-tarea-details {
  display: flex;
  align-items: center;
  width: 100%;
}

.todo-tarea span {
  font-size: 20px;
  margin-right: 10px;
}

.todo-tarea span:first-child::before {
  content: '*';
  margin-right: 5px;
}

.tarea-estado {
  margin-left: 470px;
  display: flex;
  color: #999;
  font-size: 15px !important;
}

.estado-completo {
  color: green;
}

.estado-pendiente {
  color: blue;
}

.estado-progreso {
  color: orange;
}
</style>
