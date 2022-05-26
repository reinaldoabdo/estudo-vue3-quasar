<template>
  <q-page padding>
    <q-table title="Treats" :rows="posts" :columns="columns" row-key="name">
      <template v-slot:body-cell-actions="props">
        <q-td :props="props" class="q-gutter-sm">
          <q-btn icon="edit" dense size="sm" @click="handleEditPost(props.row.id)"></q-btn>
          <q-btn icon="delete" dense size="sm" @click="handleDeletePost(props.row.id)"></q-btn>
        </q-td>
      </template>
      <template v-slot:top>
        <span class="text-h5">Artigos</span>
        <q-space></q-space>
        <q-btn color="primary" label="Novo" :to="{name:'formPost'}" />
      </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref([])
    const { list, remove } = postsService()
    const router = useRouter()
    const columns = [
      { name: 'id', label: 'Id', field: 'id', sortable: true },
      { name: 'title', label: 'Título', field: 'title', sortable: true },
      { name: 'author', label: 'Autor', field: 'author', sortable: true },
      { name: 'actions', label: 'Ações', field: 'actions', sortable: true, align: 'right' }
    ]

    const $q = useQuasar()

    onMounted(() => {
      getPosts()
    })

    const getPosts = async () => {
      try {
        const data = await list()
        posts.value = data
      } catch (error) {
        console.log(error)
      }
    }

    const handleDeletePost = async (id) => {
      try {
        $q.dialog({
          title: 'Confirmar',
          message: 'Deseja realmente excluír?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(id)
          $q.notify({ message: 'Apagado com sucesso', icon: 'check', color: 'positive' })
          await getPosts()
        })
      } catch (error) {
        console.log(error)
      }
    }

    const handleEditPost = async (id) => {
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
