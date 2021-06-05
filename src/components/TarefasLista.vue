<template>
    <div>

        <div class="row">
            <div class="col-sm-10">
                <h1 class="font-weight-light">Lista de Tarefas</h1>
            </div>
            <div class="col-sm-2">
                <button class="btn btn-primary float-right" @click="exibirFormulario = !exibirFormulario">
                    <i class="fa fa-plus mr-2">
                        <span>Criar</span>
                    </i>
                </button>
            </div>
        </div>

        <ul class="list-group" v-if="tarefas.length > 0">
            <TarefasListaItem
                v-for="tarefa in tarefas"
                :key="tarefa.id"
                :tarefa="tarefa"/>
        </ul>

        <p v-else>Nenhuma tarefa foi criada.</p>
        <TarefasSalvar
            v-if="exibirFormulario"/>
    </div>
</template>

<script>
import axios from 'axios'

import config from './../config/config'
import TarefasSalvar from './TarefasSalvar.vue'
import TarefasListaItem from './TarefasListaItem.vue'

export default{
    components: {
        TarefasSalvar,
        TarefasListaItem
    },
    data() {
        return {
            tarefas: [],
            exibirFormulario: false
        }
    },
    created() {
        axios.get(`${config.apiUrl}/tarefas`)
            .then((response) => {
                console.log(response)
                this.tarefas = response.data
            })

    }
}
</script>
