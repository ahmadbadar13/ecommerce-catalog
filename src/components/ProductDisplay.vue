<template>
  <div :class="['page', themeClass]">
    <div class="card">

      <!-- LOADER -->
      <div v-if="loading" class="loader"></div>

      <!-- CONTENT -->
      <template v-else>

        <!-- UNAVAILABLE -->
        <div v-if="!isValidProduct" class="unavailable">
          <!-- SAD FACE -->
          <div class="sad-face">
            <div class="eyebrows">
              <span></span>
              <span></span>
            </div>

            <div class="eyes">
              <span></span>
              <span></span>
            </div>

            <div class="mouth"></div>
          </div>

          <p class="unavailable-text">
            This product is unavailable to show
          </p>

          <button class="btn-outline gray unavailable-btn" @click="nextProduct">
            Next product
          </button>
        </div>

        <!-- WOMEN -->
        <div v-else-if="product.category === `women's clothing`" class="content">
          <img :src="product.image" class="product-img" />

          <div class="info">
            <h2 class="title women">{{ product.title }}</h2>

            <div class="meta-row">
              <p class="category">{{ product.category }}</p>

              <div class="rating">
                <span class="rate">{{ product.rating.rate }}/5</span>
                <div class="dots">
                  <span
                    v-for="n in 5"
                    :key="n"
                    :class="['dot', n <= Math.round(product.rating.rate) ? 'active women' : '']"
                  ></span>
                </div>
              </div>
            </div>

            <hr class="divider" />

            <div class="text-content">
              <p class="description">{{ product.description }}</p>
            </div>

            <hr class="divider" />

            <p class="price women">${{ product.price }}</p>

            <div class="actions">
              <button class="btn women">Buy now</button>
              <button class="btn-outline women" @click="nextProduct">
                Next product
              </button>
            </div>
          </div>
        </div>

        <!-- MEN -->
        <div v-else-if="product.category === `men's clothing`" class="content">
          <img :src="product.image" class="product-img" />

          <div class="info">
            <h2 class="title men">{{ product.title }}</h2>

            <div class="meta-row">
              <p class="category">{{ product.category }}</p>

              <div class="rating">
                <span class="rate">{{ product.rating.rate }}/5</span>
                <div class="dots">
                  <span
                    v-for="n in 5"
                    :key="n"
                    :class="['dot', n <= Math.round(product.rating.rate) ? 'active men' : '']"
                  ></span>
                </div>
              </div>
            </div>

            <hr class="divider" />

            <div class="text-content">
              <p class="description">{{ product.description }}</p>
            </div>

            <hr class="divider" />

            <p class="price men">${{ product.price }}</p>

            <div class="actions">
              <button class="btn men">Buy now</button>
              <button class="btn-outline men" @click="nextProduct">
                Next product
              </button>
            </div>
          </div>
        </div>

      </template>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const product = ref({})
const loading = ref(false)
const index = ref(1)

/* FETCH API SESUAI INDEX */
const fetchProduct = async () => {
  loading.value = true

  try {
    const res = await fetch(`https://fakestoreapi.com/products/${index.value}`)
    const data = await res.json()
    product.value = data
  } catch (err) {
    product.value = {}
  }

  loading.value = false
}

/* NEXT PRODUCT (1–20 LOOP) */
const nextProduct = () => {
  index.value = index.value === 20 ? 1 : index.value + 1
  fetchProduct()
}

/* VALIDASI SESUAI SOAL */
const isValidProduct = computed(() => {
  return (
    product.value?.category === "men's clothing" ||
    product.value?.category === "women's clothing"
  )
})

/* CLASS BINDING SESUAI SOAL */
const themeClass = computed(() => {
  if (product.value?.category === "men's clothing") return 'men-bg'
  if (product.value?.category === "women's clothing") return 'women-bg'
  return 'gray-bg'
})

onMounted(fetchProduct)
</script>