<script>
import { useMutation, useQuery, useQueryClient } from '@tanstack/vue-query'
import axios from 'axios'
import { computed } from 'vue'

// axios
const getData = async (page, limit, search) => {
  const data = await axios.get(
    `http://localhost:3001/posts?_page=${page.value}&_per_page=${limit.value}&_sort=-views&title=${search.value}`,
  )
  return data.data
}
const createData = body => {
  return axios.post('http://localhost:3001/posts', body)
}
const deleteData = async id => {
  await axios.delete(`http://localhost:3001/posts/${id}`)
}

const updateData = async body => {
  return await axios.put(`http://localhost:3001/posts/${body.id}`, body)
}

// takstack query
export const createPost = () => {
  const queryClient = useQueryClient()

  const mutation = useMutation({
    mutationFn: body => {
      return createData(body)
    },
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['posts'] })
    },
  })

  return mutation
}

export const getPost = ({ page, limit, search }) => {
  const query = useQuery({
    queryKey: computed(() => ['posts', page.value, search.value]),
    queryFn: () => {
      return getData(page, limit, search)
    },
  })
  return query
}

export const deletePost = () => {
  const queryClient = useQueryClient()

  const mutation = useMutation({
    mutationFn: id => {
      return deleteData(id)
    },
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['posts'] })
    },
  })

  return mutation
}

export const updatePost = () => {
  const queryClient = useQueryClient()

  const mutation = useMutation({
    mutationFn: body => {
      return updateData(body)
    },
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['posts'] })
    },
  })

  return mutation
}
</script>
