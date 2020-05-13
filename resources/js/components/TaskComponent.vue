<template>
    <div class="container">
        <add-task @task-added="refresh"></add-task>
        
            <ul class="list-group">
                <li class="list-group-item d-flex justify-content-between align-items-center" v-for="task in tasks.data" :key="task.id"><a href="#">{{task.name}}</a>
                <div class="btn-group">
                    <button type="button" class="btn btn-secondary" data-toggle="modal" data-target="#modifierModal" @click="getTask(task.id)">
                    Editer
                    </button>
                    <button type="button" class="btn btn-danger" @click="deleteTask(task.id)">Supprimer</button>
                </div>
                </li>
                <edit-task v-bind:taskToEdit = "taskToEdit" @task-updated="refresh"></edit-task>
                
            </ul>
            <pagination :data="tasks" @pagination-change-page="getResults" class="mt-5"></pagination>
    </div>
</template>

<script>
    export default {

        data()
        {
            return {
                            tasks: {},
                            taskToEdit:''
                    }
        },

        methods:{
                // Our method to GET results from a Laravel endpoint
            getResults(page = 1) {
            axios.get('/tasksList?page=' + page)
                .then(response => {
                    this.tasks = response.data;
                });
            },
            refresh(tasks){
                this.tasks=tasks.data
            },
            getTask(id){
                axios.get('http://127.0.0.1:8000/tasks/edit/' + id)
                    .then(response=> this.taskToEdit = response.data)
                    .catch(error => console.log(error));
            },
            deleteTask(id){
                axios.delete('http://127.0.0.1:8000/tasks/' +id)
                     .then(response => this.tasks = response.data)
                     .catch(error => console.log(error) );
            },

        },

        created(){
            axios.get('/tasksList')
                 .then(response => this.tasks = response.data)
                 .catch(error => console.log(error));
        },

        mounted() {
            console.log('Component mounted.')
        }
    }
</script>
