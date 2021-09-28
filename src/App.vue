<template>
  <div class="container">

    <!-- search bar -->
    <div class="search-bar">
      <span><i class="fas fa-search"></i></span>
      <input type="text" id="imie" v-model="nameQuery" placeholder="imię">
      <div @click.prevent="showQuery = !showQuery" class="dep-ctn">wybierz dział/y
          <button  class="query-btn">
            <i class="fas fa-chevron-down"
                :class="{'rotate': showQuery}"></i>
          </button>
      </div>
      <transition name="fade">
      <div v-show="showQuery === true" class="checks-ctn">
        <div v-for="department in departments" :key="department" class="checkbox-wrapper">
          <input v-model="departmentsQuery" type="checkbox" :id="department" :value="department">
          <label :for="department">{{ department }}</label>
        </div>
      </div>
      </transition>
      <input v-model="salaryQuery.min" type="number" placeholder="kwota od" id="min" class="min-salary-input">
      <span class="dash">-</span>
      <input v-model="salaryQuery.max" type="number" placeholder="kwota do" class="max-salary-input">
      <button @click.prevent="clearQuery" class="reset-btn">
        <i class="fas fa-undo-alt"></i>
      </button>
    </div>

    <!-- table -->
    <table>
      <thead>
        <tr>
          <th v-for="col in cols" :key="col" :alt="col">{{ col }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in filteredData" :key="row">
          <td v-for="data in row" :key="data">{{ data }}</td>
        </tr>
        <br />
        <br />
        <tr>
          <td colspan="5">suma wszystkich wynagrodzen: {{ total }}</td>
        </tr>
        <tr>
          <td colspan="5">suma wyszukanych wynagrodzen: {{ totalDepartment }}</td>
        </tr>
      </tbody>
    </table>
    <div class="new-user-wrapper">
      <button @click="toggleAdd" type="button" class="toggle-add">Nowy pracownik</button>

      <!-- add new user -->
      <transition name="fade">
        <div v-show="showNewUser" class="newUser">
          <form @submit.prevent="addNewUser">
            <div class="input-wrapper">
              <label for="name">Imię:</label>
              <input v-model="newUser.imie" type="text"
                    name="imie" id="name" placeholder="imię" required>
            </div>
            <div class="input-wrapper">
              <label for="surname">Nazwisko:</label>
              <input v-model="newUser.nazwisko" type="text" name="nazwisko"
                    id="surname" placeholder="nazwisko" required>
            </div>
            <div class="input-wrapper">
            <label for="department">Dział:</label>
            <select v-if="!showTextInput" @change="toggleDepInput($event)"
                    v-model="newUser.dzial" name="dzial" id="department" required>
              <option value="Nowy">dodaj nowy</option>
              <option value="IT">IT</option>
              <option value="Administracja">Administracja</option>
              <option value="Handlowiec">Handlowiec</option>
            </select>

            <div v-else class="hiddenInput">
              <input  type="text" v-model="newUser.dzial" placeholder="wpisz nowy dział..." required>
              <button @click.prevent="showTextInput = false" class="close-input-btn">
                <i class="fas fa-undo-alt"></i>
              </button>
            </div>
            </div>
            <div class="input-wrapper">
              <label for="salary">Wynagrodzenie:</label>
              <input v-model.number="newUser.wynagrodzenieKwota" id="salary" placeholder="kwota"
                    type="number" name="wynagrodzenieKwota" required>
            </div>
            <div class="input-wrapper">
              <label for="currency">Waluta:</label>
              <input v-model="newUser.wynagrodzenieWaluta" id="currency" placeholder="waluta"
                    type="text" name="wynagrodzenieWaluta" required>
            </div>
            <button type="submit" class="add-btn">Dodaj pracownika</button>
          </form>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
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
      showQuery: false,
      showTextInput: false,
      showNewUser: false,
      newUser: {
        imie: '',
        nazwisko: '',
        dzial: '',
        wynagrodzenieKwota: '',
        wynagrodzenieWaluta: '',
      },
    };
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
    clearQuery() {
      this.nameQuery = '';
      this.departmentsQuery = [];
      this.salaryQuery = {};
    },
    toggleAdd() { // toggle new user form
      this.showNewUser = !this.showNewUser;
      if (this.showTextInput === true) {
        this.showTextInput = false;
      }
    },
    addNewUser() {
      this.PRACOWNICY.push(this.newUser); // adding new user to data
      this.getRows(); // updating rows array with new data
      this.getDepartments(); // updating list of departments for query

      this.newUser = {
        imie: '',
        nazwisko: '',
        dzial: '',
        wynagrodzenieKwota: '',
        wynagrodzenieWaluta: '',
      };
      this.showTextInput = false;
      this.showNewUser = false;
    },
    toggleDepInput(e) { // toggling a text input for adding new department
      if (e.target.value === 'Nowy') {
        this.newUser.dzial = '';
        this.showTextInput = !this.showTextInput;
      }
    },
  },
  computed: {
    cols() {
      const cols = Object.keys(this.PRACOWNICY[0]);
      return cols;
    },

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
    total() {
      let total = 0;
      this.PRACOWNICY.forEach((pracownik) => {
        total += parseFloat(pracownik.wynagrodzenieKwota);
      });
      return total;
    },
    totalDepartment() { // this function count total salary for current query
      let totalDep = 0;

      this.filteredData.forEach((pracownik) => {
        totalDep += parseFloat(pracownik.wynagrodzenieKwota);
      });
      return totalDep;
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
