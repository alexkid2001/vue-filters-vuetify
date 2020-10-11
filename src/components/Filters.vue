<template>
  <v-card class="filters">
    <v-card-title>
      Filters
    </v-card-title>
    <v-card-text>
      <v-autocomplete
        v-model="filtersParams.country"
        :items="countries"
        hide-no-data
        hide-selected
        label="Country"
        placeholder="Start typing to Search"
        return-object
      ></v-autocomplete>
      <v-autocomplete
        v-model="filtersParams.type"
        :items="types"
        hide-no-data
        hide-selected
        label="Type"
        placeholder="Тип жилья"
        return-object
      ></v-autocomplete>
      <v-container class="container--checkboxes">
        <div class="container__title">Количество звезд</div>
        <template v-for="(star, index) in [1, 2, 3, 4, 5]">
          <v-checkbox
            v-model="filtersParams.stars"
            :value="star"
            :key="index"
            class="checkbox"
          >
            <template v-slot:label>
              <v-icon
                small
                color="orange"
                v-for="ind in star"
                :key="ind"
              >
                mdi-star
              </v-icon>
            </template>
          </v-checkbox>
        </template>
      </v-container>

      <v-text-field
        label="Количество отзывов от"
        v-model="filtersParams.reviews_amount"
      />

      <v-slider
        v-model="filtersParams.min_price"
        label="Цена до:"
        thumb-label="always"
        :max="rangePrice[1]"
        :min="rangePrice[0]"
      >
        <template v-slot:thumb-label="item">
          <div class="slider__label">{{item.value}}</div>
        </template>
      </v-slider>

    </v-card-text>
    <v-card-actions class="filters__action">
      <v-btn
        color="primary"
        @click.prevent="doFilter"
      >
        Применить фильтр
      </v-btn>
      <v-btn
        @click.prevent="clearFilter"
      >
        Очистить фильтр
      </v-btn>
    </v-card-actions>
  </v-card>

</template>

<script>
export default {
  name: 'Filters',
  data () {
    return {
      filtersParams: {
        country: '',
        type: '',
        stars: [],
        reviews_amount: '',
        min_price: ''
      },
      isLoading: false,
      search: null
    }
  },
  props: [
    'hotels',
    'countries'
  ],
  created () {
  },
  computed: {
    types () {
      const typesList = []

      if (!this.hotels) {
        return
      }
      this.hotels.map(item => {
        if (!typesList.includes(item.type)) {
          typesList.push(item.type)
        }
      })
      return typesList
    },
    rangePrice () {
      if (!this.hotels) {
        return [0, 0]
      }
      let min = this.hotels[0].min_price
      let max = this.hotels[0].min_price

      this.hotels.forEach(item => {
        const v = item.min_price
        min = (v < min) ? v : min
        max = (v > max) ? v : max
      })

      return [min, max]
    }
  },
  watch: {
    rangePrice () {
      this.filtersParams.min_price = this.rangePrice[1]
    }
  },
  methods: {
    doFilter () {
      this.$emit('doFilter', this.filtersParams)
    },
    clearFilter () {
      this.filtersParams.country = null
      this.filtersParams.type = null
      this.filtersParams.stars = []
      this.filtersParams.reviews_amount = []
      this.filtersParams.min_price = this.rangePrice[1]
      this.$emit('doFilter', this.filtersParams)
    }
  }
}
</script>

<style lang="scss">
  .filters {

    &__title {
      font-size: 24px;
      font-weight: bold;
    }

    &__action {
      display: flex;
      flex-direction: column;

      &.v-card__action {

      }

      .v-btn {
        width: 100%;

        &:nth-child(n+2) {
          margin-top: 20px;
          margin-left: 0 !important;
        }
      }
    }
  }

  .v-input--checkbox.checkbox {
    margin-top: 0px;

    .v-messages {
      display: none;
    }
  }

  .container {

    &.container--checkboxes {
      padding: 0;
    }
  }

  .v-input__slider {

    .v-input__slot {
      flex-direction: column;

      & > * {
        flex-basis: 100%;
        width: 100%;
      }
    }

    .v-slider__thumb-label {
      background-color: #fff !important;
      padding: 5px;
      bottom: auto;
      top: calc(100% + 10px);
      border-radius: 2px;
      transform: translateX(-50%) rotate(0) !important;
      width: auto !important;
      height: auto !important;
      line-height: 1;
      & > * {
        transform: rotate(0) !important;
      }
    }
  }

  .v-slider--horizontal {
    margin-left: 28px !important;
    margin-right: 28px !important;
    width: calc(100% - 20px) !important;
  }

</style>
