<template>
    <div class="newUser">
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
</template>

<script>
export default {
  data() {
    return {
      newUser: {
        imie: '',
        nazwisko: '',
        dzial: '',
        wynagrodzenieKwota: '',
        wynagrodzenieWaluta: '',
      },
      showTextInput: false,
    };
  },
  name: 'AppNewUser',
  emits: ['add-new'],
  methods: {
    toggleDepInput(e) { // toggling a text input for adding new department
      if (e.target.value === 'Nowy') {
        this.newUser.dzial = '';
        this.showTextInput = !this.showTextInput;
      }
    },
    addNewUser() {
      this.$emit('add-new', this.newUser);
      this.newUser = {
        imie: '',
        nazwisko: '',
        dzial: '',
        wynagrodzenieKwota: '',
        wynagrodzenieWaluta: '',
      };
      this.showTextInput = false;
    },
  },
};
</script>
