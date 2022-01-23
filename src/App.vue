<template>
  <div id="app">
    <BuscarCEP
    v-on:submit="searchByCep"
    v-on:return="handleReturn"
    v-if="currentPage == 'SEARCHBYCEP'"
    ></BuscarCEP>

    <BuscarEndereco
    v-on:submit="searchByAdress"
    v-on:return="handleReturn"
    v-if="currentPage == 'SEARCHBYADRESS'"
    ></BuscarEndereco>

    <div class="card mb-3 shadow border-0" v-if="currentPage=='INDEX'">
      <form class="card-body" v-on:submit.prevent="search">
        <h5 class="card-title mb-3 text-primary">CEP x IBGE API consumer</h5>
        <div class="mb-1">
          <button class="btn btn-primary w-100" type="button" v-on:click="setCurrentPage('SEARCHBYCEP')">Buscar pelo CEP</button>
        </div>
        <div class="mb-1">
          <button class="btn btn-primary w-100" type="button" v-on:click="setCurrentPage('SEARCHBYADRESS')">Buscar pelo Endereço</button>
        </div>
        <div class="mb-1">
          <button class="btn btn-outline-secondary btn-sm mt-3" type="button" data-bs-toggle="modal" data-bs-target="#modalSaberMais">Saber Mais</button>
        </div>
      </form>
    </div>

    <div class="my-3">
      <h5 v-show="results.length == 0" class="text-muted">
        Nenhuma busca foi feita ainda
      </h5>
      <div class="card shadow border-primary my-3" v-for="(result, resultIndex) in results" :key="`result_${resultIndex}`" >
        <i class="my-2 text-primary">{{result.query||'-'}}</i>
        <table class="table table-bordered card-body mb-0 rounded" v-if="result.type == 'ADRESS'">
          <thead>
          <tr>
            <th>CEP</th>
            <th>Logradouro</th>
            <th>Bairo</th>
            <th>IBGE</th>
          </tr>
          </thead>
          <tbody>
            <tr v-for="(value, index) in result" :key="`result_${index}`">
              <td class="px-5">{{value.cep||'-'}}</td>
              <td class="px-5">{{value.logradouro||'-'}}</td>
              <td class="px-5">{{value.bairro||'-'}}</td>
              <td class="px-5">{{value.ibge||'-'}}</td>
            </tr>
          </tbody>
        </table>
        <table class="table table-bordered card-body mb-0 rounded" v-else>
          <thead>
          <tr>
            <th>Logradouro</th>
            <th>Complemento</th>
            <th>Cidade</th>
            <th>UF</th>
          </tr>
          </thead>
          <tbody>
            <tr>
              <td class="px-5">{{result.logradouro||'-'}}</td>
              <td class="px-5">{{result.complemento||'-'}}</td>
              <td class="px-5">{{result.localidade||'-'}}</td>
              <td class="px-5">{{result.uf||'-'}}</td>
            </tr>
          </tbody>
        </table>
        <button class="btn btn-outline-danger border-0 rounded-0 btn-sm" v-on:click="results.pop(resultIndex)">
          Apagar
        </button>
      </div>
    </div>

    <div class="modal fade" id="modalSaberMais" tabindex="-1" aria-labelledby="modalSaberMaisLabel" aria-hidden="true">
      <div class="modal-dialog modal-lg">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="modalSaberMaisLabel">Saber Mais</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body text-start">
            <p>Este é um projeto realizado para a cadeira de <b>Sistemas Distribuídos</b> da <a target="_blank" href="https://www.furg.br/"><b>FURG (Universidade Federal de Rio Grande)</b></a> pelo aluno <a target="_blank" href="https://portifolio.iagoalmeida1.repl.co/"><b>Iago Borba de Almeida</b></a>.</p>
            <p>A aplicação armazena o resultado de todas as suas buscas para que futuras consultas do mesmo endereço ou CEP não exijam uma nova chamada as APIs.</p>
            <p>Estão sendo utilizadas duas APIs para execução da aplicação, sendo elas:</p>
            <table class="table table-bordered">
              <thead>
                <tr>
                  <th>BaseURL</th>
                  <th>Função</th>
                </tr>
                <tr>
                  <td><a target="_blank" href="https://viacep.com.br/">https://viacep.com.br/ws</a></td>
                  <td>Para encontrar os CEPS a partir do Estado, Cidade e Logradouro e também o Estado, Cidade e Logradouro a partir do CEP</td>
                </tr>
                <tr>
                  <td><a target="_blank" href="https://servicodados.ibge.gov.br/api/docs/localidades">https://servicodados.ibge.gov.br/api</a></td>
                  <td>Para encontrar as Cidades a partir do Estado.</td>
                </tr>
              </thead>
            </table>
            <div class="p-3 rounded bg-dark text-light font-monospace">
<pre>
fetch(`${this.baseURl}/${cep}/json/`).then((response) => (response.json())).then((data) => {
  this.results.push({
    type: 'CEP',
    query: `${cep}`,
    ...data
  });
}).catch((ex) => {
  console.log(ex.message);
}).finally(() => {
  this.loading = false;
});
</pre>
            </div>
            <p><small class="text-muted m-0">Exemplo de requisição para obter os dados</small></p>
          </div>
        </div>
      </div>
    </div>
  </div>

    
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import BuscarCEP from './components/BuscarCEP.vue'
import BuscarEndereco from './components/BuscarEndereco.vue'

const PAGES = {
  INDEX: 'INDEX',
  SEARCHBYCEP: 'SEARCHBYCEP',
  SEARCHBYADRESS: 'SEARCHBYADRESS'
};

export default {
  name: 'App',
  components: {
    BuscarCEP,
    BuscarEndereco
  },
  data:() => ({
    currentPage: PAGES.INDEX,
    // HelloWorld
    loading: false,
    baseUrl: 'https://viacep.com.br/ws',
    results: []
  }),
  created() {
    const localResults = localStorage.getItem('results');
    if(localResults) {
      this.results = JSON.parse(localResults);
    }
  },
  methods: {
    searchByCep(cep) {
      console.log(cep);
      fetch(`${this.baseUrl}/${cep}/json/`).then((response) => (response.json())).then((data) => {
        this.results.push({
          type: 'CEP',
          query: `${cep}`,
          ...data
        });
      }).catch((ex) => {
        console.log(ex.message);
      }).finally(() => {
        this.loading = false;
      });
    },
    searchByAdress(data) {
      console.log(data)
      const { state, city, adress } = data;
      fetch(`${this.baseUrl}/${state}/${city}/${adress}/json/`).then((response) => (response.json())).then((data) => {
        this.results.push({
          type: 'ADRESS',
          query: `${adress} - ${city}, ${state}`,
          ...data
        });
      }).catch((ex) => {
        console.log(ex.message);
      }).finally(() => {
        this.loading = false;
      });
    },
    handleReturn() {
      this.currentPage = PAGES.INDEX;
    },
    setCurrentPage(pageName) {
      this.currentPage = pageName;
    }
  },
  watch: {
    results(newVal) {
      localStorage.setItem('results', JSON.stringify(newVal));
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.card {
  min-width: 400px;
}
</style>
