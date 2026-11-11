<script setup>
import { ref, computed } from 'vue'

// Упражнение 1
const grade = ref(0)

// Упражнение 2
const newItem = ref('')
const items = ref([])

const itemsCount = computed(() => items.value.length)

function addItem() {
  const name = newItem.value.trim()

  if (!name) return

  items.value.push(name)
  newItem.value = ''
}

function removeItem(index) {
  items.value.splice(index, 1)
}

// Упражнение 3
const searchQuery = ref('')
const selectedCategory = ref('Все')

const courses = ref([
  { id: 1, name: 'Основы JavaScript', category: 'Программирование' },
  { id: 2, name: 'Vue.js для начинающих', category: 'Программирование' },
  { id: 3, name: 'Основы UI/UX', category: 'Дизайн' },
  { id: 4, name: 'Figma с нуля', category: 'Дизайн' },
  { id: 5, name: 'Python для анализа данных', category: 'Данные' },
  { id: 6, name: 'SQL и базы данных', category: 'Данные' },
])

const filteredCourses = computed(() => {
  return courses.value.filter(course => {
    const matchesSearch = course.name
      .toLowerCase()
      .includes(searchQuery.value.toLowerCase())

    const matchesCategory =
      selectedCategory.value === 'Все' ||
      course.category === selectedCategory.value

    return matchesSearch && matchesCategory
  })
})

// Упражнение 4
const students = ref([
  { id: 1, name: 'Иван', grade: 85 },
  { id: 2, name: 'Мария', grade: 95 },
  { id: 3, name: 'Алексей', grade: 55 },
  { id: 4, name: 'Анна', grade: 72 },
  { id: 5, name: 'Дмитрий', grade: 40 },
])

const maxGrade = computed(() => {
  if (students.value.length === 0) return '—'

  return Math.max(...students.value.map(student => student.grade))
})

const minGrade = computed(() => {
  if (students.value.length === 0) return '—'

  return Math.min(...students.value.map(student => student.grade))
})

const averageGrade = computed(() => {
  if (students.value.length === 0) return 0

  const sum = students.value.reduce(
    (total, student) => total + student.grade,
    0
  )

  return (sum / students.value.length).toFixed(1)
})

const passedCount = computed(() => {
  return students.value.filter(student => student.grade >= 60).length
})

function changeGrade(student, amount) {
  student.grade = Math.max(0, Math.min(100, student.grade + amount))
}

function removeStudent(id) {
  students.value = students.value.filter(
    student => student.id !== id
  )
}
</script>

