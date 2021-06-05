<template>
    <div>
        <h1 class="font-weight-light">Lista de Tarefas</h1>
        <ul class="list-group" v-if="tarefas.length > 0">
            <TarefasListaItem
                v-for="tarefa in tarefas"
                :key="tarefa.id"
                :tarefa="tarefa"/>
        </ul>

        <p v-else>Nenhuma tarefa foi criada.</p>
        <TarefasSalvar/>
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
            tarefas: []
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
