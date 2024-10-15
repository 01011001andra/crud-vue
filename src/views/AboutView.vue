<script setup>
import MainHeaderVue from '@/components/MainHeader.vue'
import {
  createPost,
  deletePost,
  getPost,
  updatePost,
} from '@/composables/posts.vue'
import { ref, computed, watch } from 'vue'
import { refDebounced } from '@vueuse/core'

const searchInput = ref('')

const pagination = ref({
  page: 1,
  limit: 10,
  search: '', // Mulai dengan string kosong
})
const debounced = refDebounced(searchInput, 1000)

// Watch untuk update nilai `pagination.search` saat `debounced` berubah
watch(debounced, newDebouncedValue => {
  pagination.value.search = newDebouncedValue
})
const { data: postData } = getPost({
  page: computed(() => pagination.value.page),
  limit: computed(() => pagination.value.limit),
  search: computed(() => pagination.value.search),
})

const createMutation = createPost()
const deleteMutation = deletePost()
const updateMutation = updatePost()
const formData = ref({
  id: null,
  title: '',
  views: 0,
})

const handleClose = id => {
  formData.value = { title: '', views: '' }
  document.getElementById(id).close()
}

const handleSubmit = e => {
  e.preventDefault()
  if (createMutation.isPending.value) return
  createMutation
    .mutateAsync(formData.value)
    .then(() => {
      handleClose('my_modal_3')
    })
    .catch(err => {
      console.error(err)
    })
}

const handleUpdate = e => {
  e.preventDefault()
  const bodies = {
    id: formData.value.id,
    title: formData.value.title,
    views: formData.value.views,
  }

  updateMutation
    .mutateAsync(bodies)
    .then(() => {
      handleClose('update_post')
    })
    .catch(err => {
      console.error(err)
    })
  return
}

const handleOpenUpdate = body => {
  formData.value = { id: body.id, title: body.title, views: body.views }
  document.getElementById('update_post').showModal()
}

const handleDelete = id => {
  deleteMutation.mutate(id)
}

const handlePagination = data => {
  if (data === 'prev') {
    pagination.value = {
      ...pagination.value,
      page: postData.value.prev || postData.value.first,
    }
  }

  if (data === 'next') {
    pagination.value = {
      ...pagination.value,
      page: postData.value.next || postData.value.last,
    }
  }
  console.log(pagination.value)
}
</script>

<template>
  <div>
    <MainHeaderVue />
    <div class="w-full min-h-screen flex py-20 justify-center">
      <div class="flex flex-col items-end gap-4 w-full max-w-xl">
        <div class="flex justify-between w-full">
          <input
            type="text"
            placeholder="Search..."
            class="input input-bordered w-full max-w-xs"
            v-model="searchInput"
          />
          <button class="btn btn-primary" onclick="my_modal_3.showModal()">
            ADD MORE
          </button>
        </div>

        <div class="overflow-x-auto mx-auto border w-full">
          <table class="table table-zebra">
            <!-- head -->
            <thead>
              <tr>
                <th class="border-r">No</th>
                <th class="border-r">Title</th>
                <th class="border-r">Views</th>
                <th>Action</th>
              </tr>
            </thead>
            <tbody>
              <!-- row 1 -->
              <tr v-for="(item, index) in postData?.data" :key="index">
                <th class="border-r">
                  {{ (pagination.page - 1) * pagination.limit + 1 + index }}
                </th>
                <td class="border-r">{{ item.title }}</td>
                <td class="border-r">{{ item.views }}</td>
                <td class="w-44 p-1">
                  <div class="flex gap-2 max-w-xs w-full">
                    <button
                      class="btn btn-error p-2 rounded-lg text-white"
                      @click="handleDelete(item.id)"
                    >
                      Delete
                    </button>
                    <button
                      class="btn btn-warning p-2 rounded-lg text-white"
                      @click="
                        e => {
                          handleOpenUpdate(item)
                        }
                      "
                    >
                      Edit
                    </button>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="join">
          <button class="join-item btn" @click="handlePagination('prev')">
            «
          </button>
          <button class="join-item btn">Page {{ pagination.page }}</button>
          <button class="join-item btn" @click="handlePagination('next')">
            »
          </button>
        </div>
      </div>
      <dialog id="my_modal_3" class="modal">
        <div class="modal-box">
          <button
            onclick="my_modal_3.close()"
            class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2"
          >
            ✕
          </button>
          <h3 class="text-lg font-bold">Add Data</h3>
          <form
            @submit="
              e => {
                handleSubmit(e)
              }
            "
          >
            <label class="form-control w-full mt-2">
              <div class="label">
                <span class="label-text whitespace-nowrap">Title: </span>
              </div>
              <input
                type="text"
                placeholder="Type here"
                class="input input-bordered w-full"
                v-model="formData.title"
              />
            </label>
            <label class="form-control w-full mt-2">
              <div class="label">
                <span class="label-text whitespace-nowrap">Views: </span>
              </div>
              <input
                type="number"
                placeholder="Type here"
                class="input input-bordered w-full"
                v-model="formData.views"
              />
            </label>
            <div class="flex items-end justify-end mt-4">
              <button
                class="btn btn-primary"
                type="submit"
                :disabled="createMutation.isPending.value"
              >
                Submit
              </button>
            </div>
          </form>
        </div>
      </dialog>
      <dialog id="update_post" class="modal">
        <div class="modal-box">
          <button
            @click="handleClose('update_post')"
            class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2"
          >
            ✕
          </button>
          <h3 class="text-lg font-bold">Update Data</h3>
          <form :onsubmit="handleUpdate">
            <label class="form-control w-full mt-2">
              <div class="label">
                <span class="label-text whitespace-nowrap">Title: </span>
              </div>
              <input
                type="text"
                placeholder="Type here"
                class="input input-bordered w-full"
                v-model="formData.title"
              />
            </label>
            <label class="form-control w-full mt-2">
              <div class="label">
                <span class="label-text whitespace-nowrap">Views: </span>
              </div>
              <input
                type="number"
                placeholder="Type here"
                class="input input-bordered w-full"
                v-model="formData.views"
              />
            </label>
            <div class="flex items-end justify-end mt-4">
              <button
                class="btn btn-primary"
                type="submit"
                :disabled="updateMutation.isPending.value"
              >
                Update
              </button>
            </div>
          </form>
        </div>
      </dialog>
    </div>
  </div>
</template>
