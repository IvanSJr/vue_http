<template>
    <div>

        <div class="row">
            <div class="col-sm-10">
                <h1 class="font-weight-light">Lista de Tarefas</h1>
            </div>
            <div class="col-sm-2">
                <button class="btn btn-primary float-right" @click="exibirFormularioCriarTarefa">
                    <i class="fa fa-plus mr-2">
                        <span>Criar</span>
                    </i>
                </button>
            </div>
        </div>

        <ul class="list-group" v-if="tarefas.length > 0">
            <TarefasListaItem
                v-for="tarefa in tarefasOrdenadas"
                :key="tarefa.id"
                :tarefa="tarefa"
                @editar="selecionarTarefaParaEdicao"
                @deletar="deletarTarefa"
                @concluir="editarTarefa"/>
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
    computed: {
        tarefasOrdenadas() {
            return this.tarefas.slice().sort((t1, t2) => {
                if (t1.concluido === t2.concluido){
                    return t1.titulo < t2.titulo
                        ? -1 
                        : t1.titulo > t2.titulo
                            ? 1
                            : 0
                }
                return t1.concluido - t2.concluido
            })
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
        resetar(){
            this.tarefaSelecionada = undefined
            this.exibirFormulario = false
        },
        criarTarefa(tarefa) {
            axios.post(`${config.apiUrl}/tarefas`, tarefa)
                .then((response) => {
                    console.log('POST /tarefas', response)
                    this.tarefas.push(response.data)
                    this.resetar()
                })
        },
        editarTarefa(tarefa){
            console.log('EDITAR: ', tarefa)
            axios.put(`${config.apiUrl}/tarefas/${tarefa.id}`, tarefa)
                .then(response => {
                    console.log(`PUT /tarefas/${tarefa.id}`, response)
                    const indice = this.tarefas.findIndex(t => t.id === tarefa.id)
                    this.tarefas.splice(indice, 1, tarefa)
                    this.resetar()
                })
        },        
        deletarTarefa(tarefa){
            const confirmar = window.confirm(`Você realmente deseja deletar a tarefa "${tarefa.titulo}"?`)
            if (confirmar){
                axios.delete(`${config.apiUrl}/tarefas/${tarefa.id}`)
                    .then(response => {
                        console.log(`DELETE /tarefas/${tarefa.id}`, response)
                        const indice = this.tarefas.findIndex(t => t.id === tarefa.id)
                        this.tarefas.splice(indice, 1)
                    })
            }
        },
        selecionarTarefaParaEdicao(tarefa) {
            this.tarefaSelecionada = tarefa
            this.exibirFormulario = true
        },
        exibirFormularioCriarTarefa(){
            if(this.tarefaSelecionada) {
                this.tarefaSelecionada = undefined
                return
            }
            this.exibirFormulario = !this.exibirFormulario
        },
    }
}
</script>
