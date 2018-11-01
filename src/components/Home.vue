<template>
  <div class="hello">
    <h1>{{ msg }}</h1>

    <b-container class="bv-example-row">
      <b-row>
        <b-col class="atc_col_left">
          <b-button class="atc_botao-lg" href="#/produto">Produto</b-button>
        </b-col>
        <b-col class="atc_col_right">
          <b-button class="atc_botao-lg" href="#">Categoria</b-button>
        </b-col>
      </b-row>
    </b-container>

  </div>
</template>

<script>
  export default {
    name: 'hello',
    data() {
      return {
        msg: 'Atacadão Sistema de Qualidade!'
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .atc_botao-lg {
    padding: 50px 50px 50px 50px;
  }

  .atc_col_left {
    margin-left: 23%;
  }

  .atc_col_right {
    margin-right: 20%;
  }
</style>
