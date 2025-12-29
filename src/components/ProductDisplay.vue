<template>
  <div :class="['page', themeClass]">
    <div class="card">

      <!-- LOADER -->
      <div v-if="loading" class="loader"></div>

      <!-- CONTENT -->
      <template v-else>

        <!-- UNAVAILABLE -->
        <div v-if="!isValidProduct" class="unavailable">
          <p>This product is unavailable to show</p>
          <button class="btn-outline gray" @click="nextProduct">
            Next product
          </button>
        </div>

        <!-- PRODUCT -->
        <div
          v-else
          class="content"
        >
          <img :src="product.image" class="product-img" />

          <div class="info">
            <!-- TITLE -->
            <h2
              class="title"
              :class="{
                women: product.category === `women's clothing`,
                men: product.category === `men's clothing`
              }"
            >
              {{ product.title }}
            </h2>

            <!-- META ROW -->
            <div class="meta-row">
              <p class="category">{{ product.category }}</p>

              <div class="rating">
                <span class="rate">{{ product.rating?.rate }}/5</span>
                <div class="dots">
                  <span
                    v-for="n in 5"
                    :key="n"
                    :class="[
                      'dot',
                      n <= Math.round(product.rating?.rate)
                        ? `active ${
                            product.category === `women's clothing`
                              ? 'women'
                              : product.category === `men's clothing`
                              ? 'men'
                              : product.category
                          }`
                        : ''
                    ]"
                  ></span>
                </div>
              </div>
            </div>

            <hr class="divider" />

            <!-- DESCRIPTION (SCROLL SAFE) -->
            <div class="text-content">
              <p class="description">
                {{ product.description }}
              </p>
            </div>

            <hr class="divider" />

            <!-- PRICE -->
            <p
              class="price"
              :class="{
                women: product.category === `women's clothing`,
                men: product.category === `men's clothing`
              }"
            >
              ${{ product.price }}
            </p>

            <!-- ACTIONS -->
            <div class="actions">
              <button
                class="btn"
                :class="{
                  women: product.category === `women's clothing`,
                  men: product.category === `men's clothing`,
                  jewelery: product.category === 'jewelery',
                  electronics: product.category === 'electronics'
                }"
              >
                Buy now
              </button>

              <button
                class="btn-outline"
                :class="{
                  women: product.category === `women's clothing`,
                  men: product.category === `men's clothing`,
                  gray:
                    product.category === 'jewelery' ||
                    product.category === 'electronics'
                }"
                @click="nextProduct"
              >
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

const product = ref(null)
const loading = ref(true)
const index = ref(1)
const isValidProduct = ref(true)

const fetchProduct = async () => {
  loading.value = true
  isValidProduct.value = true

  try {
    const res = await fetch(`https://fakestoreapi.com/products/${index.value}`)

    if (!res.ok) {
      throw new Error('API error')
    }

    const data = await res.json()

    // validasi data produk
    if (!data || !data.id) {
      isValidProduct.value = false
      product.value = null
    } else {
      product.value = data
    }

  } catch (error) {
    isValidProduct.value = false
    product.value = null
  }

  loading.value = false
}

const nextProduct = () => {
  index.value = index.value === 20 ? 1 : index.value + 1
  fetchProduct()
}

const themeClass = computed(() => {
  if (!product.value || !isValidProduct.value) return 'gray-bg'
  if (product.value.category === "women's clothing") return 'women-bg'
  if (product.value.category === "men's clothing") return 'men-bg'
  return 'gray-bg'
})


onMounted(fetchProduct)
</script>