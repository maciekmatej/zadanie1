<template>
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
        <br/>
        <br/>
        <tr>
          <td colspan="5">suma wszystkich wynagrodzen: {{ total }}</td>
        </tr>
        <tr>
          <td colspan="5">suma wynagrodzen dla wyników wyszukiwania: {{ totalDepartment }}</td>
        </tr>

        <tr v-for="department in departments" :key="department">
          <td colspan="5">wynagrodzenia działu {{ department }}: {{ totalForEachDep(department) }}</td>
        </tr>
      </tbody>
    </table>
</template>

<script>
export default {
  name: 'Table',
  props: ['filteredData', 'PRACOWNICY', 'departments'],
  methods: {
    totalForEachDep(dep) {
      let salary = 0;
      this.PRACOWNICY.forEach((pracownik) => {
        if (pracownik.dzial === dep) {
          salary += parseFloat(pracownik.wynagrodzenieKwota);
        }
      });
      return salary;
    },
  },
  computed: {
    cols() { // builds table heads off of data
      const cols = Object.keys(this.PRACOWNICY[0]);
      return cols;
    },
    total() {
      let total = 0;
      this.PRACOWNICY.forEach((pracownik) => {
        total += parseFloat(pracownik.wynagrodzenieKwota);
      });
      return total;
    },
    totalDepartment() { // this function counts total salary for current query
      let totalDep = 0;
      this.filteredData.forEach((pracownik) => {
        totalDep += parseFloat(pracownik.wynagrodzenieKwota);
      });
      return totalDep;
    },
  },
};
</script>
