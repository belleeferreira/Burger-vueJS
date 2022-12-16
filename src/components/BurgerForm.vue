<template>
  <div>
    <Message :msg="msg" v-show="msg" />
    <div>
      <form id="burger-form" @submit="createBurger">
        <div class="input-container">
          <label for="nome">Me diz teu nome</label>
          <input type="text" id="nome" v-model="nome" placeholder="Digite seu nome"/>
        </div>

        <div class="input-container">
          <label for="pao">Que pão tu quer?</label>
          <select name="pao" id="pao" v-model="pao">
            <option value="">Selecione o seu pão </option>
            <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">{{pao.tipo}}</option>
          </select>
        </div>

          <div class="input-container">
          <label for="carne">Escolha a tua carne:</label>
          <select name="carne" id="carne" v-model="carne">
            <option value="">Selecione o tipo de carne </option>
            <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">{{carne.tipo}}</option>
          </select>
        </div>

        <div id="opcionais-container" class="input-container">
          <label id="opcionais-title" for="opcionais">O que mais tu quer no teu Burger?</label>
          <div class="checkbox-container" v-for="opcional in opcionaisdata" :key="opcional.id" >
            <input type="checkbox" name="opcionais" v-model="opcionais[opcional.tipo]">
            <span>{{opcional.tipo}}</span>
          </div>
        </div>

        <div class="input-container">
            <input type="submit" class="submit-btn" value="Criar o meu Burger!">
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import Message from './Message.vue';
export default {
  name: "BurgerForm",
  data(){
    return {
        paes: null,
        carnes: null,
        opcionaisdata: null,
        nome: null,
        pao: null,
        carne: null,
        opcionais: [],
        msg: null
    }
  },
  methods: {
    async getIngredientes(){
        const req = await fetch("http://localhost:3000/ingredientes");
        const data = await req.json();
        
        this.paes= data.paes;
        this.carnes= data.carnes;
        this.opcionaisdata = data.opcionais;
},
async createBurger(e){
  e.preventDefault();
//criando objeto
  const data = {
    nome: this.nome,
    carne: this.carne,
    pao: this.pao,
    opcionais: Object.keys(this.opcionais),
    status: "Solicitado"
  }
  //transformando o dado em texto
  const dataJason= JSON.stringify(data);
  //fazendo a requizição. fetch é para usar a api em JS puro.
  const req= await fetch("http://localhost:3000/burgers", {
    method: "POST",
    headers: {"Content-Type": "application/json"},
    body: dataJason
  });
    //ter a resposta de volta
    const res = await req.json();

   // colocar uma mensagem de sistema
   this.msg = `Pedido Nº ${res.id} realizado com sucesso`;

   // limpar a mensagem da tela
   setTimeout(() => this.msg = "", 3000);

   //limpar os campos

   this.nome ="";
   this.carne ="";
   this.pao="";
   this.opcionais="";

}
  },
  mounted(){
    this.getIngredientes()
  },
  components: {
    Message
  }
};
</script>


