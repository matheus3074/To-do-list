<script setup>
import { ref, computed } from 'vue';

const tarefas = ref([]);
const novaTarefa = ref('');
const filtro = ref('todas');

// Função para adicionar tarefa
const adicionarTarefa = () => {
  if (novaTarefa.value.trim() !== '') {
    tarefas.value.push({ texto: novaTarefa.value, concluida: false });
    novaTarefa.value = ''; // Limpa o campo após adicionar
  }
};

// Função para remover tarefa
const removerTarefa = (index) => {
  tarefas.value.splice(index, 1);
};

// Função para alternar o status de concluída
const toggleConcluida = (index) => {
  tarefas.value[index].concluida = !tarefas.value[index].concluida;
};

// Função para filtrar tarefas
const filtrarTarefas = (tipo) => {
  filtro.value = tipo;
};

// Computed para pegar as tarefas filtradas
const tarefasFiltradas = computed(() => {
  if (filtro.value === 'pendentes') {
    return tarefas.value.filter(tarefa => !tarefa.concluida);
  } else if (filtro.value === 'concluidas') {
    return tarefas.value.filter(tarefa => tarefa.concluida);
 } else {
    return tarefas.value; // Todas as tarefas
  }
});
</script>

<template>
    <div class="container">
    <h1>Lista de Tarefas</h1>
    
    <input v-model="novaTarefa" type="text" placeholder="Adicione uma tarefa" @keyup.enter="adicionarTarefa" />
    <button @click="adicionarTarefa">Adicionar</button>

    <!-- Filtros -->
    <button @click="filtrarTarefas('todas')">Todas</button>
    <button @click="filtrarTarefas('pendentes')">Pendentes</button>
    <button @click="filtrarTarefas('concluidas')">Concluídas</button>
    
    <ul>
      <li v-for="(tarefa, index) in tarefasFiltradas" :key="index">
        <span 
          :class="{ concluida: tarefa.concluida }" 
          @click="toggleConcluida(index)">
          {{ tarefa.texto }}
        </span>
        <button @click="removerTarefa(index)">❌</button>
      </li>
    </ul>
  </div>
</template>

<style scoped>
  .container {
    max-width: 400px;
    margin: 0 auto;
    text-align: center;
  }

  input {
    padding: 8px;
    margin-right: 5px;
  }

  button {
    padding: 8px;
    cursor: pointer;
  }

  span.concluida {
    text-decoration: line-through;
    color: gray;
  }
</style>
