<template>
    <div>
        <form @submit.prevent="addTodo()">
            <center><h2>To Do List</h2></center>
            <label>New ToDo </label>
            <input
                v-model="newTodo"
                name="newTodo"
                autocomplete="off"
            >
            <button>Add ToDo</button>
        </form>

        <div class="filter-buttons">
            <button class="filter-button" :class="{ active: filter === 'all' }" @click="filter = 'all'">All</button>
            <button class="filter-button" :class="{ active: filter === 'done' }" @click="filter = 'done'">Done</button>
            <button class="filter-button" :class="{ active: filter === 'undone' }" @click="filter = 'undone'">Undone</button>
        </div>
      
        <ul>
            <li
                v-for="(todo, index) in filteredTodos"
                :key="index"
            >
                <input
                    type="checkbox"
                    v-model="todo.done"
                    :id="'todo_' + index"
                >
                <label :for="'todo_' + index" :class="{ done: todo.done }">{{ todo.content }}</label>
                <button @click="removeTodo(index)">Remove</button>
            </li>
        </ul>
        <h4 v-if="filteredTodos.length === 0">Empty list.</h4>
    </div>
</template>

<script>
    import { ref, computed } from 'vue';
    export default {
        name: 'App',
        setup () {
            const newTodo = ref('');
            const defaultData = [{
                done: false,
                content: 'Write a blog post'
            }]
            const todosData = JSON.parse(localStorage.getItem('todos')) || defaultData;
            const todos = ref(todosData);
            const filter = ref('all');
            function addTodo () {
                if (newTodo.value) {
                    todos.value.push({
                        done: false,
                        content: newTodo.value
                    });
                    newTodo.value = '';
                }
                saveData();
            }
            function removeTodo (index) {
                todos.value.splice(index, 1);
                saveData();
            }
            function saveData () {
                const storageData = JSON.stringify(todos.value);
                localStorage.setItem('todos', storageData);
            }

            const filteredTodos = computed(() => {
                if (filter.value === 'done') {
                    return todos.value.filter(todo => todo.done);
                } else if (filter.value === 'undone') {
                    return todos.value.filter(todo => !todo.done);
                } else {
                    return todos.value;
                }
            });

            return {
                todos,
                newTodo,
                addTodo,
                removeTodo,
                saveData,
                filter,
                filteredTodos
            }
        }
    }
</script>