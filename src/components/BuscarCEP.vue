<template>
  <form class="card mb-3 shadow border-0" v-on:submit.prevent="handleSubmit">
    <div class="card-body">
      <h5 class="card-title">Buscar por CEP</h5>
      <div class="mb-3">
        <label for="buscaCEPinput">CEP</label>
        <input type="text" name="buscaCEPinput" class="form-control" v-model="searchQuery" minlength="9" maxlength="9" placeholder="0000000-00" required>
      </div>
      <button class="btn btn-primary w-100">Pesquisar</button>
      <button class="btn btn-outline-secondary btn-sm my-1 w-100" type="button" v-on:click="handleReturn">Voltar</button>
    </div>
  </form>
</template>

<script>
export default {
  name: 'BuscarCEP',
  data: () => ({
    searchQuery: ''
  }),
  methods: {
    handleSubmit() {
      this.$emit('submit', this.searchQuery);
    },
    handleReturn() {
      this.$emit('return');
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
