<script setup>
import { computed, ref } from 'vue'

const darkMode = ref(true)

function toggleDarkMode() {
  darkMode.value = !darkMode.value
}

const currentPage = ref('home')

function goTo(page) {
  currentPage.value = page
}

const searchText = ref('')

const products = ref([
  {
    id: 1,
    name: 'Air Max 270',
    category: 'Running',
    price: 99.00,
    image: 'https://images.unsplash.com/photo-1542291026-7eec264c27ff?w=700',
    rating: 4.8
  },
  {
    id: 2,
    name: 'Air Jordan 1',
    category: 'Lifestyle',
    price: 95.00,
    image: 'https://images.unsplash.com/photo-1552346154-21d32810aba3?w=700',
    rating: 4.9
  },
  {
    id: 3,
    name: 'Air Force 1',
    category: 'Lifestyle',
    price: 119.00,
    image: 'https://images.unsplash.com/photo-1600185365483-26d7a4cc7519?w=700',
    rating: 4.7
  },
  {
    id: 4,
    name: 'Zoom Fly 5',
    category: 'Running',
    price: 149.00,
    image: 'https://images.unsplash.com/photo-1608231387042-66d1773070a5?w=700',
    rating: 4.6
  },
  {
    id: 5,
    name: 'Dunk Low',
    category: 'Lifestyle',
    price: 124.00,
    image: 'https://images.unsplash.com/photo-1554136898-6e569b1d7e4f?w=700',
    rating: 4.8
  },
  {
    id: 6,
    name: 'Pegasus 41',
    category: 'Running',
    price: 129.00,
    image: 'https://images.unsplash.com/photo-1495555961986-6d4c1ecb7be3?w=700',
    rating: 4.7
  }
])

const selectedCategory = ref('All')

const categories = [
  'All',
  'Running',
  'Lifestyle'
]

const filteredProducts = computed(() => {
  return products.value.filter(product => {
    const categoryMatch =
      selectedCategory.value === 'All' ||
      product.category === selectedCategory.value

    const searchMatch =
      product.name
        .toLowerCase()
        .includes(searchText.value.toLowerCase())

    return categoryMatch && searchMatch
  })
})

const favorites = ref([])

function toggleFavorite(product) {
  const index = favorites.value.findIndex(
    item => item.id === product.id
  )

  if (index === -1) {
    favorites.value.push(product)
  } else {
    favorites.value.splice(index, 1)
  }
}

function isFavorite(product) {
  return favorites.value.some(
    item => item.id === product.id
  )
}

const cart = ref([])

function addToCart(product, size = null) {
  const existing = cart.value.find(
    item =>
      item.id === product.id &&
      item.size === size
  )

  if (existing) {
    existing.quantity++
  } else {
    cart.value.push({
      ...product,
      size: size,
      quantity: 1
    })
  }
}

function increaseQuantity(item) {
  item.quantity++
}

function decreaseQuantity(item) {
  if (item.quantity > 1) {
    item.quantity--
  } else {
    removeFromCart(item)
  }
}

function removeFromCart(item) {
  const index = cart.value.findIndex(
    product =>
      product.id === item.id &&
      product.size === item.size
  )

  if (index !== -1) {
    cart.value.splice(index, 1)
  }
}

const cartCount = computed(() => {
  return cart.value.reduce(
    (total, item) => total + item.quantity,
    0
  )
})

const subtotal = computed(() => {
  return cart.value.reduce(
    (total, item) =>
      total + item.price * item.quantity,
    0
  )
})

const shipping = computed(() => {
  if (subtotal.value === 0) return 0

  return subtotal.value >= 100 ? 0 : 10
})

const total = computed(() => {
  return subtotal.value + shipping.value
})

const selectedProduct = ref(null)

const selectedSize = ref(null)

const availableSizes = [7, 8, 9, 10, 11]

function selectSize(size) {
  selectedSize.value = size
}

function viewProduct(product) {
  selectedProduct.value = product
  selectedSize.value = null
  currentPage.value = 'product'
}

