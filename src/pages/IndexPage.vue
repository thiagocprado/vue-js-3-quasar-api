<template>
  <q-page padding>
    <q-table title="Treats" :rows="posts" :columns="columns" row-key="name">
      <template v-slot:body-cell-actions="props">
        <q-td :props="props">
          <q-btn
            @click="handleDeletePost(props.row.id)"
            icon="delete"
            color="negative"
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
import postsService from 'src/services/posts'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref([])
    const { list, remove } = postsService()
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

    return {
      posts,
      columns,
      handleDeletePost
    }
  }
})
</script>
