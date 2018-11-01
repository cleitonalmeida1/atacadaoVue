<template>
  <b-container fluid>
    <b-form @submit="salvarProduto" @reset="onReset">
      <b-alert v-show="sucesso" show variant="primary">Operação Realizada com Sucesso!</b-alert>
      <b-alert v-show="erro" show variant="danger">{{msgErro}}</b-alert>
      <b-row>
        <b-col lg="6" md="6" sm="12">
          <b-row>
            <b-col>
              <b-form-group id="produto"
                            label="Nome do Produto:"
                            label-for="produtoInput">
                <b-form-input id="produtoInput" sm="5"
                              type="text"
                              v-model="produto.nome"
                              required
                              placeholder="Digite o nome do produto">
                </b-form-input>
              </b-form-group>
            </b-col>
          </b-row>

          <b-row>
            <b-col>
              <b-form-group id="preco"
                            label="Preço do Produto:"
                            label-for="precoInput">
                <b-form-input id="precoInput"
                              type="number"
                              v-model="produto.preco"
                              required
                              placeholder="Digite o Preço">
                </b-form-input>
              </b-form-group>
            </b-col>
          </b-row>

          <b-row>
            <b-col>
              <b-button type="submit" variant="primary">Submit</b-button>
              <b-button type="reset" variant="danger">Reset</b-button>
            </b-col>
          </b-row>
        </b-col>

        <!--Tabela -->
        <b-col lg="6">
          <b-row>

            <b-table show-empty
                     stacked="md"
                     :items="items"
                     :fields="fields"
                     :current-page="currentPage"
                     :per-page="perPage"
                     :filter="filter"
                     :sort-by.sync="sortBy"
                     :sort-desc.sync="sortDesc"
                     :sort-direction="sortDirection"
                     @filtered="onFiltered"
            >
              <template slot="name" slot-scope="row">{{row.value.first}} {{row.value.last}}</template>
              <template slot="isActive" slot-scope="row">{{row.value?'Yes :)':'No :('}}</template>
              <template slot="actions" slot-scope="row">
                <!-- We use @click.stop here to prevent a 'row-clicked' event from also happening -->
                <b-button size="sm" @click.stop="info(row.item, row.index, $event.target)" class="mr-1">
                  Info modal
                </b-button>
                <b-button size="sm" @click.stop="row.toggleDetails">
                  {{ row.detailsShowing ? 'Hide' : 'Show' }} Details
                </b-button>
              </template>
              <template slot="row-details" slot-scope="row">
                <b-card>
                  <ul>
                    <li v-for="(value, key) in row.item" :key="key">{{ key }}: {{ value}}</li>
                  </ul>
                </b-card>
              </template>
            </b-table>

            <b-row>
              <b-col md="6" class="my-1">
                <b-pagination :total-rows="totalRows" :per-page="perPage" v-model="currentPage" class="my-0"/>
              </b-col>
            </b-row>

            <!-- Info modal -->
            <b-modal id="modalInfo" @hide="resetModal" :title="modalInfo.title" ok-only>
              <pre>{{ modalInfo.content }}</pre>
            </b-modal>

          </b-row>
        </b-col>

      </b-row>
    </b-form>
  </b-container>
</template>

<script>
  const items = [
    {isActive: true, age: 40, name: {first: 'Dickerson', last: 'Macdonald'}},
    {isActive: false, age: 21, name: {first: 'Larsen', last: 'Shaw'}},
    {
      isActive: false,
      age: 9,
      name: {first: 'Mini', last: 'Navarro'},
      _rowVariant: 'success'
    },
    {isActive: false, age: 89, name: {first: 'Geneva', last: 'Wilson'}},
    {isActive: true, age: 38, name: {first: 'Jami', last: 'Carney'}},
    {isActive: false, age: 27, name: {first: 'Essie', last: 'Dunlap'}},
    {isActive: true, age: 40, name: {first: 'Thor', last: 'Macdonald'}},
    {
      isActive: true,
      age: 87,
      name: {first: 'Larsen', last: 'Shaw'},
      _cellVariants: {age: 'danger', isActive: 'warning'}
    },
    {isActive: false, age: 26, name: {first: 'Mitzi', last: 'Navarro'}},
    {isActive: false, age: 22, name: {first: 'Genevieve', last: 'Wilson'}},
    {isActive: true, age: 38, name: {first: 'John', last: 'Carney'}},
    {isActive: false, age: 29, name: {first: 'Dick', last: 'Dunlap'}}
  ];

  export default {
    name: "produto",
    data() {
      return {
        msgErro: '',
        produto: {
          nome: '',
          preco: ''
        },
        sucesso: false,
        erro: false,
        items: items,
        fields: [
          {key: 'name', label: 'Person Full name', sortable: true, sortDirection: 'desc'},
          {key: 'age', label: 'Person age', sortable: true, 'class': 'text-center'},
          {key: 'isActive', label: 'is Active'},
          {key: 'actions', label: 'Actions'}
        ],
        currentPage: 1,
        perPage: 5,
        totalRows: items.length,
        pageOptions: [5, 10, 15],
        sortBy: null,
        sortDesc: false,
        sortDirection: 'asc',
        filter: null,
        modalInfo: {title: '', content: ''}
      }
    },
    methods: {
      onReset(evt) {
        evt.preventDefault();
        /* Reset our form values */
        this.produto.nome = '';
        this.produto.preco = '';
        this.sucesso = false;
      },
      salvarProduto(evt) {
        evt.preventDefault();
        this.$http.post('http://localhost:8080/produtos', this.produto).then(response => {
          this.sucesso = true;
        }, response => {
          this.msgErro = response.body.msg;
          this.erro = true;
        });
      },
      info(item, index, button) {
        this.modalInfo.title = `Row index: ${index}`
        this.modalInfo.content = JSON.stringify(item, null, 2)
        this.$root.$emit('bv::show::modal', 'modalInfo', button)
      },
      resetModal() {
        this.modalInfo.title = ''
        this.modalInfo.content = ''
      },
      onFiltered(filteredItems) {
        // Trigger pagination to update the number of buttons/pages due to filtering
        this.totalRows = filteredItems.length
        this.currentPage = 1
      }
    }
  }
</script>

<style scoped>
  div {
    text-align: left;
  }
</style>
