<template>

    <div class="app-component">

        <loading :active.sync="isLoading" :can-cancel="true" :is-full-page="true"></loading>
        <table class="table">
            <thead>
                <tr>
                    <th>Id</th>
                    <th>Task Title</th>
                    <th>Priority</th>
                    <th>Action</th>
                </tr>
            </thead>
            <tbody>
                <task-component v-for="task in tasks" :key="task.id" :task="task" @delete="remove"></task-component>
                <tr>
                    <td><input type="text" v-model="task.title" id="task" class="form-control"></td>
                    <td>
                        <select id="select" v-model="task.priority" class="form-control">
                            <option>Low</option>
                            <option>Medium</option>
                            <option>High</option>
                        </select>
                    </td>
                    <td><button @click="store" class="btn btn-primary">ADD</button></td>
                </tr>
            </tbody>
        </table>

    </div>

</template>

<script>

    import Loading from 'vue-loading-overlay';
    import 'vue-loading-overlay/dist/vue-loading.min.css';
    import TaskComponent from './Task.vue';
    export default{
        data(){
            return{
                isLoading : false,
                tasks: [],
                task: {title: '', priority: ''},
            }
        },
        methods:{
            getTasks(){
                window.axios.get('/api/tasks').then(({data})=>{
                    data.forEach(task =>{
                        this.tasks.push(task)
                    });
                });
            },
            store(){
                    if(this.checkInputs()){
                        this.isLoading = true;
                        window.axios.post('/api/tasks', this.task).then(savedTask =>{
                            this.tasks.push(savedTask.data);
                            this.tasks.title = '';
                            this.isLoading = false;
                        })
                    }

            },
            checkInputs(){
                if(this.task.title && this.task.priority) return true;
            },
            remove(id){
                this.isLoading = true;
                window.axios.delete(`/api/tasks/${id}`).then(()=>{
                    let index = this.tasks.findIndex(task => task.id === id);
                    this.tasks.splice(index, 1);
                    this.isLoading = false;
                });
                console.log(`GOTCHA DATA ${id}`);
            }
        },

        created(){
            this.getTasks();
        },
        components: {TaskComponent, Loading}
    }

</script>

<style>

</style>