<template>
  <div class="modal fade" id="book_modal" tabindex="-1" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel"> {{ `${is_create ? 'Crear' : 'Actualizar'} Libro` }} </h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <form @submit.prevent="storeBook" enctype="multipart/form-data">
            <div class="mb-3">
              <label for="images" class="form-label">Imagen</label>
              <input type="file" class="form-control" id="file" accept="imgae/*" @change="loadImage">
            </div>

            <div class="mb-3">
              <label for="title" class="form-label">Titulo</label>
              <input type="text" class="form-control" id="title" v-model="book.title">
            </div>

            <div class="mb-3">
              <label for="stock" class="form-label">Stock</label>
              <input type="number" class="form-control" id="title" v-model="book.stock">
            </div>
            <div class="mb-3">
              <label for="description" class="form-label">Descripcion</label>
              <textarea class="form-control" id="description" rows="3" v-model="book.description"></textarea>
            </div>

            <div class="mb-3">
              <label for="category" class="form-label">Categorias</label>
              <v-select id="category" :options="categories" v-model="book.category_id" :reduce="category => category.id"
                label="name" :clearble="false">
              </v-select>
            </div>


            <div class="mb-3">
              <label for="author" class="form-label">Autor</label>
              <v-select id="author" :options="authors" v-model="book.author_id" :reduce="author => author.id"
                label="name" :clearble="false">
              </v-select>
            </div>

            <hr>
            <section class="d-flex justify-content-end mt-3">
              <button type="button" class="btn btn-secondary me-1" data-bs-dismiss="modal">Cerrar</button>
              <button type="submit" class="btn btn-primary me-1"> {{ `${is_create ? 'Crear' : 'Actualizar'} ` }}
              </button>
            </section>
          </form>

        </div>
        <div class="modal-footer">

        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['book_data'],
  data() {
    return {
      is_create: true,
      categories: [],
      authors: [],
      book: {},
      file: null
    }
  },
  created() {
    this.index()
  },
  methods: {
    index() {
      this.getCategories()
      this.getAuthors()
      this.setBook()
    },

    setBook(){
      if (!this.book_data) return
      this.book = { ...this.book_data }
      this.is_create = false
    },

    loadImage(event){
      this.file =event.target.files[0]
    },

    loadFormData(){
      const form_data = new FormData()
      if(this.file) form_data.append('image', this.file , this.file.name)
      form_data.append('title', this.book.title);
      form_data.append('stock', this.book.stock);
      form_data.append('description', this.book.description);
      form_data.append('category_id', this.book.category_id);
      form_data.append('author_id', this.book.author_id);
      return form_data;
    },

    async getCategories() {
      const { data } = await axios.get('Categories/GetAllCategories')
      this.categories = data.categories
    },

    async getAuthors() {
      const { data } = await axios.get('Authors/GetAllAuthors')
      this.authors = data.authors
    },

    async storeBook() {
      try {
        const book = this.loadFormData()
        if (this.is_create) {
          await axios.post('Books/SaveBook', book)
        } else {
          await axios.post(`Books/UpdateBook/${this.book.id}`, book)
        }
        swal.fire({
          icon: 'success',
          title: 'Grandiso!',
          text: 'Libro almacenado!',
        })
        this.$parent.closeModal()  //Llamar al papá con el $parent
      } catch (error) {
        console.error(error)
        swal.fire({
          icon: 'error',
          title: 'Oops...',
          text: 'Algo salio mal!',
        })
      }
    }
  }
}
</script>

<style>

</style>