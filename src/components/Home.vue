<script setup>
import { ref, onMounted, watch } from 'vue';

const carrinho = ref([]);
const verMais = ref(false);
const data = ref([]);
const loading = ref(true);
const error = ref(null);

  const fetchData = async () => {
    try {
      const response = await fetch('http://localhost:3000/produtos');
      if (!response.ok) {
        throw new Error('Falha na requisição');
      }
      const result = await response.json();
      data.value = result;
    } catch (err) {
      error.value = err.message;
    } finally {
      loading.value = false;
    }
  };

  function carregarMais() {
    verMais.value = !verMais.value;
  }

  function salvarProdutos(produto) {
    const carrinhoSalvo = JSON.parse(localStorage.getItem('carrinho')) || [];
    const indexProduto = carrinhoSalvo.findIndex(item => item.nome === produto.nome);

    if (indexProduto !== -1) {
      carrinhoSalvo[indexProduto].quantidade += 1;
    } else {
      produto.quantidade = 1;
      produto.subtotal = produto.preco * produto.quantidade;
      carrinhoSalvo.push(produto);
    }

    localStorage.setItem('carrinho', JSON.stringify(carrinhoSalvo));

    carrinho.value = carrinhoSalvo;

    alert('Produto adicionado ao carrinho!');
  }

onMounted(fetchData);
</script>

<template>
  <div class="container mt-3 d-flex justify-content-center flex-column align-items-center shadow col-4 col-md-4">

    
  
    <div v-if="!loading && !error" class="card mb-0 mt-2" style="width: 340px;">

      <div v-for="produto in data.slice(0, 3)" :key="produto.id" class="row no-gutters">
        <div class="col-4 col-md-4">
          <img :src="'./src/assets/image/' + produto.imagem" class="card-img" :alt="produto.imagem">
        </div>
        <div class="col-8 col-md-8">
          <div class="card-body">
            <h6 class="card-title text-truncate">{{ produto.nome }}</h6>
            <p>( {{ produto.avaliacoe }} avaliações )</p>
            <p class="card-text"><strong>R$ {{ produto.preco.toFixed(2) }}</strong></p>
            <button type="button" class="btn btn-roxo" @click="salvarProdutos({
                nome: produto.nome,
                preco: produto.preco.toFixed(2),
                imagem: './src/assets/image/' + produto.imagem
              })">Adicionar ao Carrinho
            </button>
          </div>
        </div>
      </div>

      <div v-if="verMais">
        <div  v-for="produto in data.slice(3)" :key="produto.id" class="row no-gutters">
              <div class="col-4 col-md-4">
                <img :src="'./src/assets/image/' + produto.imagem" class="card-img" :alt="produto.imagem">
              </div>
              <div class="col-8 col-md-8">
                <div class="card-body">
                  <h6 class="card-title text-truncate">{{ produto.nome }}</h6>
                  <p>( {{ produto.avaliacoe }} avaliações )</p>
                  <p class="card-text"><strong>R$ {{ produto.preco.toFixed(2) }}</strong></p>
                  <button type="button" class="btn btn-roxo" @click="salvarProdutos({
                      nome: produto.nome,
                      preco: produto.preco.toFixed(2),
                      imagem: './src/assets/image/' + produto.imagem
                    })">Adicionar ao Carrinho
                  </button>
                </div>
              </div>
        </div>
      </div>

    </div>
  
    <nav class="d-flex gap-2 mb-3">
      <button type="button" class="btn btn-roxo" @click="carregarMais">{{ verMais ? 'Ver menos' : 'Carregar mais produtos' }}</button>
      <RouterLink to="/carrinho" class="btn btn-success">Ir para Carrinho</RouterLink>
    </nav>

    <RouterView />

  </div>



</template>