<template>
    <div id="burger-table">
    <Message :msg="msg" v-show="msg" />

        <div>
            <div id="burger-table-heading">
                <div class="order-id">#:</div>
                <div>cliente:</div>
                <div>Pão:</div>
                <div>Carne:</div>
                <div>Opcionais:</div>
                <div>Ações:</div>
            </div>
        </div>
        <div id="burger-table-rows">
            <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
                <div class=".order-number">{{burger.id}}</div>
                <div>{{burger.nome}}</div>
                <div>{{burger.pao}}</div>
                <div>{{burger.carne}}</div>
                <div>
                    <ul v-for="(opcional, index) in burger.opcionais" :key="index">
                        <li >{{opcional}}</li>
                    </ul>
                </div>
                <select name="status" class="status" @change="updateBurger($event, burger.id)">
                    <option value="">Selecione</option>
                    <option v-for="s in status" :key="s.id" :value="s.tipo" :selected="burger.status == s.tipo">
                        {{s.tipo}}
                    </option>
                </select>
                <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
            </div>
        </div>
    </div>
</template>

<script>
import Message from './Message.vue';

    export default {
        name: "Dashboard",
        data(){
            return{
            burgers: null,
            burger_id: null,
            status: [],
            msg: null
        }
          

    },
    components: { 
        Message
    },
    methods: {
        async getPedidos() {
            const req = await fetch("http://localhost:3000/burgers");

            const data= await req.json();

            this.burgers = data;

            console.log(this.burgers);

            //Resgatar os status
            this.getStatus();
        },
        async getStatus(){
            const req= await fetch("http://localhost:3000/status");

            const data = await req.json();

            this.status = data;
        },
        async deleteBurger(id) {
            const req = await fetch(`http://localhost:3000/burgers/${id}`, {method: "DELETE"});
            const res= await req.json();

             // colocar uma mensagem de sistema
            this.msg = `Pedido Nº ${id} removido com sucesso`;

            // limpar a mensagem da tela
            setTimeout(() => this.msg = "", 3000);

            //mensagem de deletado
            this.getPedidos();
        },
        async updateBurger(event, id){
            const option = event.target.value;
            const dataJson= JSON.stringify({status: option});
            const req= await fetch(`http://localhost:3000/burgers/${id}`, {
            method: "PATCH", 
            headers: {"Content-Type": "application/json"},
            body: dataJson
            });
            const res= await req.json();

             // colocar uma mensagem de sistema
            this.msg = `Pedido Nº ${res.id} foi atualizado para o status ${res.status}!`;

            // limpar a mensagem da tela
            setTimeout(() => this.msg = "", 3000);

            console.log(res);
        }
    },
    mounted(){
        this.getPedidos();
    }
}
</script>
