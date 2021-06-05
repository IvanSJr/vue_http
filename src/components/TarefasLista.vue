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
                :tarefa="tarefa"
                @editar="selecionarTarefaParaEdicao"/>
        </ul>

        <p v-else>Nenhuma tarefa foi criada.</p>
        <TarefasSalvar
            v-if="exibirFormulario"
            :tarefa="tarefaSelecionada"
            @criar="criarTarefa"
            @editar="editarTarefa"/>
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
            exibirFormulario: false,
            tarefaSelecionada: undefined
        }
    },
    created() {
        axios.get(`${config.apiUrl}/tarefas`)
            .then((response) => {
                console.log('GET /tarefas', response)
                this.tarefas = response.data
            })

    },
    methods: {
        criarTarefa(tarefa) {
            axios.post(`${config.apiUrl}/tarefas`, tarefa)
                .then((response) => {
                    console.log('POST /tarefas', response)
                    this.tarefas.push(response.data)
                })
        },
        editarTarefa(tarefa){
            console.log('EDITAR: ', tarefa)
        },
        selecionarTarefaParaEdicao(tarefa) {
            this.tarefaSelecionada = tarefa
            this.exibirFormulario = true
        }
    }
}
</script>
