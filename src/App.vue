<script setup>
import { ref, computed, onMounted } from 'vue';

const tarefas = ref([]);
const novaTarefa = ref("");
const filtro = ref('todas');

onMounted(() => {
  const tarefasSalvas = localStorage.getItem("tarefas");
  if (tarefasSalvas) {
    tarefas.value = JSON.parse(tarefasSalvas).map(tarefa => ({
      id: tarefa.id || Date.now(), // Garante um ID válido
      titulo: tarefa.titulo || "Tarefa sem título", // Evita tarefas sem nome
      concluida: tarefa.concluida ?? false // Garante que 'concluida' seja booleano
    }));
  }
});

// Função para adicionar tarefa
const adicionarTarefa = () => {
  if (novaTarefa.value.trim() !== "") {
    tarefas.value.push({
      id: Date.now(),
      titulo: novaTarefa.value, // 🔹 Garante que o título é armazenado corretamente
      concluida: false
    });
    novaTarefa.value = ""; // 🔹 Limpa o campo após adicionar a tarefa
    salvarTarefas();
  }
};

// Função para remover tarefa
const removerTarefa = (id) => {
  tarefas.value = tarefas.value.filter(tarefa => tarefa.id !== id);
  salvarTarefas();
};

// Função para alternar o status de concluída
const alternarConclusao = (id) => {
  const tarefa = tarefas.value.find(tarefa => tarefa.id === id);
  if (tarefa) {
    tarefa.concluida = !tarefa.concluida;
    salvarTarefas(); // 🔹 Salva as tarefas no LocalStorage
  }
};

// Função para filtrar tarefas
const filtrarTarefas = (tipo) => {
  filtro.value = tipo;
};

const salvarTarefas = () => {
  localStorage.setItem("tarefas", JSON.stringify(tarefas.value));
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
      <li v-for="tarefa in tarefasFiltradas" :key="tarefa.id">
        <span 
          :class="{ concluida: tarefa.concluida }" 
          @click="alternarConclusao(tarefa.id)">
          {{ tarefa.titulo }}  <!-- 🔹 Agora exibe o nome correto -->
        </span>
        <button @click="removerTarefa(tarefa.id)">❌</button> <!-- 🔹 Agora remove corretamente -->
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
