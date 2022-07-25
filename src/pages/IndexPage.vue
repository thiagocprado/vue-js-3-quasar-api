<template>
  <q-page padding>
    <q-table :rows="posts" :columns="columns" row-key="name">
      <template v-slot:top>
        <span class="text-h5">Artigos</span>
        <q-space />
        <q-btn color="primary" label="Novo artigo" :to="{ name: 'formPost' }" />
      </template>
      <template v-slot:body-cell-actions="props">
        <q-td :props="props" class="q-gutter-sm">
          <q-btn
            @click="handleDeletePost(props.row.id)"
            icon="delete"
            color="negative"
            dense
            size="sm"
          />
          <q-btn
            @click="handleEditPost(props.row.id)"
            icon="edit"
            color="info"
            dense
            size="sm"
          />
        </q-td>
      </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'
import postsService from 'src/services/posts'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const { list, remove } = postsService()
    const router = useRouter()
    const posts = ref([])
    const columns = [
      { name: 'id', field: 'id', label: 'Id', sortable: true, align: 'left' },
      { name: 'title', field: 'title', label: 'Título', sortable: true, align: 'left' },
      { name: 'author', field: 'author', label: 'Autor', sortable: true, align: 'left' },
      { name: 'actions', field: 'actions', label: 'Ações', sortable: true, align: 'right' }

    ]

    onMounted(() => {
      getPosts()
    })

    const $q = useQuasar()

    const getPosts = async () => {
      try {
        const data = await list()
        posts.value = data
      } catch (error) {
        console.error(error)
      }
    }

    const handleDeletePost = (id) => {
      try {
        $q.dialog({
          title: 'Apagar',
          message: 'Deseja apagar esse post?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(id)
          $q.notify({ message: 'Apagado com sucesso', icon: 'check', color: 'positive' })
          await getPosts()
        })
      } catch (error) {
        $q.notify({ message: 'Erro ao apagar', icon: 'times', color: 'negative' })
      }
    }

    const handleEditPost = (id) => {
      router.push({ name: 'formPost', params: { id } })
    }

    return {
      posts,
      columns,
      handleDeletePost,
      handleEditPost
    }
  }
})
</script>
