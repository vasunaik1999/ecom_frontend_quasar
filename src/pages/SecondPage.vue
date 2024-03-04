<template>
  <q-page class="">

    <div class="row">
      <div :class="$q.screen.gt.md ? 'col-3' : 'col-12'">
        <q-card bordered class="my-card" style="height: 100%; width: 100%;">
          <q-card-section>
            <div class="text-h6">Filters</div>
          </q-card-section>

          <q-card-section class="q-pt-xs">
            <div class="text-subtitle2 q-mt-md">Sort by Price</div>
            <q-btn-toggle v-model="sortByPrice" :options="sortOptions" color="primary" />

            <div class="text-subtitle2 q-mt-lg">Filter by Category</div>
            <q-select v-model="selectedCategory" outlined :options="categoryOptions" label="Filter by Category"
              color="primary" />

            <div class="text-subtitle2 q-mt-lg">Filter by Rating</div>
            <q-select filled v-model="selectedRatings" multiple :options="ratingsOptions" use-chips stack-label
              label="Filter by Ratings" color="primary" />

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
            <q-img :class="$q.screen.gt.md ? 'col-3' : 'col-12'" style=" height: 200px; " :src="product.Image" />

            <q-card-section style=" width: 100%;">
              <h6 class="q-my-xs">{{ product.Name }}</h6>
              <div class="q-gutter-y-md column">
                <q-rating v-model="product.Ratings" size="2em" color="yellow-9" icon="star_border"
                  icon-selected="star" />
              </div>
              <p>{{ product.Description }}</p>
            </q-card-section>

            <aside class="text-center q-my-auto">
              <h4 class="q-mt-xs q-mb-md q-px-xl text-bold">₹{{ product.Price }}</h4>
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
import products from "src/assets/json/products.json"

export default defineComponent({
  name: 'IndexPage',

  data() {
    return {
      products: products,
      sortByPrice: 'asc',
      sortOptions: [
        { label: 'Low to high', value: 'asc' },
        { label: 'High to Low', value: 'desc' }
      ],
      selectedCategory: null,
      categoryOptions: ['None', ...new Set(products.map(product => product.Category))],
      selectedRatings: [],
      ratingsOptions: ['1', '2', '3', '4', '5'],
      minPrice: null,
      maxPrice: null,
      // priceError: null,
    }

  },

  computed: {
    filteredProducts() {
      let filtered = this.products.slice();

      // category filt
      if (this.selectedCategory && this.selectedCategory !== 'None') {
        filtered = filtered.filter(product => product.Category === this.selectedCategory);
      }

      // ratings
      if (this.selectedRatings.length > 0) {
        filtered = filtered.filter(product => this.selectedRatings.includes(product.Ratings));
      }

      // sort price
      if (this.sortByPrice === 'asc') {
        filtered.sort((a, b) => a.Price - b.Price);
      } else {
        filtered.sort((a, b) => b.Price - a.Price);
      }

      // Filter by price range
      if (this.minPrice !== null && this.maxPrice !== null) {
        // if (this.minPrice > this.maxPrice) {
        //   console.log("Min Price cannot be gerater then max price")
        // } else {
        filtered = filtered.filter(product => product.Price >= this.minPrice && product.Price <= this.maxPrice);
        // }
      }

      return filtered;
    }

  },


})
</script>
