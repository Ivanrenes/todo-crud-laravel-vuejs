<template>
    <div class="w-25">
        <form @submit.prevent="saveData">
            <div class="input-group mb-3 w-100">
            <input v-model="form.title" :class="{'is-invalid' : form.errors.has('title'), 'is-valid' : form.title }" type="text" class="form-control form-control-lg"  @keydown="form.errors.clear('title')"
            aria-label="Recipient's username" aria-describedby="button-addon2">
            <div class="input-group-append">
                <button class="btn btn-success" type="submit" id="button-addon2">Add this to your list</button>
            </div>
            </div>
            <span class="text-danger pt-3 pb-3" style="font-size:20px;" v-if="form.errors.has('title')" v-text="form.errors.get('title')"></span>
        </form>
        <div class="w-100 todo">
            <div v-for="todo in todos" :key="todo.id" class="w-100 d-flex align-items-center p-3 bg-white border-bottom">
                <span class="mr-2">

            <svg v-if="todo.completed == false" v-on:click="toggleTodo(todo)" xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-check" width="60" height="60" viewBox="0 0 24 24" stroke-width="1.5" stroke="#7bc62d" fill="none" stroke-linecap="round" stroke-linejoin="round">
            <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
            <path d="M5 12l5 5l10 -10" />
            </svg>

            <svg v-if="todo.completed == true" v-on:click="toggleTodo(todo)" xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-checks" width="60" height="60" viewBox="0 0 24 24" stroke-width="1.5" stroke="#7bc62d" fill="none" stroke-linecap="round" stroke-linejoin="round">
            <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
            <path d="M7 12l5 5l10 -10" />
            <path d="M2 12l5 5m5 -5l5 -5" />
            </svg>
            </span>

            <div  class="font-weight-bolder"><span v-if="editmode == false || editmode != todo.id">{{todo.title}}</span><input v-if="editmode == todo.id"   v-model="todo.title" type="text">
            </div>

            <div class="ml-auto mr-2 d-flex align-items-center"><span>



            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                editmode: false,
                todos:'',
                form: new Form({
                    title: '',
                })
            }
        },
        methods:{
            deleteTodo(e){
                let data = new FormData();
                data.append('_method', 'DELETE')
                axios.post('/api/todo/'+e.id, data).then((res) =>{
                    this.todos = res.data
                }).catch((error) => {
                    this.form.errors.record(error.response.data.errors)
                })
            },
            updateTodo(e){
                this.editmode = false
                let data = new FormData();
                data.append('_method', 'PATCH')
                data.append('title', e.title)
                axios.post('/api/todo/'+e.id, data)
                .catch((error) => {
                    this.form.errors.record(error.response.data.errors)
                })
            },
            toggleTodo(e){
                e.completed = !e.completed
                let data = new FormData();
                data.append('_method', 'PATCH')
                if(e.completed == true){
                    data.append('completed', 1);
                }
                if(e.completed == false){
                    data.append('completed', 0)
                }
                axios.post('/api/todo/'+e.id, data)
            },
            getTodos(){
                    axios.get('/api/todo').then((res) =>{
                        this.todos = res.data
                    }).catch((error) =>{
                        console.log(error)
                    })
            },
            saveData(){
                let data = new FormData();
                data.append('title', this.form.title)
                axios.post('/api/todo', data).then((res) =>{
                    this.form.reset()
                     this.getTodos()
                }).catch((error) => {
                    this.form.errors.record(error.response.data.errors)
                })
            }
        },
        mounted() {
          this.getTodos()
        }
    }
</script>
