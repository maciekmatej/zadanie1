<template>
    <span class="search-icon">
        <span class="search-span">Wyszukaj</span>
        <i class="fas fa-search"></i>
    </span>
    <div class="search-bar">
        <input @input="nameUpdate" type="text" id="imie"
            v-model="nameQuery" placeholder="imię">
        <div @click.prevent="showQuery = !showQuery" class="dep-ctn">wybierz dział/y
            <button  class="query-btn">
                <i class="fas fa-chevron-down" :class="{'rotate': showQuery}"></i>
            </button>
        </div>
        <transition name="fade">
            <div v-show="showQuery === true" class="checks-ctn">
                <div v-for="department in departments" :key="department" class="checkbox-wrapper">
                    <input @change="departmentsUpdate" v-model="departmentsQuery"
                        type="checkbox" :id="department" :value="department">
                    <label :for="department">{{ department }}</label>
                </div>
            </div>
        </transition>
        <input @input="salaryUpdate" v-model="salaryQuery.min" type="number"
            placeholder="kwota od" id="min" class="min-salary-input">
        <span class="dash">-</span>
        <input @input="salaryUpdate" v-model="salaryQuery.max" type="number"
            placeholder="kwota do" class="max-salary-input">
        <button @click.prevent="clearQuery" class="reset-btn">
            <i class="fas fa-undo-alt"></i>
        </button>
    </div>
</template>

<script>
export default {
  data() {
    return {
      showQuery: false,
      nameQuery: '',
      departmentsQuery: [],
      salaryQuery: {
        min: null,
        max: null,
      },
    };
  },
  name: 'SearchBar',
  props: ['departments'],
  emits: ['clear-query', 'name-update', 'salary-update', 'dep-update'],
  methods: {
    nameUpdate() {
      this.$emit('name-update', this.nameQuery);
    },
    salaryUpdate() {
      this.$emit('salary-update', this.salaryQuery);
    },
    departmentsUpdate() {
      this.$emit('dep-update', this.departmentsQuery);
    },
    clearQuery() {
      this.$emit('clear-query');
      this.nameQuery = '';
      this.departmentsQuery = [];
      this.salaryQuery = {};
    },
  },
};
</script>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: all 0.3s ease-in-out;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(-15px);

}
.fade-enter-to {
  transform: translateY(0);

}
</style>
