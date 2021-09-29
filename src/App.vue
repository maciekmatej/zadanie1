<template>
  <div class="container">
    <!-- search bar -->
    <search-bar
    :departments="departments"
    @name-update="nameUpdate"
    @dep-update="departmentsUpdate"
    @salary-update="salaryUpdate"
    @clear-query="clearQuery" />

    <!-- table -->
    <app-table
    :filteredData="filteredData"
    :departments="departments"
    :PRACOWNICY="PRACOWNICY" />

    <div class="new-user-wrapper">
      <button @click="toggleAdd" type="button" class="toggle-add">Nowy pracownik</button>
      <!-- add new user -->
      <transition name="fade">
        <app-new-user
        v-show="showNewUser"
        @add-new="addNewUser" />
      </transition>
    </div>
  </div>
</template>

<script>
import SearchBar from './components/SearchBar.vue';
import AppTable from './components/Table.vue';
import AppNewUser from './components/NewUser.vue';

export default {
  data() {
    return { // PRACOWNICY is a original given data
      PRACOWNICY: [
        {
          imie: 'Jan', nazwisko: 'Kowalski', dzial: 'IT', wynagrodzenieKwota: '3000', wynagrodzenieWaluta: 'PLN',
        },
        {
          imie: 'Anna', nazwisko: 'Bąk', dzial: 'Administracja', wynagrodzenieKwota: '2400.50', wynagrodzenieWaluta: 'PLN',
        },
        {
          imie: 'Paweł', nazwisko: 'Zabłocki', dzial: 'IT', wynagrodzenieKwota: '3300', wynagrodzenieWaluta: 'PLN',
        },
        {
          imie: 'Tomasz', nazwisko: 'Osiecki', dzial: 'Administracja', wynagrodzenieKwota: '2100', wynagrodzenieWaluta: 'PLN',
        },
        {
          imie: 'Iwona', nazwisko: 'Leihs-Gutowska', dzial: 'Handlowiec', wynagrodzenieKwota: '3100', wynagrodzenieWaluta: 'PLN',
        },
      ],
      rows: [],
      departments: [],
      nameQuery: '',
      departmentsQuery: [],
      salaryQuery: {
        min: null,
        max: null,
      },
      showNewUser: false,

    };
  },
  components: {
    SearchBar,
    AppTable,
    AppNewUser,
  },
  methods: {
    getRows() { // fetching the data to rows array
      const arr = this.PRACOWNICY;
      this.rows = [...arr];
    },
    getDepartments() { // fetching all unique departments to generate query
      this.PRACOWNICY.forEach((row) => {
        this.departments.push(row.dzial);
      });
      this.departments = [...new Set(this.departments)];
    },
    nameUpdate(value) {
      this.nameQuery = value;
    },
    departmentsUpdate(value) {
      this.departmentsQuery = value;
    },
    salaryUpdate(value) {
      this.salaryQuery = value;
    },
    toggleAdd() { // toggle new user form
      this.showNewUser = !this.showNewUser;
      if (this.showTextInput === true) {
        this.showTextInput = false;
      }
    },
    addNewUser(values) {
      this.PRACOWNICY.push(values); // adding new user to data
      this.getRows(); // updating rows array with new data
      this.getDepartments(); // updating list of departments for query
      this.showNewUser = false;
    },
    clearQuery() {
      this.nameQuery = '';
      this.departmentsQuery = [];
      this.salaryQuery = {};
    },

  },
  computed: {
    filteredData() { // here are all the queries that can be inputed by user
      return this.rows.filter((pracownik) => {
        const name = pracownik.imie.toLowerCase();
        const department = pracownik.dzial.toLowerCase();
        const salary = parseFloat(pracownik.wynagrodzenieKwota);

        const salaryQuery = () => {
          const minSalQ = this.salaryQuery.min;
          const maxSalQ = this.salaryQuery.max;

          if (!this.salaryQuery.min && !this.salaryQuery.max) { // filters the list when there is no query values
            return true;
          }
          if ((!maxSalQ) && salary >= minSalQ) { // filters the list when only  minimum salary value is passed
            return true;
          }
          if ((!minSalQ) && salary <= maxSalQ) { // filters the list when only maximum salary value is passed
            return true;
          }
          if ((salary >= minSalQ) && (salary <= maxSalQ)) { // filters the list when both min and max values are passed
            return true;
          }
          return false;
        };
        const nameQ = this.nameQuery.toLowerCase();
        const depQ = this.departmentsQuery;
        const result = () => { // searching for users that have at least one element from query
          if (depQ.length < 1) {
            return true;
          }
          return depQ.some((dep) => dep.toLowerCase() === department);
        };

        return name.includes(nameQ) && result() && salaryQuery();
      });
    },

  },
  created() { // this is to simulate fetching data on load from PRACOWNICY array
    this.getRows();
    this.getDepartments();
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
