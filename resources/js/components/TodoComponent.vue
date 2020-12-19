<template>
    <div class="w-50">
        <form @submit.prevent="saveData">
            <div class="input-group mb-3">
            <input v-model="form.title" type="text" class="form-control  form-control-lg"  aria-label="Recipient's username" aria-describedby="basic-addon2">
            <div class="input-group-append">
                <button class="btn btn-success" type="submit">Add this to your list</button>
            </div>
            </div>
        </form>
        <div  class="w-25">
            <div v-for="todo in todos" :key="todo.id" clas="w-100">
                {{todo.title}}
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                todos:'',
                form: new Form({
                    title: '',
                })

            }
        },
        methods:{
            getTodos(){
                axios.get('/api/todo').then((res) =>{
                    this.todos = res.data;
                }).catch((error) => {
                    console.log(error)
                })
            },
            saveData(){
              let data = new FormData();
              data.append('title', this.form.title);
              axios.post('/api/todo', data).then((res) =>{
                      this.getTodos();
              }).catch((error) => {
                    console.log(error)
                });
            }
        },

        mounted() {
            this.getTodos();
        }
    }
</script>