<template>
  <main class="page-container">
    <!-- Упражнение 1 -->
    <section class="container">
      <h2>Упражнение 1. Оценка словами</h2>
      <label for="grade">Введите оценку:</label>
      <input
        id="grade"
        type="number"
        v-model.number="grade"
        min="0"
        max="100"
      />

      <p v-if="grade < 0 || grade > 100">
        Ошибка: число от 0 до 100
      </p>
      <p v-else-if="grade >= 90">
        Отлично
      </p>
      <p v-else-if="grade >= 75">
        Хорошо
      </p>
      <p v-else-if="grade >= 60">
        Удовлетворительно
      </p>
      <p v-else>
        Не сдал
      </p>
    </section>

    <!-- Упражнение 2 -->
    <section class="container">
      <h2>Упражнение 2. Список покупок</h2>

      <div class="input-group">
        <input
          v-model="newItem"
          type="text"
          placeholder="Введите название товара"
          @keyup.enter="addItem"
        />
        <button @click="addItem">Добавить</button>
      </div>

      <p class="count">
        Количество элементов: {{ itemsCount }}
      </p>

      <p v-if="items.length === 0" class="empty">
        Список пуст
      </p>

      <ul v-else>
        <li v-for="(item, index) in items" :key="index">
          <span>{{ item }}</span>
          <button class="delete-btn" @click="removeItem(index)">
            Удалить
          </button>
        </li>
      </ul>
    </section>

    <!-- Упражнение 3 -->
    <section class="container">
      <h2>Упражнение 3. Каталог курсов</h2>

      <div class="filters">
        <input
          v-model="searchQuery"
          type="text"
          placeholder="Поиск по названию..."
        />

        <select v-model="selectedCategory">
          <option value="Все">Все категории</option>
          <option value="Программирование">Программирование</option>
          <option value="Дизайн">Дизайн</option>
          <option value="Данные">Данные</option>
        </select>
      </div>

      <p class="count">
        Найдено курсов: {{ filteredCourses.length }}
      </p>

      <ul v-if="filteredCourses.length > 0">
        <li v-for="course in filteredCourses" :key="course.id">
          <div class="course-info">
            <strong>{{ course.name }}</strong>
            <span>{{ course.category }}</span>
          </div>
        </li>
      </ul>

      <p v-else class="empty">
        Курсы не найдены
      </p>
    </section>

    <!-- Упражнение 4 -->
    <section class="container">
      <h2>Упражнение4. Статистика оценок</h2>

      <div class="stats">
        <div class="stat">
          <span>Максимум</span>
          <strong>{{ maxGrade }}</strong>
        </div>

        <div class="stat">
          <span>Минимум</span>
          <strong>{{ minGrade }}</strong>
        </div>

        <div class="stat">
          <span>Средняя</span>
          <strong>{{ averageGrade }}</strong>
        </div>

        <div class="stat">
          <span>Сдали</span>
          <strong>{{ passedCount }} / {{ students.length }}</strong>
        </div>
      </div>

      <h3>Студенты</h3>

      <p v-if="students.length === 0" class="empty">
        Список студентов пуст
      </p>

      <ul v-else>
        <li v-for="student in students" :key="student.id">
          <div class="student-info">
            <strong>{{ student.name }}</strong>
            <span :class="student.grade >= 60 ? 'passed' : 'failed'">
              {{ student.grade }} баллов
            </span>
          </div>

          <div class="actions">
            <button @click="changeGrade(student, -5)">-5</button>
            <button @click="changeGrade(student, 5)">+5</button>
            <button
              class="delete-btn"
              @click="removeStudent(student.id)"
            >
              Удалить
            </button>
          </div>
        </li>
      </ul>
    </section>
  </main>
</template>

<style scoped>
  label {
    font-size: 20px;
  }

  p {
    font-size: 20px;
  }

  .page-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 30px;
  width: 100%;
  padding: 30px 20px;
  box-sizing: border-box;
}

.container {
  width: 100%;
  max-width: 900px;
  padding: 30px;
  box-sizing: border-box;
  font-family: Arial, sans-serif;
  font-size: 20px;
}

  h2 {
    margin-bottom: 20px;
    font-size: 30px;
  }

  .input-group {
    display: flex;
    gap: 10px;
  }

  input, select {
    flex: 1;
    min-width: 0;
    padding: 12px;
    font-size: 18px;
    border: 2px solid #ccc;
    border-radius: 8px;
  }

  button {
    padding: 12px 18px;
    font-size: 18px;
    cursor: pointer;
    border: none;
    border-radius: 8px;
    background: #2563eb;
    color: white;
  }

  .count {
    margin: 20px 0;
    font-weight: bold;
  }

  .empty {
    color: #777;
    font-style: italic;
  }

  ul {
    padding: 0;
    list-style: none;
  }

  li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 12px;
    padding: 12px;
    margin-bottom: 10px;
    background: #f3f4f6;
    border-radius: 8px;
  }

  .delete-btn {
    background: #dc2626;
  }

  li span {
    color: #2563eb;
    font-size: 16px;
  }

  .stats {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 12px;
  }

  .stat {
    display: flex;
    flex-direction: column;
    gap: 10px;
    padding: 18px;
    background: #f3f4f6;
    border-radius: 10px;
  }

  .stat span {
    color: #666;
    font-size: 16px;
  }

  .stat strong {
    font-size: 28px;
  }

  .actions {
    display: flex;
    gap: 8px;
  }

  .delete-btn {
    background: #dc2626;
  }

  .student-info {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  .actions {
    display: flex;
    flex-direction: row;
    gap: 8px;
    align-items: center;
  }

  .course-info {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }
</style>



