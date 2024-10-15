<script setup>
import { onMounted, ref } from 'vue'
import MainHeader from '../components/MainHeader.vue'
import MainFooter from '../components/MainFooter.vue'

const count = ref(0)

const formData = ref({
  index: null,
  no: '',
  name: '',
  skill: '',
})
const isEdit = ref(false)
const developerList = ref([
  { no: Math.random(), name: 'Yandra Muslim', skill: 'Javascript' },
  { no: Math.random(), name: 'Muslim Yandra', skill: 'Golang' },
  { no: Math.random(), name: 'Musyan', skill: 'PHP' },
])
const handleClick = () => {
  if (!formData.value.name || !formData.value.skill) return
  if (isEdit.value) {
    developerList.value[formData.value.index] = {
      no: formData.value.no,
      name: formData.value.name,
      skill: formData.value.skill,
    }
    hanldleCancel()
  } else {
    developerList.value = [
      ...developerList.value,
      {
        no: Math.random(),
        name: formData.value.name,
        skill: formData.value.skill,
      },
    ]
    formData.value = {
      name: '',
      skill: '',
    }
  }
}
const hanldleCancel = () => {
  isEdit.value = false
  formData.value = {
    name: '',
    skill: '',
  }
}
const handleDelete = no => {
  const filteredData = developerList.value.filter(item => item.no !== no)
  developerList.value = filteredData
}

const handleEditMode = (programmer, index) => {
  isEdit.value = true
  formData.value = {
    index: index,
    name: programmer.name,
    no: programmer,
    skill: programmer.skill,
  }
}

onMounted(() => {
  count.value = 10
})
</script>

<template>
  <main>
    <MainHeader />
    <div
      class="max-w-7xl mx-auto w-full min-h-screen flex flex-col justify-between items-center gap-8"
    >
      <div
        class="flex flex-col flex-1 w-full gap-8 h-full items-center justify-center"
      >
        <div class="flex gap-8 items-center justify-center">
          <button
            @click="count--"
            class="bg-red-600 text-white p-2 rounded-lg px-8"
          >
            -
          </button>
          <h1>{{ count }}</h1>
          <button
            @click="count++"
            class="bg-green-600 text-white p-2 rounded-lg px-8"
          >
            +
          </button>
        </div>

        <div class="flex gap-2">
          <label for="name">Name</label>
          <input
            type="text"
            id="name"
            name="name"
            v-model="formData.name"
            class="border"
          />
        </div>
        <div class="flex gap-2">
          <label for="skill">skill</label>
          <input
            type="text"
            id="skill"
            name="skill"
            v-model="formData.skill"
            class="border"
          />
        </div>
        <div class="flex gap-2 flex-row-reverse">
          <button
            :class="{
              'bg-gray-600 p-2 text-white rounded-lg': !isEdit,
              'bg-blue-600 p-2 text-white rounded-lg': isEdit,
            }"
            @click="handleClick"
          >
            {{ isEdit ? 'Edit' : 'Submit' }}
          </button>
          <button
            v-if="isEdit"
            @click="hanldleCancel"
            :class="{ 'bg-red-600 text-white p-2 rounded-lg': isEdit }"
          >
            Cancel
          </button>
        </div>
        <table class="border-collapse w-full max-w-lg">
          <thead>
            <tr class="border-b bg-gray-200">
              <th
                class="border border-r-gray-300 px-4 py-2 text-left text-black"
              >
                No
              </th>
              <th
                class="border border-r-gray-300 px-4 py-2 text-left text-black"
              >
                Name
              </th>
              <th
                class="border border-r-gray-300 px-4 py-2 text-left text-black"
              >
                Skill
              </th>
              <th
                class="border border-r-gray-300 px-4 py-2 text-left text-black"
              >
                Action
              </th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(programmer, index) in developerList"
              :key="programmer.no"
              class="border-b"
            >
              <td class="border px-4 py-2">{{ index + 1 }}</td>
              <td class="border px-4 py-2">{{ programmer.name }}</td>
              <td class="border px-4 py-2">{{ programmer.skill }}</td>
              <td class="border px-4 space-x-2">
                <button
                  class="bg-red-600 text-white p-2 text-xs rounded-lg"
                  @click="handleDelete(programmer.no)"
                >
                  Delete
                </button>
                <button
                  class="bg-green-600 text-white p-2 text-xs rounded-lg"
                  @click="handleEditMode(programmer, index)"
                >
                  Edit
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <MainFooter />
    </div>
  </main>
</template>
