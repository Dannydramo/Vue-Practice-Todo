<template>
    <div class="w-[90%] mx-auto">
        <div class="p-4 flex flex-col justify-center my-4 rounded space-y-4">
            <div class="">
                <h1 class="w-full text-md md:text-xl font-bold">
                    What's up,
                    <input
                        type="text"
                        class="w-1/2 py-2 ml-4 px-4 focus:outline-none border"
                        placeholder="Enter Your Name"
                        v-model="names"
                    />
                </h1>
            </div>

            <div class="flex flex-col space-y-4">
                <h2 class="text-xl font-bold">CREATE TO-DO</h2>
                <form @submit.prevent="addTodo">
                    <h3 class="text-lg mb-4 font-semibold">
                        What's on your to-do list?
                    </h3>
                    <input
                        class="p-4 mb-2 border-2 border-green-400 w-full md:w-1/2 shadow-md rounded-lg focus:outline-none"
                        type="text"
                        placeholder="e.g Go and get something"
                        v-model="inputName"
                    />
                    <textarea
                        name="description"
                        placeholder="Add a description"
                        id=""
                        cols="5"
                        rows="3"
                        v-model="inputDescription"
                        class="w-full md:w-1/2 border-2 border-green-400 block p-4 rounded-md shadow-md focus:outline-none"
                    ></textarea>
                    <h4 class="text-lg mb-4 font-semibold mt-4">
                        Pick a category
                    </h4>

                    <div
                        class="w-full md:w-1/2 flex justify-around items-center"
                    >
                        <label
                            class="flex flex-col justify-center items-center space-y-1 rounded-md shadow-md p-6 border-2 border-blue-600"
                        >
                            <input
                                type="radio"
                                name="category"
                                value="Business"
                                v-model="inputCategory"
                                class="accent-blue-600"
                            />
                            <div>Business</div>
                        </label>

                        <label
                            class="flex flex-col justify-center items-center space-y-1 rounded-md shadow-md p-6 border-2 border-pink-600"
                        >
                            <input
                                type="radio"
                                name="category"
                                value="Personal"
                                v-model="inputCategory"
                                class="accent-pink-600"
                            />
                            <div>Personal</div>
                        </label>
                    </div>
                    <input
                        type="submit"
                        value="Add Todo"
                        class="w-full md:w-1/2 p-4 bg-green-400 text-white font-semibold rounded-md shadow-md mt-4"
                    />
                </form>
            </div>

            <div class="flex flex-col">
                <h2 class="mb-4 font-semibold">YOUR TODO LIST</h2>
                <div
                    class="w-full md:w-1/2 uppercase font-bold h-full text-center"
                    v-if="noTodo"
                >
                    No TODOS yet!
                </div>
                <div v-else-if="!noTodo" class="flex flex-col space-y-2">
                    <div
                        v-for="(todo, index) in todos"
                        v-bind:class="`w-full md:w-1/2 py-2 px-4 rounded-md shadow-md flex space-x-3 items-center justify-between border-2 relative ${
                            todo.done
                                ? ' border-green-400'
                                : todo.descriptionColor
                                ? 'border-blue-500'
                                : 'border-pink-500'
                        }`"
                        :key="index"
                    >
                        <label>
                            <input
                                type="checkbox"
                                :class="`${
                                    todo.descriptionColor
                                        ? 'bg-blue-500'
                                        : 'bg-pink-500'
                                }`"
                                v-model="todo.done"
                            />
                        </label>
                        <div class="w-2/3">
                            <input
                                type="text"
                                class="text-center w-full capitalize"
                                v-model="todo.names"
                            />
                        </div>
                        <div
                            v-if="todo.showDescription"
                            :class="`absolute ${
                                todo.descriptionColor
                                    ? 'bg-blue-500'
                                    : 'bg-pink-500'
                            } w-2/3 md:w-1/2 left-[2.5%] md:left-[15%] lg:left-[25%] text-xs md:text-md text-center h-full font-mono`"
                        >
                            <h2 class="uppercase mt-4 font-semibold">
                                Description of task: {{ todo.names }}
                            </h2>
                            <input
                                type="text"
                                class="bg-transparent"
                                name=""
                                id=""
                                v-model="todo.description"
                            />
                        </div>
                        <div class="flex flex-col space-y-1">
                            <button
                                class="p-2 rounded-md bg-blue-500 text-white text-xs md:text-sm font-semibold"
                                @click="showDescription(todo)"
                            >
                                description
                            </button>
                            <button
                                class="p-2 rounded-md bg-red-500 text-white text-xs md:text-sm font-semibold"
                                @click="removeTodo(todo)"
                            >
                                Delete
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";

export default {
    data() {
        return {
            todos: [],
            names: "",
            inputName: "",
            inputDescription: "",
            inputCategory: null,
            noTodo: true,
        };
    },
    methods: {
        checkCategory(todo) {
            todo.category === "Business"
                ? (todo.descriptionColor = true)
                : (todo.descriptionColor = false);
        },
        showDescription(todo) {
            todo.showDescription = !todo.showDescription;
            this.checkCategory(todo);
        },
        addTodo() {
            if (
                this.inputName.trim() === "" ||
                this.inputCategory === null ||
                this.inputDescription.trim() === ""
            ) {
                toast.warn("PLEASE FILL IN ALL FIELDS!");
                return;
            }

            this.todos.push({
                names: this.inputName,
                description: this.inputDescription,
                category: this.inputCategory,
                descriptionColor: false,
                showDescription: false,
                done: false,
                createdAt: new Date().getTime(),
            });

            this.checkTodo();
        },
        removeTodo(todo) {
            this.todos = this.todos.filter((t) => t != todo);
            this.checkTodo();
        },
        checkTodo() {
            const todos = JSON.parse(JSON.stringify(this.todos));

            this.noTodo = todos.length <= 0;
        },
    },
    watch: {
        names(newValue) {
            localStorage.setItem("name", newValue);
        },
        todos(newValue) {
            localStorage.setItem("todos", JSON.stringify(newValue));
        },
    },
    mounted() {
        this.names = localStorage.getItem("name") || "";
        this.todos = JSON.parse(localStorage.getItem("todos")) || [];
        this.checkTodo();
    },
};
</script>
