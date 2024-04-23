<template>
    <div id="add-container">
      <div style="justify-content: flex-end; display: flex;">
        <button @click="openModal" class="btnAgregar">Agregar Proyecto</button>
      </div>
  
      <!-- Ventana modal -->
      <div v-if="showModal" class="modal">
        <div class="modal-content">
          <span @click="closeModal" class="close">&times;</span>
          <h2>Agregar Proyecto</h2>
          <form @submit.prevent="addTodo">
            <div class="input-group">
              <label>Título:</label>
              <input type="text" v-model="title" required />
            </div>
            <div class="input-group">
              <label>Descripción:</label>
              <textarea v-model="description" style="font-family: Arial, Helvetica, sans-serif;" required></textarea>
            </div>
            <div class="button-group">
              <button type="submit" class="btnGuardar">Guardar</button>
              <button type="button" @click="closeModal" class="btnCancelar">Cancelar</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import { uuid } from 'vue-uuid';
  
  export default {
    name: 'TodoAdd',
    data() {
      return {
        title: '',
        description: '',
        showModal: false
      };
    },
    methods: {
      openModal() {
        this.showModal = true;
      },
      closeModal() {
        this.showModal = false;
        this.resetForm();
      },
      resetForm() {
        this.title = '';
        this.description = '';
      },
      addTodo() {
        if (this.title.trim() === '' || this.description.trim() === '') {
          alert('Por favor ingresa un título y una descripción válidos.');
          return;
        }
  
        const newTodo = {
          id: uuid.v4(),
          title: this.title.trim(),
          description: this.description.trim(),
          completed: false
        };
  
        this.$emit('add-todo', newTodo);
        this.closeModal(); // Cerrar la ventana modal después de agregar la actividad
      }
    }
  };
  </script>
  
  <style scoped>
  #add-container {
    padding: 10px;
  }
  
  .btnAgregar {
    padding: 10px;
    background-color: green;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    text-align: left;
    display: block;
  }
  
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
    background-color: #4CAF50; 
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
  </style>
  