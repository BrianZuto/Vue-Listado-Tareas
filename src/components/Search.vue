<template>
    <div id="search-container">
      <div class="search-input-container">
        <i class="fas fa-search search-icon"></i>
        <input type="text" v-model="query" @input="$emit('query-change', query)" placeholder="Buscar Proyecto">
      </div>
    </div>
  </template>

<script>
export default {
    name: 'SearchInput',
    data(){
        return {
            query: ''
        }
    }
}
</script>

<style scoped>
    #search-container{
        width: 600px;
        margin: 0 auto;
    }

    input{
        padding: 10px;
        width: 100%;
        outline: none;
    }

    .search-icon {
        position: absolute;
        left: 297px;
        top: 18px;
        color: #ffffff;
    }

</style>