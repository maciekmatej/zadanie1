<template>
    <table>
      <thead>
        <tr>
          <th>Imię</th>
          <th>Nazwisko</th>
          <th>dział</th>
          <th>wynagrodzenie</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in filteredData" :key="row">
          <!-- <td v-for="data in row" :key="data">{{ data }}</td> -->
          <td >{{ row.imie }}</td>
          <td >{{ row.nazwisko }}</td>
          <td >{{ row.dzial }}</td>
          <td >{{ `${row.wynagrodzenieKwota} ${row.wynagrodzenieWaluta}` }}</td>
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
      let type = this.PRACOWNICY[0].wynagrodzenieWaluta;
      this.PRACOWNICY.forEach((pracownik) => {
        if (pracownik.dzial === dep) {
          salary += parseFloat(pracownik.wynagrodzenieKwota);
          if (pracownik.wynagrodzenieWaluta !== type) {
            type = 'mixed';
          }
        }
      });
      return `${salary} ${type}`;
    },
  },
  computed: {
    total() {
      let total = 0;
      let type = this.PRACOWNICY[0].wynagrodzenieWaluta;
      this.PRACOWNICY.forEach((pracownik) => {
        total += parseFloat(pracownik.wynagrodzenieKwota);
        if (pracownik.wynagrodzenieWaluta !== type) {
          type = 'mixed';
        }
      });
      return `${total} ${type}`;
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
