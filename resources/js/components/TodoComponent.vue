<template>
    <div class="w-25">
        <form @submit.prevent="saveData">
            <div class="input-group mb-3 w-100">
                <input v-model="form.title"
                       :class="{'is-invalid' : form.errors.has('title')}"
                       type="text"
                       class="form-control form-control-lg"
                       @keydown="form.errors.clear('title')"
                >
                <div class="input-group-append">
                    <button class="btn btn-success" type="submit" id="button-add">Add task</button>
                </div>
            </div>
            <span class="text-danger pt-3" style="font-size:20px" v-if="form.errors.has('title')" v-text="form.errors.get('title')"></span>
        </form>
        <div class="w-25">
            <div v-for="todo in todos" :key="todo.id" class="w-100">
                {{todo.title}}
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
            return {
                editMode: false,
                todos: '',
                form: new Form({
                    title: '',
                })
            }
        },
        methods: {

            deleteTodo(e) {
                let data = new FormData()
                data.append('_method', 'DELETE')
            },

            updateTodo(e) {

            },

            // Изменение выполнености таска
            toggleTodo(e) {
                e.completed = !e.completed
                let data = new FormData()
                data.append('_method', 'PATCH')
                data.append('completed', e.completed ? 1 : 0)
                axios.post('api/todo/'+e.id, data).then((res) =>  {
                    this.todos = res.data;
                }).catch((error) => {
                    console.log(error)
                })
            },

            getTodos() {
                axios.get('api/todo').then((res) =>  {
                    this.todos = res.data;
                }).catch((error) => {
                    console.log(error)
                })
            },

            saveData() {
                let data = new FormData();
                data.append('title', this.form.title)
                axios.post('/api/todo', data).then((res) => {
                    this.form.title = ''
                    this.getTodos()
                }).catch((err) => {
                    this.form.errors.record(err.response.data.errors)
                });
            }
        },
        mounted() {
            this.getTodos()
        }
    }
</script>
