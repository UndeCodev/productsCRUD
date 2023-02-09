<template>
  <main class="container-fluid bg-vue py-3 d-flex justify-content-center align-items-center flex-wrap gap-sm-2 gap-lg-5" style="min-height: 100vh;">
    <section class="col-sm-11 col-lg-3 product__add d-flex justify-content-center flex-wrap">
      <div class="d-flex align-items-center w-100">
        <h1 class="fw-light text-white text-shadow text-center mb-3 w-100">{{ (!this.editProduct ? 'Insert ' : 'Edit ') + 'Product' }}</h1>
        <a class="addProduct" v-if="this.editProduct" @click="resetData">
          <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-new-section" width="44" height="44" viewBox="0 0 24 24" stroke-width="1.5" stroke="#ffffff" fill="none" stroke-linecap="round" stroke-linejoin="round">
            <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
            <line x1="9" y1="12" x2="15" y2="12" />
            <line x1="12" y1="9" x2="12" y2="15" />
            <path d="M4 6v-1a1 1 0 0 1 1 -1h1m5 0h2m5 0h1a1 1 0 0 1 1 1v1m0 5v2m0 5v1a1 1 0 0 1 -1 1h-1m-5 0h-2m-5 0h-1a1 1 0 0 1 -1 -1v-1m0 -5v-2m0 -5" />
          </svg>
        </a>
      </div>
      <form class="col-sm-6 col-lg-12 bg-white rounded-1 shadow-sm p-3" name="idForm" ref="form">
        <div class="d-flex justify-content-center flex-wrap" v-if="imageURL">
          <label for="" class="fw-bold w-100 text-center">Image selected:</label>
          <img :src="imageURL" alt="Image" class="w-50">
        </div>
        <div class="mb-2">
          <label for="selectImage">Select image:</label>
          <input type="file" class="form-control shadow-none" name="image" id="image" @change="renderImage">
        </div>
        <div class="mb-2">
          <label for="name">Name:</label>
          <input type="text" v-model="this.formData.product" placeholder="Name" class="form-control shadow-none" name="name" id="name">
        </div>
        <div class="mb-2">
          <label for="description">Description:</label>
          <textarea v-model="this.formData.productDescription" name="description" id="description" cols="20" rows="3"  class="form-control shadow-none" placeholder="Description"></textarea>
        </div>
        <div class="mb-3">
          <label for="price">Price:</label>
          <input type="number" v-model="this.formData.productPrice" placeholder="Price" class="form-control shadow-none" name="price" id="price" min="1">
        </div>
        <div class="text-end">
          <button
            type="submit"
            class="btn btn-success btn-sm rounded-1"
            @click.prevent="createProduct"
            v-if="!editProduct">
            Add Product
          </button>
          <button
            type="submit"
            class="btn btn-primary btn-sm rounded-1"
            @click.prevent="saveEdit"
            v-else>
            Save Changes
          </button>
        </div>
      </form>
    </section>
    <section class="col-sm-10 col-lg-8 product__list d-flex flex-wrap justify-content-center gap-sm-4 gap-lg-5">
      <h1 class="text-center w-100 mb-sm-0 text-white fw-light">Products</h1>
      <h1 class="text-center w-100 mb-sm-0 text-white fw-light" v-if="this.loading">Loading Products...</h1>
     <div class="col-sm-5 col-lg-3 card bg-white rounded-2 p-2 shadow-sm" v-for="({id, Name, Description, Price, ImageURL}) in items" :key="id">
        <img :src="require(`./assets/img/${ImageURL}`)" alt="Image Product" class="m-0" name="productImage">
        <p class="fw-light text-center fw-bold mb-1" name="product">
          {{ Name }}
        </p>
        <p class="m-0 fw-light text-center mb-4" name="productDescription">{{ Description }}</p>
        <div class="d-flex flex-wrap mb-1 justify-content-between align-items-center mt-1">
          <span class="w-25 badge bg-success rounded-pill py-2 text-center" name="productPrice">${{ Price }}</span>
          <div class="d-flex gap-2" :data-id="id" name="productID">
              <button class="btn btn btn-edit d-flex align-items-center gap-1 position-relative border-0 px-2 rounded-1" @click.prevent="updateProduct">
                <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-edit" width="18" height="18" viewBox="0 0 24 24" stroke-width="2" stroke="#ffffff" fill="none" stroke-linecap="round" stroke-linejoin="round">
                  <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                  <path d="M9 7h-3a2 2 0 0 0 -2 2v9a2 2 0 0 0 2 2h9a2 2 0 0 0 2 -2v-3" />
                  <path d="M9 15h3l8.5 -8.5a1.5 1.5 0 0 0 -3 -3l-8.5 8.5v3" />
                  <line x1="16" y1="5" x2="19" y2="8" />
                </svg>
              </button>
              <button class="btn btn btn-delete d-flex align-items-center gap-1 position-relative border-0 px-2 rounded-1" @click.prevent="deleteProduct">
                <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-trash" width="18" height="18" viewBox="0 0 24 24" stroke-width="2" stroke="#ffffff" fill="none" stroke-linecap="round" stroke-linejoin="round">
                  <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                  <line x1="4" y1="7" x2="20" y2="7" />
                  <line x1="10" y1="11" x2="10" y2="17" />
                  <line x1="14" y1="11" x2="14" y2="17" />
                  <path d="M5 7l1 12a2 2 0 0 0 2 2h8a2 2 0 0 0 2 -2l1 -12" />
                  <path d="M9 7v-3a1 1 0 0 1 1 -1h4a1 1 0 0 1 1 1v3" />
                </svg>
              </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script>
