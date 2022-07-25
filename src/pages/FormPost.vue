<template>
  <q-page padding>
    <q-form @submit="onSubmit" class="row q-col-gutter-sm">
      <q-input
        outlined
        v-model="form.title"
        label="Título"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[(val) => (val && val.length > 0) || 'Campo obrigatório']"
      />

      <q-input
        outlined
        v-model="form.author"
        label="Autor"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[(val) => (val && val.length > 0) || 'Campo obrigatório']"
      />

      <div class="col-lg-12 col-xs-12">
        <q-editor v-model="form.content" min-height="5rem" />
      </div>

      <div class="col-12 q-gutter-md">
        <q-btn
          color="primary"
          label="salvar"
          class="float-right"
          icon="save"
          type="submit"
        />
        <q-btn
          color="white"
          label="cancelar"
          class="float-right"
          text-color="primary"
          :to="{ name: 'home' }"
        />
      </div>
    </q-form>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import { useQuasar } from 'quasar'
import { useRouter, useRoute } from 'vue-router'
import postsService from 'src/services/posts'

export default defineComponent({
  name: 'FormPost',
  setup () {
    const { post, getById, update } = postsService()
    const $q = useQuasar()
    const router = useRouter()
    const route = useRoute()
    const form = ref({
      title: '',
      content: '',
      author: ''
    })

    onMounted(() => {
      if (route.params.id) {
        getPost(route.params.id)
      }
    })

    const getPost = async (id) => {
      try {
        const response = await getById(id)
        form.value = response
      } catch (error) {
        console.error(error)
      }
    }

    const onSubmit = async () => {
      try {
        if (form.value.id) {
          await update(form.value)
          $q.notify({ message: 'Atualizado com sucesso', icon: 'check', color: 'positive' })
          router.push({ name: 'home' })
        } else {
          await post(form.value)
          $q.notify({ message: 'Criado com sucesso', icon: 'check', color: 'positive' })
          router.push({ name: 'home' })
        }
      } catch (error) {
        console.error(error)
      }
    }

    return {
      form,
      onSubmit
    }
  }
})
</script>
