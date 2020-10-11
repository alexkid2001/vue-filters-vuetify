<template>

  <v-app>
    <v-main>
      <filters
       :hotels="hotels"
       :countries="countries"
       @doFilter="doFilter"
      />
      <div class="main-content">
        <template v-if="filteredHotels.length">
          <table >
            <thead>
            <tr>
              <th>Название</th>
              <th>Описание</th>
            </tr>
            </thead>
            <tbody>
            <tr
              v-for="(item, index) in this.currentPageHotels"
              :key="index"
            >
              <td>{{item.name}}</td>
              <td>{{item.description}}</td>
              <td>
                <v-btn>Забронировать</v-btn>
              </td>
            </tr>
            </tbody>
          </table>
          <v-pagination
            v-model="currentPage"
            :length="maxPages"
            @input="changePage"
          ></v-pagination>
        </template>
        <div class="dummy-block" v-else>Записей не найдено</div>

      </div>
    </v-main>

  </v-app>
</template>

<script>
import Filters from './components/Filters'

export default {
  name: 'App',

  components: {
    Filters
  },

  data () {
    return {
      hotels: null,
      countries: null,
      filteredHotels: [],
      perpage: 3,
      currentPage: 0
    }
  },
  created () {
    fetch('/hotels.json')
      .then(res => res.json())
      .then(res => {
        this.hotels = res.hotels
        this.countries = res.countries
        this.filteredHotels = this.hotels
        this.currentPage = 1
      })
      .catch(err => {
        console.log(err)
      })
  },
  methods: {
    // filterHotels (filters) {
    //   const filterKeys = Object.keys(filters)
    //
    //   return this.filteredHotels.filter(function (eachObj) {
    //     return filterKeys.every(function (eachKey) {
    //       if (!filters[eachKey].length) {
    //         return true
    //       }
    //       return filters[eachKey].includes(eachObj[eachKey])
    //     })
    //   })
    // },
    doFilter (filters) {
      this.filteredHotels = this.hotels
      if (filters.country) {
        this.filteredHotels = this.filteredHotels.filter(item => item.country.toLowerCase() === filters.country.toLowerCase())
      }
      if (filters.type) {
        this.filteredHotels = this.filteredHotels.filter(item => item.type.toLowerCase() === filters.type.toLowerCase())
      }
      if (filters.stars.length) {
        this.filteredHotels = this.filteredHotels.filter(item => filters.stars.includes(item.stars))
      }
      if (filters.reviews_amount) {
        this.filteredHotels = this.filteredHotels.filter(item => item.reviews_amount >= filters.reviews_amount)
      }
      if (filters.min_price) {
        this.filteredHotels = this.filteredHotels.filter(item => item.min_price <= filters.min_price)
      }
    },
    changePage (page) {
      this.currentPage = page
    }
  },
  computed: {
    maxPages () {
      const tail = this.filteredHotels.length % this.perpage
      const intPage = parseInt(this.filteredHotels.length / this.perpage)

      return tail === 0 ? intPage : intPage + 1
    },
    currentPageHotels () {
      return this.filteredHotels.slice((this.currentPage - 1) * this.perpage, this.currentPage * this.perpage)
    }
  }

}
</script>

<style lang="scss">
  .v-main__wrap {
    display: flex;
  }

  .filters {
    flex-basis: 200px;
  }

  .main-content {
    flex-basis: 100%;
    flex: 1;
    padding: 2rem;
  }

  td {
    padding: 10px;

    &:first-child {
      width: 200px;
    }
  }

  tbody {

    tr {

      &:nth-child(odd) {
        background-color: #eee;
      }
    }
  }
</style>
