<template>
  <form class="card mb-3 shadow border-0" v-on:submit.prevent="handleSubmit">
    <div class="card-body">
      <h5 class="card-title">Buscar por Endereço</h5>
      <div class="mb-3">
        <label>Estado</label>
        <select required class="form-control" v-model="state" :disabled="loading">
          <option value="AC">Acre</option>
          <option value="AL">Alagoas</option>
          <option value="AP">Amapá</option>
          <option value="AM">Amazonas</option>
          <option value="BA">Bahia</option>
          <option value="CE">Ceará</option>
          <option value="DF">Distrito Federal</option>
          <option value="ES">Espírito Santo</option>
          <option value="GO">Goiás</option>
          <option value="MA">Maranhão</option>
          <option value="MT">Mato Grosso</option>
          <option value="MS">Mato Grosso do Sul</option>
          <option value="MG">Minas Gerais</option>
          <option value="PA">Pará</option>
          <option value="PB">Paraíba</option>
          <option value="PR">Paraná</option>
          <option value="PE">Pernambuco</option>
          <option value="PI">Piauí</option>
          <option value="RJ">Rio de Janeiro</option>
          <option value="RN">Rio Grande do Norte</option>
          <option value="RS">Rio Grande do Sul</option>
          <option value="RO">Rondônia</option>
          <option value="RR">Roraima</option>
          <option value="SC">Santa Catarina</option>
          <option value="SP">São Paulo</option>
          <option value="SE">Sergipe</option>
          <option value="TO">Tocantins</option>
          <option value="EX">Estrangeiro</option>
        </select>
      </div>
      <div class="mb-3">
        <label>Cidade</label>
        <select required class="form-control" v-model="city" :disabled="loading">
          <option v-for="(cityOption, cityOptionIndex) in cityOptions" :key="`city_${cityOptionIndex}`" :value="cityOption['nome']">{{cityOption['nome']}}</option>
        </select>
      </div>
      <div class="mb-3">
        <label>Rua</label>
        <input required type="text" placeholder="Alfredo da Silva..." v-model="adress" class="form-control">
        <small class="form-help">Digite apenas o nome da Rua, sem número nem o termo "Rua"</small>
      </div>
      <button class="btn btn-primary mt-3 w-100">Pesquisar</button>
      <button class="btn btn-outline-secondary btn-sm my-1 w-100" type="button" v-on:click="handleReturn">Voltar</button>
    </div>
  </form>
</template>

<script>
export default {
  name: 'BuscarEndereco',
  data: () => ({
    loading: false,
    state: '',
    cityOptions: [],
    city: '',
    adress: '',
  }),
  methods: {
    handleSubmit() {
      this.$emit('submit', {city: this.city, state: this.state, adress: this.adress});
    },
    handleReturn() {
      this.$emit('return');
    }
  },
  watch: {
    state(newVal) {
      this.loading = true;
      fetch(`https://servicodados.ibge.gov.br/api/v1/localidades/estados/${newVal}/distritos?orderBy=nome`).then((response) => (response.json())).then((data) => {
        console.log(data);
        this.cityOptions = data.sort();
      }).catch((ex) => {
        console.log(ex.message);
      }).finally(() => {
        this.loading = false;
      });
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
