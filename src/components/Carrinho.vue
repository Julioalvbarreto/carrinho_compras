<script setup>
import { ref, onMounted, watch } from 'vue';
import { useRouter } from 'vue-router';

const router = useRouter();

const carrinho = ref([]);
const showModal = ref(false);
const total = ref(0);
const confirmacaoLimpar = ref(false);
const confirmacaoPedido = ref(false);


  const fecharModalERedirecionar = () => {
    showModal.value = false;
    router.push('/');
  };

  const verificarCarrinhoVazio = () => {
    if (carrinho.value.length == 0) {
      showModal.value = true;
    }
  };

  function zerarCarrinho(){
    carrinho.value = []; 
    localStorage.setItem('carrinho', JSON.stringify(carrinho.value));
    alert('Você será redirecionado para lista de produtos.')
    router.push('/');
  }

  function finalizarPedido(){
    zerarCarrinho();
  }

  watch(carrinho, () => {
    verificarCarrinhoVazio();
  });

  function carregarCarrinho() {
    const carrinhoSalvo = JSON.parse(localStorage.getItem('carrinho')) || [];
    carrinho.value = carrinhoSalvo;
    total.value = 0;
    for (let index = 0; index < carrinho.value.length; index++) {
      total.value += carrinho.value[index].subtotal;
    };
    
  }

  function incrementarQuantidade(produto) {
    produto.quantidade += 1;
    produto.subtotal = produto.preco * produto.quantidade;
    total.value = 0;
    for (let index = 0; index < carrinho.value.length; index++) {
      total.value += carrinho.value[index].subtotal;
    };
    salvarCarrinho();
  }

  function decrementarQuantidade(produto) {
    if (produto.quantidade > 0) {
      produto.quantidade -= 1;
      produto.subtotal = produto.preco * produto.quantidade;
      total.value = 0;
      for (let index = 0; index < carrinho.value.length; index++) {
        total.value += carrinho.value[index].subtotal;
      };
      if (produto.quantidade === 0) {
        removerProdutoCarrinho(produto);
      } else {
        salvarCarrinho();
      }
    }
  }

  function removerProdutoCarrinho(produto) {
    const carrinhoSalvo = carrinho.value.filter(item => item.nome !== produto.nome);
    localStorage.setItem('carrinho', JSON.stringify(carrinhoSalvo));
    carrinho.value = carrinhoSalvo;
  }


  function salvarCarrinho() {
    localStorage.setItem('carrinho', JSON.stringify(carrinho.value));
  }

  onMounted(() => {
    carregarCarrinho();
  });
</script>

<template>
  <div class="container mt-4 d-flex justify-content-center flex-column align-items-center shadow col-12 col-md-12">
    <div v-if="showModal" class="modal show" style="display: block;" tabindex="-1" role="dialog">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Carrinho Vazio</h5>
          </div>
          <div class="modal-body">
            <p>Seu carrinho está vazio. Você será redirecionado para a página inicial.</p>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" @click="fecharModalERedirecionar()">Fechar</button>
          </div>
        </div>
      </div>
    </div>
  
    <div v-if="carrinho.length > 0" class="mt-5">
        <h5>Carrinho de Compras:</h5>
        <ul>
          <li v-for="(produto, index) in carrinho" :key="index">
            <img :src="produto.imagem" alt="Produto" style="width: 50px; height: auto;">
            {{ produto.nome }} - R$ {{ produto.preco }} (x{{ produto.quantidade }}) 
            - <strong>Subtotal R$ {{ (produto.preco * produto.quantidade).toFixed(2) }}</strong>
            <button class="btn btn-sm btn-success btn-menor" @click="incrementarQuantidade(produto)">+</button>
            <button class="btn btn-sm btn-danger btn-menor" @click="decrementarQuantidade(produto)">-</button>
          </li>
        </ul>
    </div>
    <div>
      <h6><strong> Total = R$ {{ total.toFixed(2).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',') }}</strong></h6>
    </div>

    <nav class="d-flex gap-2 mb-3">
    <button type="button" class="btn btn-roxo" @click="confirmacaoPedido = true">Finalizar pedido</button>
    <RouterLink to="/" class="btn btn-success">Voltar para listagem</RouterLink>
    <button type="button" class="btn btn-danger" @click="confirmacaoLimpar=true">Limpar carrinho</button>
    </nav>

    <RouterView />

    <div v-if="confirmacaoLimpar" class="modal show" style="display: block;" tabindex="-1" role="dialog">
        <div class="modal-dialog" role="document">
          <div class="modal-content">
            <div class="modal-header">
            </div>
            <div class="modal-body">
              <p>Você tem certeza que deseja limpar todos os itens do carrinho?</p>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" @click="zerarCarrinho()">Sim, Limpar!</button>
              <button type="button" class="btn btn-secondary" @click="confirmacaoLimpar=false">Não</button>
            </div>
          </div>
        </div>
    </div>

    <div v-if="confirmacaoPedido" class="modal show" style="display: block;" tabindex="-1" role="dialog">
        <div class="modal-dialog" role="document">
          <div class="modal-content">
            <div class="modal-header">
            </div>
            <div class="modal-body">
              <p>Seu pedido foi aceito! Em instantes você receberá um e-mail de confirmação com o codigo de rastreamento para sua entrega.</p>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-success" @click="finalizarPedido()">OK!</button>
            </div>
          </div>
        </div>
    </div>

  </div>
</template>