<template>
  <q-page class="">
    <div class="row">
      <div :class="$q.screen.gt.md ? 'col-3' : 'col-12'">
        <q-card bordered class="my-card" style="height: 100%; width: 100%; min-height: 100dh;">
          <q-card-section>
            <div class="text-h6">Filters</div>
          </q-card-section>

          <q-card-section class="q-pt-xs">
            <div class="text-subtitle2 q-mt-md">Sort by Price</div>
            <q-btn-toggle v-model="sortByPrice" :options="sortOptions" color="primary" />

            <div class="text-subtitle2 q-mt-lg">Filter by Category</div>
            <q-select v-model="selectedCategory" outlined :options="categoryOptions" label="Filter by Category"
              color="primary" />

            <!-- rating filter -->
            <div class="text-subtitle2 q-mt-lg">Filter by Rating</div>
            <q-select filled v-model="selectedRatings" multiple :options="ratingsOptions" use-chips stack-label
              label="Filter by Ratings" color="primary" />

            <!-- Price Range Filter -->
            <div class="q-gutter-md">
              <q-input v-model="minPrice" type="number" label="Min Price" color="primary" />
              <q-input v-model="maxPrice" type="number" label="Max Price" color="primary" />
            </div>


          </q-card-section>
        </q-card>
      </div>
      <div class="col-md-9 col-12">
        <q-card class="my-card q-ma-lg" v-for="(product, index) in   filteredProducts  " :key="index">
          <q-card-section :horizontal="$q.screen.gt.md">
            <q-img :class="$q.screen.gt.md ? 'col-3' : 'col-12'" style=" height: 200px; " :src="product.image" />

            <q-card-section style=" width: 100%;">
              <h6 class="q-my-xs">{{ product.name }}</h6>
              <div class="q-gutter-y-md column">
                <q-rating v-model="product.ratings" size="2em" color="yellow-9" icon="star_border"
                  icon-selected="star" />
              </div>
              <p>{{ product.description }}</p>
            </q-card-section>

            <aside class="text-center q-my-auto">
              <h4 class="q-mt-xs q-mb-md q-px-xl text-bold">₹{{ product.price }}</h4>
              <q-btn color="primary" label="Buy Now" />
            </aside>
          </q-card-section>
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import axios from 'axios';

export default defineComponent({
  name: 'IndexPage',

  data() {
    return {
      products: [],
      sortByPrice: 'asc',
      sortOptions: [
        { label: 'Low to high', value: 'asc' },
        { label: 'High to Low', value: 'desc' }
      ],
      selectedCategory: null,
      categoryOptions: ['None'],
      selectedRatings: [],
      ratingsOptions: [1, 2, 3, 4, 5],
      minPrice: null,
      maxPrice: null,
    }
  },

  computed: {
    filteredProducts() {
      let filtered = this.products.slice();

      // category fil
      if (this.selectedCategory && this.selectedCategory !== 'None') {
        filtered = filtered.filter(product => product.category === this.selectedCategory);
      }

      // ratings filter
      if (this.selectedRatings.length > 0) {
        filtered = filtered.filter(product => this.selectedRatings.includes(parseInt(product.ratings)));
        console.log('Filtered by ratings:', filtered);
      }

      // sort pprice
      if (this.sortByPrice === 'asc') {
        filtered.sort((a, b) => a.price - b.price);
      } else {
        filtered.sort((a, b) => b.price - a.price);
      }

      // min price max price filterr
      if (this.minPrice !== null && this.maxPrice !== null) {
        filtered = filtered.filter(product => product.price >= this.minPrice && product.price <= this.maxPrice);
      }

      return filtered;
    }
  },

  mounted() {
    this.fetchProducts();
  },

  methods: {
    async fetchProducts() {
      try {
        const response = await axios.get('http://127.0.0.1:8000/api/products');
        this.products = response.data;

        this.categoryOptions = ['None', ...new Set(this.products.map(product => product.category))];
      } catch (error) {
        console.error('Error fetching products:', error);
      }
    }
  }
})
</script>
