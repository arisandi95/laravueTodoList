<template>
    <div class="container">
        <div class="row">
            <div class="col-md-6 offset-md-3">
                <div class="card" style="width : 25rem;">
                    <div class="card-body">
                        <div class="card-title"><h3>My Tasks</h3></div>
                        <div class="input-group">
                            <input 
                                type="text" class="form-control"
                                v-model="task.name"
                                @keydown.enter="create">
                            <span class="input-group-btn">
                                <button class="btn btn-success" @click="create">Add</button>
                            </span>
                        </div>
                        
                        <div class="tasks-list">
                            <ul class="list-unstyled">
                                <div class="div alert alert-danger" v-if="!tasks.length">
                                    You have no tasks!
                                </div>
                                <li v-for="task in tasks" :key="task.id" :class="{done : task.completed}">
                                    <div class="checkbox">
                                        <label>
                                            <input type="checkbox" v-model="task.completed" @click="done(task)"> {{ task.name }} 
                                        </label>
                                        <div class="float-right">
                                            <a href="#" @click.prevent="remove(task)">x</a>
                                        </div>
                                    </div>

                                </li>
                            </ul>
                        </div>

                    </div>

                    <div class="card-footer" v-if="tasks.length">
                        <span class="badge badge-primary">You have {{ tasks.length }} tasks</span>
                        <span class="badge badge-warning">{{ remainingTasks() }} tasks left</span>
                        <span class="badge badge-success">{{ completedTasks() }} tasks completed</span>
                    </div>
                </div>
            </div>

        </div>
    </div>
</template>

<script>
    export default {
        mounted() {
            this.fetchData()
        },
        data () {
            return {
                tasks: [],
                task: {
                    name: ''
                }
            }
        },
        methods: {
            remainingTasks () {
                return this.tasks.filter(task => {return !task.completed}).length
            },

            completedTasks () {
                return this.tasks.filter(task => {return task.completed}).length
            },

            fetchData () {
                axios.get(`/api/tasks`)
                    .then((res) => {
                        this.tasks = res.data
                    })
                    .catch((err) => {
                        console.log(err)
                    })
            },

            create() {
                axios.post(`/api/tasks`, this.task)
                    .then((res) => {
                        this.tasks.unshift(res.data)
                        this.task.name = ''
                    })
                    .catch((err) => {
                        console.log(err)
                    })
            },

            done (task) {
                axios.put(`/api/tasks/${task.id}`, {
                    completed: !task.completed
                })
                .then((res) => {
                    console.log('task updated')
                })
                .catch((err) => {
                    console.log(err)
                })
            },

            remove (task) {
                axios.delete(`/api/tasks/${task.id}`)
                .then((res) => {
                    const taskIndex = this.tasks.indexOf(task)
                    this.tasks.splice(taskIndex, 1)
                })
                .catch((err) => {
                    console.log(err)
                })
            }
        }
    }
</script>

<style type="text/css">
    body, .tasks-list {
        padding-top: 20px;
    }    

    .done label {
        text-decoration: line-through;
    }
</style>
