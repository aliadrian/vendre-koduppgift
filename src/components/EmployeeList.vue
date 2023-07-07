<template>
  <div>
    <div v-if="employees && employees.length" class="cards">
      <div v-for="employee in employees" :key="employee.id" class="employee-card">
        <img :src="employee.avatar" class="profile-picture" />
        <div class="employee-info">
          <h2>{{ employee.first_name }} {{ employee.last_name }}</h2>
          <a :href="`mailto:${employee.email}`">Contact</a>
        </div>
      </div>
    </div>
    <div v-else>Loading...</div>
    <div class="buttons">
      <button :disabled="currentPage === 1" @click="goToPage(1)">1</button>
      <button :disabled="currentPage === 2" @click="goToPage(2)">2</button>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, watch } from 'vue'
import axios from 'axios'

export default {
  setup() {
    const employees = ref([])
    const currentPage = ref(1)
    const perPage = 6
    let totalPages = ref(1)

    const fetchEmployees = async () => {
      try {
        const response = await axios.get(`https://reqres.in/api/users?page=${currentPage.value}`)
        employees.value = response.data.data
        totalPages.value = response.data.total_pages
      } catch (error) {
        console.error('Error fetching employees:', error)
      }
    }

    const goToPage = (page) => {
      currentPage.value = page
      fetchEmployees()
    }

    onMounted(fetchEmployees)
    watch(currentPage, fetchEmployees)

    return {
      employees,
      currentPage,
      totalPages,
      goToPage
    }
  }
}
</script>

<style>
:root {
  --cardWidth: 13rem;
  --cardHeight: 14rem;
}

.cards {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 1rem;
}

.employee-card {
  width: var(--cardWidth);
  height: var(--cardHeight);
  border-radius: 0.5rem;
  background: #eeeeee;
  display: flex;
  align-items: center;
  flex-direction: column;
  justify-content: center;
}

.profile-picture {
  width: 6.25rem;
  height: 6.25rem;
  border-radius: 50%;
}

.employee-info {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 1rem;
  gap: 1rem;
}

.employee-info h2 {
  font-size: 0.95rem;
  font-weight: 700;
  letter-spacing: 0.001em;
}

a {
  color: #5c5c5c;
  text-decoration: none;
  letter-spacing: 0.001em;
}

.buttons {
  display: flex;
  justify-content: center;
  gap: 0.25rem;
  transform: translateY(2vh);
}
</style>