import swal from 'sweetalert';

export default {
  name: 'App',
  data(){
    return{
      baseURL: 'http://localhost/productsCRUDPHP/public/',
      loading: false,
      editProduct: false,
      form: null,
      items: [],
      imageURL: '',
      formData: {
        product: null,
        productDescription: null,
        productPrice: null,
      },
    }
  },
  mounted() {
    this.getAllProducts()
    this.form = this.$refs.form
  },
  watch: {
    items(){
      if(this.loading){
        this.getAllProducts()
      }
    }
  },
  methods: {
    async getAllProducts(){
      this.loading = true

      const res = await fetch(`${this.baseURL}?allProducts`)
      this.items = await res.json()

      this.loading = false
    },
    async createProduct(){
      // Send them creating a new FormData
      const res = await fetch(`${this.baseURL}`, {
        method: 'POST',
        body: new FormData(this.form)  
      })

      const result = await res.json()
      
      // We reset the form field
      this.resetData()

      this.resultAlert(result, 'create')
    },
    async saveEdit(){

      const formData = new FormData(this.form)

      formData.append('id', this.form.getAttribute('data-id'))

      const res = await fetch(`${this.baseURL}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'multipart/form-data'
        },
        body: formData  
      })

      const result = await res.text()

      // We reset the form field
      this.resetData()

      this.resultAlert(result, 'edit')
    },
    async deleteProduct(event){
      const btn = event.target;

      if(btn.classList.contains('btn-delete')){
        const id = btn.parentElement.getAttribute('data-id');
        
        const res = await fetch(`${this.baseURL}?id=${id}`, {
          method: 'DELETE'
        })

        const result = await res.json()
  
        this.resultAlert(result, 'delete')
      }
    },
    updateProduct(event){

      this.editProduct = true

      const card = event.target.parentElement.parentElement.parentElement,
            fields = card.querySelectorAll('[name]')
      
      const form = document.querySelector('form')

      for(const datos of fields){
        let name = datos.getAttribute('name')

        switch(name){
          case "productImage":
            this.imageURL = datos.src
            break
          case "productPrice":
            this.formData[name] = parseFloat(datos.textContent.replace('$', ''))
            break
          case "productID":
            form.setAttribute('data-id', datos.getAttribute('data-id')) 
            break
          default:
            this.formData[name] = datos.textContent
            break
        }
      }
    },
    renderImage(event){
      const file = event.target.files[0]
      const reader = new FileReader()

      reader.onload = (event) => {
        this.imageURL = event.target.result
      }

      reader.readAsDataURL(file)
    },
    resetData(){
      this.editProduct = false
      this.imageURL = ''

      for (const prop in this.formData) {
        this.formData[prop] = null
      }
    },
    resultAlert(result, action){
      // We update the items
      this.getAllProducts()

      // We display an alert depending on the result of our insertion
      result 
        ? swal("Good job!", `Product ${action + (action[action.length - 1] == 'e') ? 'd' : 'ed'} successfully!`, "success") 
        : swal("Error!", `Failed to ${action} product!`, "error")
    }
  }
}
</script>

<style>
  :root{
    --primary: #42b883;
    --primaryDark: rgb(66 184 131 / 85%);
    --secondary: #004F98;
    --secondaryDark: rgb(0 79 152 / 85%);
    --danger: #EE2E31;
    --dangerDark: rgb(238 46 49 / 85%);
    --white: #FFFFFF;
    --dark: #2A2A2A;
  }
  
  .bg-vue{ background-color: var(--primary); }

  .btn-edit{ background-color: var(--secondary); }
  .btn-delete{ background-color: var(--danger); }

  .btn-delete:active{
    background-color: var(--danger) !important;
  }

  .btn-edit:active{
    background-color: var(--secondary) !important;
  }

  .btn-edit:hover{ background-color: var(--secondaryDark); }
  .btn-delete:hover{ background-color: var(--dangerDark); }

  .btn-edit,
  .btn-delete{
    color: var(--white);
    transition: transform .3s ease, background-color .3s ease;
  }

  .btn-delete::after,
  .btn-edit::after{
    content: '';
    position: absolute;
    width: 100%;
    height: 100%;
  }

  .btn-edit:hover,
  .btn-delete:hover{
    transform: translateY(-2px);
    color: var(--white);
  }

  .img-product{
    width: auto;
    height: 10rem;
  }

  .addProduct{
    transition: transform .3s ease;
  }

  .addProduct:hover{
    cursor: pointer;
    transform: scale(1.1);
  }

</style>
