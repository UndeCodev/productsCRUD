<template>
  <main class="container-fluid bg-vue py-3 d-flex justify-content-center align-items-center flex-wrap gap-sm-2 gap-lg-5" style="min-height: 100vh;">
    <section class="col-sm-11 col-lg-3 product__add d-flex justify-content-center flex-wrap">
      <h1 class="fw-light text-white text-shadow text-center mb-3 w-100">Insert Product</h1>
      <form class="col-sm-6 col-lg-12 bg-white rounded-1 shadow-sm p-3" @submit.prevent="addProduct">
        <div class="d-flex justify-content-center flex-wrap" v-if="imageURL">
          <label for="" class="fw-bold w-100 text-center">Image selected:</label>
          <img :src="imageURL" alt="Image" class="w-50">
        </div>
        <div class="mb-2">
          <label for="selectImage">Select image:</label>
          <input type="file" class="form-control shadow-none" name="selectImage" id="selectImage" @change="renderImage">
        </div>
        <div class="mb-2">
          <label for="name">Name:</label>
          <input type="text" placeholder="Name" class="form-control shadow-none" name="name" id="name">
        </div>
        <div class="mb-2">
          <label for="description">Description:</label>
          <textarea name="description" id="description" cols="20" rows="3"  class="form-control shadow-none" placeholder="Description"></textarea>
        </div>
        <div class="mb-3">
          <label for="price">Price:</label>
          <input type="number" placeholder="Price" class="form-control shadow-none" name="price" id="price" min="1">
        </div>
        <div class="text-end">
          <button type="submit" class="btn btn-success btn-sm rounded-1" @submit.prevent="createProduct">Add Product</button>
        </div>
      </form>
    </section>
    <section class="col-sm-10 col-lg-8 product__list d-flex flex-wrap justify-content-center gap-sm-4 gap-lg-5">
      <h1 class="text-center w-100 mb-sm-0 text-white fw-light">Products</h1>
      <h1 class="text-center w-100 mb-sm-0 text-white fw-light" v-if="this.loading">Loading Products...</h1>
      <div class="col-sm-5 col-lg-3 card bg-white rounded-2 p-2 shadow-sm"
      v-for="({id, Name, Description, Price, ImageURL}) in items"
      :key="id">
        <img :src="require(`./assets/img/${ImageURL}`)" alt="Image Product" class="m-0">
        <p class="fw-light text-center fw-bold mb-1">
          {{ Name }}
        </p>
        <p class="m-0 fw-light text-center mb-4">{{ Description }}</p>
        <div class="d-flex flex-wrap mb-1 justify-content-between align-items-center mt-1">
          <span class="w-25 badge bg-success rounded-pill py-2 text-center">${{ Price }}</span>
          <div class="d-flex gap-2" :data-id="id">
              <button class="btn btn-sm btn-edit d-flex align-items-center gap-1">
                <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-edit" width="18" height="18" viewBox="0 0 24 24" stroke-width="2" stroke="#ffffff" fill="none" stroke-linecap="round" stroke-linejoin="round">
                  <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                  <path d="M9 7h-3a2 2 0 0 0 -2 2v9a2 2 0 0 0 2 2h9a2 2 0 0 0 2 -2v-3" />
                  <path d="M9 15h3l8.5 -8.5a1.5 1.5 0 0 0 -3 -3l-8.5 8.5v3" />
                  <line x1="16" y1="5" x2="19" y2="8" />
                </svg>
              </button>
              <button class="btn btn-sm btn-delete d-flex align-items-center gap-1 position-relative" @click.prevent="deleteProduct">
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
export default {
  name: 'App',
  data(){
    return{
      baseURL: 'http://localhost/Products_CRUD/public/',
      loading: false,
      items: [],
      imageURL: ''
    }
  },
  mounted() {
    this.getAllProducts()
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
      
      const res = await fetch(`${this.baseURL}/?allProducts`)
      this.items = await res.json()

      this.loading = false

      // console.log(this.items)
    },
    async addProduct(event){
      // We read the properties of the form received by the event
      const formData = new FormData(event.target)
      console.log(formData);

      // Creates a new object with the information obtained from the form
      const data = Object.assign({}, ...Array.from(formData, ([key, value]) => ({ [key]: value })))
      
      // console.log(data);

      const res    = await this.createProduct(data)
      const result = await res.json()
      console.log(result)
    },
    async createProduct(data){

      // console.log(data);

      // We send the object with the information from the form
      const res = await fetch(`${this.baseURL}`, {
        method: 'POST',
        body: data
      })

      return res
    },
    async deleteProduct(event){
      const btn = event.target;

      if(btn.classList.contains('btn-delete')){
        const id = btn.parentElement.getAttribute('data-id');
        const res = await fetch()
      }
    },
    renderImage(e){
      const file = e.target.files[0]
      // console.log(file)
      const reader = new FileReader()

      reader.onload = (e) => {
        this.imageURL = e.target.result
      }

      reader.readAsDataURL(file)
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

  .btn-vue{ background-color: var(--primary); }
  .btn-edit{ background-color: var(--secondary); }
  .btn-delete{ background-color: var(--danger); }

  .btn-delete:active{
    border: none;
    background-color: var(--danger) !important;
  }

  .btn-vue:hover{ background-color: var(--primaryDark); }
  .btn-edit:hover{ background-color: var(--secondaryDark); }
  .btn-delete:hover{ background-color: var(--dangerDark); }

  .btn-vue,
  .btn-edit,
  .btn-delete{
    color: var(--white);
    transition: transform .3s ease, background-color .3s ease;
  }

  .btn-delete::after{
    content: '';
    position: absolute;
    width: 100%;
    height: 100%;
  }

  .btn-vue:hover,
  .btn-edit:hover,
  .btn-delete:hover{
    transform: translateY(-2px);
    color: var(--white);
  }

  .img-product{
    width: auto;
    height: 10rem;
  }

</style>