function addSelectedProductToCart() {
  if (!selectedProduct.value) {
    return
  }

  if (!selectedSize.value) {
    alert('Please select a shoe size first.')
    return
  }

  addToCart(
    selectedProduct.value,
    selectedSize.value
  )

  alert(
    `${selectedProduct.value.name} - Size ${selectedSize.value} added to cart!`
  )
}

const customer = ref({
  firstName: '',
  lastName: '',
  email: '',
  address: '',
  city: '',
  postalCode: ''
})

const paymentMethod = ref('card')

function checkout() {
  if (cart.value.length === 0) {
    alert('Your cart is empty.')
    return
  }

  currentPage.value = 'checkout'
}

function placeOrder() {
  if (
    !customer.value.firstName ||
    !customer.value.lastName ||
    !customer.value.email ||
    !customer.value.address
  ) {
    alert('Please complete all required fields.')
    return
  }

  alert('Order placed successfully!')

  cart.value = []

  currentPage.value = 'home'
}

function showHome() {
  currentPage.value = 'home'
}

function showShop() {
  currentPage.value = 'shop'
}

function showFavorites() {
  currentPage.value = 'favorites'
}

function showCart() {
  currentPage.value = 'cart'
}
</script>


<template>

  <div
    class="shop"
    :class="{ 'dark-mode': darkMode }"
  >

    <header class="header">

      <div class="logo">
        <p>
          OS:
          Online Sapatos
        </p>
      </div>


      <nav class="desktop-nav">

        <button
          :class="{ active: currentPage === 'home' }"
          @click="showHome"
        >
          Home
        </button>

        <button
          :class="{ active: currentPage === 'shop' }"
          @click="showShop"
        >
          Shop
        </button>

        <button
          :class="{ active: currentPage === 'favorites' }"
          @click="showFavorites"
        >
          Favorites
        </button>

      </nav>


      <div class="header-actions">

        <button
          class="dark-toggle"
          @click="toggleDarkMode"
        >

          <span>
            {{ darkMode ? '☀️' : '🌙' }}
          </span>

          <span class="mode-text">
            {{ darkMode ? 'Dark' : 'Light' }}
          </span>

          <span
            class="toggle"
            :class="{ on: darkMode }"
          >
            <span class="toggle-circle"></span>
          </span>

        </button>


        <button
          class="icon-button"
          @click="showShop"
        >
          🔍
        </button>


        <button
          class="icon-button"
          @click="showFavorites"
        >
          ♡

          <span
            v-if="favorites.length"
            class="badge"
          >
            {{ favorites.length }}
          </span>

        </button>


        <button
          class="icon-button"
          @click="showCart"
        >
          🛒

          <span
            v-if="cartCount"
            class="badge"
          >
            {{ cartCount }}
          </span>

        </button>

      </div>

    </header>

    <main v-if="currentPage === 'home'">

      <section class="hero">

        <div class="hero-content">

          <p class="eyebrow">
            Pamalit namo
          </p>

          <h1>
            ukay ukay<br>
            online sapatos
          </h1>

          <p>
            Discover a premium
            designed ukay ukay,
            performance and the style is nice.
          </p>

          <button
            class="primary-button"
            @click="showShop"
          >
            PALIT NA
          </button>

        </div>

      </section>


      <section class="section">

        <div class="section-heading">

          <h2>
            Featured Products
          </h2>

          <button @click="showShop">
            View All →
          </button>

        </div>


        <div class="product-grid">

          <article
            v-for="product in products.slice(0, 4)"
            :key="product.id"
            class="product-card"
          >

            <div
              class="product-image"
              @click="viewProduct(product)"
            >

              <img
                :src="product.image"
                :alt="product.name"
              >

              <button
                class="favorite-button"
                @click.stop="toggleFavorite(product)"
              >
                {{ isFavorite(product) ? '♥' : '♡' }}
              </button>

            </div>


            <div class="product-info">

              <small>
                {{ product.category }}
              </small>

              <h3>
                {{ product.name }}
              </h3>

              <div class="rating">
                ★ {{ product.rating }}
              </div>

              <strong>
                ₱{{ product.price.toFixed(2) }}
              </strong>

            </div>

          </article>

        </div>

      </section>

    </main>

    <main
      v-else-if="currentPage === 'shop'"
      class="page"
    >

      <div class="page-title">

        <h1>
          Shop
        </h1>

        <p>
          Find your perfect pair.
        </p>

      </div>


      <div class="search-box">

        <span>
          🔍
        </span>

        <input
          v-model="searchText"
          type="text"
          placeholder="Search products..."
        >

      </div>


      <div class="filters">

        <button
          v-for="category in categories"
          :key="category"
          :class="{
            selected:
              selectedCategory === category
          }"
          @click="selectedCategory = category"
        >
          {{ category }}
        </button>

      </div>


      <div class="product-grid">

        <article
          v-for="product in filteredProducts"
          :key="product.id"
          class="product-card"
        >

          <div
            class="product-image"
            @click="viewProduct(product)"
          >

            <img
              :src="product.image"
              :alt="product.name"
            >

            <button
              class="favorite-button"
              @click.stop="toggleFavorite(product)"
            >
              {{ isFavorite(product) ? '♥' : '♡' }}
            </button>

          </div>


          <div class="product-info">

            <small>
              {{ product.category }}
            </small>

            <h3>
              {{ product.name }}
            </h3>

            <div class="rating">
              ★ {{ product.rating }}
            </div>

            <strong>
              ₱{{ product.price.toFixed(2) }}
            </strong>

            <button
              class="add-button"
              @click="addToCart(product)"
            >
              Add to Cart
            </button>

          </div>

        </article>

      </div>


      <div
        v-if="filteredProducts.length === 0"
        class="empty-state"
      >

        <h2>
          No products found
        </h2>

        <p>
          Try another search.
        </p>

      </div>

    </main>

    <main
      v-else-if="currentPage === 'product'"
      class="page"
    >

      <button
        class="back-button"
        @click="showShop"
      >
        ← Back to Shop
      </button>


      <div
        v-if="selectedProduct"
        class="product-detail"
      >

        <div class="detail-image">

          <img
            :src="selectedProduct.image"
            :alt="selectedProduct.name"
          >

          <button
            type="button"
            class="detail-favorite"
            @click="toggleFavorite(selectedProduct)"
          >
            {{
              isFavorite(selectedProduct)
                ? '♥'
                : '♡'
            }}
          </button>

        </div>

        <div class="detail-information">

          <small class="product-category">
            {{ selectedProduct.category }}
          </small>


          <h1>
            {{ selectedProduct.name }}
          </h1>


          <div class="detail-rating">
            ★ {{ selectedProduct.rating }}
          </div>


          <h2 class="detail-price">
            ₱{{ selectedProduct.price.toFixed(2) }}
          </h2>


          <p class="description">
            Premium ukay ukay unsa pay gi hulat
            ninyo pamalit namo. aray mo iyak
          </p>

          <div class="size-section">

            <div class="size-header">

              <h3>
                Select Size
              </h3>

              <span
                v-if="selectedSize"
                class="selected-size-text"
              >
                Size {{ selectedSize }} selected
              </span>

            </div>

            <div class="sizes">

              <button
                v-for="size in availableSizes"
                :key="size"
                type="button"
                class="size-button"
                :class="{
                  selected: selectedSize === size
                }"
                @click="selectSize(size)"
              >
                {{ size }}
              </button>

            </div>


            <p
              v-if="!selectedSize"
              class="size-hint"
            >
              Please select your size
            </p>

          </div>

          <div class="detail-actions">

            <button
              type="button"
              class="primary-button add-cart-detail"
              :class="{
                disabled: !selectedSize
              }"
              @click="addSelectedProductToCart"
            >

              🛒

              <span v-if="selectedSize">
                Add Size {{ selectedSize }} to Cart
              </span>

              <span v-else>
                Select a Size
              </span>

            </button>


            <button
              type="button"
              class="favorite-detail"
              @click="toggleFavorite(selectedProduct)"
            >
              {{
                isFavorite(selectedProduct)
                  ? '♥'
                  : '♡'
              }}
            </button>

          </div>

        </div>

      </div>

    </main>

    <main
      v-else-if="currentPage === 'favorites'"
      class="page"
    >

      <div class="page-title">

        <h1>
          Favorites
        </h1>

        <p>
          Your favorite products.
        </p>

      </div>


      <div
        v-if="favorites.length"
        class="product-grid"
      >

        <article
          v-for="product in favorites"
          :key="product.id"
          class="product-card"
        >

          <div
            class="product-image"
            @click="viewProduct(product)"
          >

            <img
              :src="product.image"
              :alt="product.name"
            >


            <button
              class="favorite-button"
              @click.stop="toggleFavorite(product)"
            >
              ♥
            </button>

          </div>


          <div class="product-info">

            <small>
              {{ product.category }}
            </small>

            <h3>
              {{ product.name }}
            </h3>

            <strong>
              ₱{{ product.price.toFixed(2) }}
            </strong>

          </div>

        </article>

      </div>


      <div
        v-else
        class="empty-state"
      >

        <div class="empty-icon">
          ♡
        </div>

        <h2>
          No Favorites Yet
        </h2>

        <p>
          Save products you love here.
        </p>

        <button
          class="primary-button"
          @click="showShop"
        >
          Browse Products
        </button>

      </div>

    </main>

    <main
      v-else-if="currentPage === 'cart'"
      class="page"
    >

      <div class="page-title">

        <h1>
          Shopping Cart
        </h1>

        <p>
          {{ cartCount }} item(s)
        </p>

      </div>


      <div
        v-if="cart.length"
        class="cart-layout"
      >

        <div class="cart-items">

          <div
            v-for="item in cart"
            :key="`${item.id}-${item.size}`"
            class="cart-item"
          >

            <img
              :src="item.image"
              :alt="item.name"
            >


            <div class="cart-item-info">

              <small>
                {{ item.category }}
              </small>

              <h3>
                {{ item.name }}
              </h3>


              <p class="cart-size">
                Size:
                <strong>
                  {{ item.size || 'Not selected' }}
                </strong>
              </p>


              <strong>
                ₱{{ item.price.toFixed(2) }}
              </strong>


              <div class="quantity">

                <button
                  @click="decreaseQuantity(item)"
                >
                  −
                </button>

                <span>
                  {{ item.quantity }}
                </span>

                <button
                  @click="increaseQuantity(item)"
                >
                  +
                </button>

              </div>

            </div>


            <button
              class="remove"
              @click="removeFromCart(item)"
            >
              ×
            </button>

          </div>

        </div>

        <aside class="cart-summary">

          <h2>
            Summary
          </h2>


          <div class="summary-row">

            <span>
              Subtotal
            </span>

            <strong>
              ₱{{ subtotal.toFixed(2) }}
            </strong>

          </div>


          <div class="summary-row">

            <span>
              Shipping
            </span>

            <strong>
              {{
                shipping === 0
                  ? 'FREE'
                  : '₱' + shipping.toFixed(2)
              }}
            </strong>

          </div>


          <div class="total-row">

            <span>
              Total
            </span>

            <strong>
              ₱{{ total.toFixed(2) }}
            </strong>

          </div>


          <button
            class="primary-button full"
            @click="checkout"
          >
            Checkout
          </button>

        </aside>

      </div>

      <div
        v-else
        class="empty-state"
      >

        <div class="empty-icon">
          🛒
        </div>

        <h2>
          Your Cart is Empty
        </h2>

        <p>
          Add some products to your cart.
        </p>

        <button
          class="primary-button"
          @click="showShop"
        >
          Start Shopping
        </button>

      </div>

    </main>

    <main
      v-else-if="currentPage === 'checkout'"
      class="page"
    >

      <button
        class="back-button"
        @click="showCart"
      >
        ← Back to Cart
      </button>


      <div class="page-title">

        <h1>
          Checkout
        </h1>

        <p>
          Complete your order.
        </p>

      </div>


      <div class="checkout-layout">

        <form
          class="checkout-form"
          @submit.prevent="placeOrder"
        >

          <section class="checkout-section">

            <h2>
              Contact Information
            </h2>

            <input
              v-model="customer.email"
              type="email"
              placeholder="Email address"
              required
            >

          </section>

          <section class="checkout-section">

            <h2>
              Shipping Address
            </h2>


            <div class="two-columns">

              <input
                v-model="customer.firstName"
                type="text"
                placeholder="First name"
                required
              >

              <input
                v-model="customer.lastName"
                type="text"
                placeholder="Last name"
                required
              >

            </div>


            <input
              v-model="customer.address"
              type="text"
              placeholder="Address"
              required
            >


            <div class="two-columns">

              <input
                v-model="customer.city"
                type="text"
                placeholder="City"
              >

              <input
                v-model="customer.postalCode"
                type="text"
                placeholder="Postal code"
              >

            </div>

          </section>

          <section class="checkout-section">

            <h2>
              Payment
            </h2>


            <label
              class="payment"
              :class="{
                active: paymentMethod === 'card'
              }"
            >

              <input
                v-model="paymentMethod"
                type="radio"
                value="card"
              >

              💳 Credit / Debit Card

            </label>


            <label
              class="payment"
              :class="{
                active: paymentMethod === 'cash'
              }"
            >

              <input
                v-model="paymentMethod"
                type="radio"
                value="cash"
              >

              💵 Cash on Delivery

            </label>

          </section>


          <button
            type="submit"
            class="primary-button full"
          >
            Place Order
          </button>

        </form>

        <aside class="checkout-summary">

          <h2>
            Order Summary
          </h2>


          <div
            v-for="item in cart"
            :key="`${item.id}-${item.size}`"
            class="checkout-product"
          >

            <img
              :src="item.image"
              :alt="item.name"
            >


            <div>

              <strong>
                {{ item.name }}
              </strong>

              <small>
                Size: {{ item.size || 'Not selected' }}
              </small>

              <small>
                Qty: {{ item.quantity }}
              </small>

            </div>


            <strong>
              ₱{{ (item.price * item.quantity).toFixed(2) }}
            </strong>

          </div>


          <hr>


          <div class="summary-row">

            <span>
              Subtotal
            </span>

            <strong>
              ₱{{ subtotal.toFixed(2) }}
            </strong>

          </div>


          <div class="summary-row">

            <span>
              Shipping
            </span>

            <strong>
              {{
                shipping === 0
                  ? 'FREE'
                  : '₱' + shipping.toFixed(2)
              }}
            </strong>

          </div>


          <div class="total-row">

            <span>
              Total
            </span>

            <strong>
              ₱{{ total.toFixed(2) }}
            </strong>

          </div>

        </aside>

      </div>

    </main>

    <nav class="mobile-nav">

      <button
        :class="{ active: currentPage === 'home' }"
        @click="showHome"
      >

        <span>
          ⌂
        </span>

        Home

      </button>


      <button
        :class="{ active: currentPage === 'shop' }"
        @click="showShop"
      >

        <span>
          ⌕
        </span>

        Shop

      </button>


      <button
        :class="{ active: currentPage === 'favorites' }"
        @click="showFavorites"
      >

        <span>
          ♡
        </span>

        Favorites

      </button>


      <button
        :class="{ active: currentPage === 'cart' }"
        @click="showCart"
      >

        <span>

          🛒

          <b v-if="cartCount">
            {{ cartCount }}
          </b>

        </span>

        Cart

      </button>

    </nav>

  </div>

</template>