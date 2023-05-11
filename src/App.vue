<template>
  <div>
    <form class="user-search-form" @submit.prevent="handleSearch">
      <input
        type="text"
        v-model="searchTerm"
        placeholder="Buscar por nome ou localização"
      />
      <button type="submit">Buscar</button>
    </form>

    <ul class="user-search-list" v-if="users.length > 0">
      <li v-for="user in users" :key="user.login.uuid">
        <strong>{{ user.name.first }} {{ user.name.last }}</strong>
        <span>{{ user.location.city }}, {{ user.location.country }}</span>
      </li>
    </ul>

    <p v-else-if="loading">Carregando...</p>

    <p v-else>Nenhum usuário encontrado.</p>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      searchTerm: "",
      users: [],
      loading: false,
    };
  },
  methods: {
    async handleSearch() {
      this.loading = true;

      try {
        const response = await axios.get("https://randomuser.me/api", {
          params: {
            results: 1000,
            nat: "br,us,mx,gb,nz,nl,ca",
          },
        });

        const allUsers = response.data.results;

        // Filtro de usuários por nome ou localização
        const filteredUsers = allUsers.filter((user) => {
          const name = `${user.name.first} ${user.name.last}`.toLowerCase();
          const location = `${user.location.city}, ${user.location.country}`.toLowerCase();
          const searchTerm = this.searchTerm.toLowerCase();
          return name.includes(searchTerm) || location.includes(searchTerm);
        });

        this.users = filteredUsers;
      } catch (error) {
        console.error("Ocorreu um erro na requisição:", error);
      }

      this.loading = false;
    },
  },
  mounted() {
    this.handleSearch(); // Realiza a busca inicial ao carregar o componente
  },
};
</script>

<style>
div {
  font-family: sans-serif;
}
.user-search-form {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 20px;
}

.user-search-form input[type="text"] {
  padding: 10px;
  border: none;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  font-size: 16px;
  flex: 1;
}

.user-search-form button {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s ease;
}

.user-search-form button:hover {
  background-color: #0056b3;
}

.user-search-list strong {
  margin-right: 10px;
}
</style>