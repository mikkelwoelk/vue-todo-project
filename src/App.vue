<template>
    <div id="app">
        <div id="header-container">
            <img
                src="./assets/logo.png"
                id="app-logo"
                alt="Todo list clipboard"
            />
            <h1>{{ header }}</h1>
            <h2>{{ subheader }}</h2>
        </div>
        <div id="form-container">
            <form @submit.prevent="addTodo">
                <input
                    type="text"
                    name="todoTitle"
                    id="user-todo-input"
                    placeholder="Add a new Todo"
                    maxlength="25"
                    required
                    v-model.trim="newTodo.title"
                />
                <input
                    type="text"
                    name="todoDesc"
                    id="user-desc-input"
                    placeholder="Describe it further if needed.."
                    maxlength="200"
                    v-model.trim="newTodo.description"
                />
                <div id="radio-container">
                    <input
                        class="importance-radio"
                        type="radio"
                        name="importance"
                        id="todo-importance-radio-green"
                        style="background: rgb(111, 200, 65);"
                        checked
                        @change="onImportanceChange($event, 'green', 1)"
                    />
                    <input
                        class="importance-radio"
                        type="radio"
                        name="importance"
                        id="todo-importance-radio-yellow"
                        style="background: rgb(197, 200, 65);"
                        @change="onImportanceChange($event, 'yellow', 2)"
                    />
                    <input
                        class="importance-radio"
                        type="radio"
                        name="importance"
                        id="todo-importance-radio-red"
                        style="background-color: rgb(200, 65, 65);"
                        @change="onImportanceChange($event, 'red', 3)"
                    />
                </div>
                <input
                    type="datetime-local"
                    name="deadline"
                    id="todo-deadline"
                    v-model="newTodo.date"
                />
                <button
                    id="submit-btn"
                    type="submit"
                    :class="[newTodo.title ? activeClass : '']"
                >
                    add todo
                </button>
            </form>
        </div>
        <div id="todos-container">
            <div id="todo-filter-button-container">
                <button @click="sortImportanceLow">Sort importance: Low</button>
                <button @click="sortImportanceHigh">
                    Sort importance: High
                </button>
            </div>
            <ul id="todo-list">
                <todoItem
                    v-for="(todo, index) in todos"
                    :key="`todo-${index}`"
                    v-bind="todo"
                    @remove="() => deleteTodo(index)"
                >
                </todoItem>
            </ul>
        </div>
    </div>
</template>

<script>
import moment from "moment";
import todoItem from "../src/assets/components/todoItem.vue";
export default {
    name: "app", // Points to the HTML elements in with to place everything
    components: { todoItem },
    data() {
        // Stores all of the data to be used in the vue instance
        return {
            header: "Todo List", // Sets the innerHTML of the h1
            subheader:
                "Make a new todo! You can also add a description and choose importance.", // Sets the innerHTML of the h2

            newTodo: {
                // Object for a new todo
                title: "", // Value for todo title
                description: "", // Value for todo description
                importance: { color: "green", num: 1 }, // Value for todo importance (Defaulting to green)
                date: moment().format("YYYY[-]MM[-]DDTHH:mm") // Value for date picker (Sets the value to the date and time as it is right now when the page is loaded)
            },
            todos: [
                // Array of todo objects
                {
                    title: "Make a todo list",
                    description: "It has to have alot of nice features",
                    importance: { color: "green", num: 1 },
                    date: moment()
                        .add("4", "weeks")
                        .fromNow()
                },
                {
                    title: "This is a medium todo",
                    description: "It has the same color as a banana",
                    importance: { color: "yellow", num: 2 },
                    date: moment()
                        .add("10", "days")
                        .fromNow()
                },
                {
                    title: "This is a max length todo",
                    description:
                        "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Duis convallis justo eros, a ullamcorper orci rhoncus sed. Phasellus blandit risus elit, et hendrerit ipsum tempor venenatis. Vestibulum dictum",
                    importance: { color: "red", num: 3 },
                    date: moment()
                        .add("1", "hours")
                        .fromNow()
                }
            ],
            activeClass: "isActive", // Class style for when the submit button is active (Activates when the user types something in the todo title input)
            dateTime: "" // Empty string for storing the date and time (updateTime() updates the date/time in the date picker every minute)
        };
    },
    methods: {
        // Object that stores functinos
        addTodo() {
            // Adds a new todo object to the todos array, as well as getting and setting the date/time from the date picker into the todoDate. Then resets the inputs for the next todo (except the importance)
            this.newTodo.date = moment(this.newTodo.date).fromNow(); // Sets the date/time of the todo to the user selected input and shows the time till its due
            this.todos.push(this.newTodo); // Pushes the todo object into the todo array
            this.newTodo = {
                // Resets all of the input in some way
                title: "",
                description: "",
                importance: {
                    color: this.newTodo.importance.color,
                    num: this.newTodo.importance.num
                }, // Except this one because reasons..
                date: moment().format("YYYY[-]MM[-]DDTHH:mm")
            };
        },
        onImportanceChange(e, color, num) {
            // Gets the color value from the selected importance radio button and sets the todoImportance to said color
            // Also sets the number for the importance sort the todos later (1 = low importance, 3 = high importance)
            if (e.target.checked) {
                this.newTodo.importance.color = color;
                this.newTodo.importance.num = num;
            }
        },
        updateTime() {
            // Gets the actual date/time from moments.js
            this.newTodo.date = moment().format("YYYY[-]MM[-]DDTHH:mm");
        },
        sortImportanceLow() {
            // Sorts todos after lowest importance by sorting after the importanceNum dependency
            this.todos.sort((a, b) =>
                a.importance.num > b.importance.num ? 1 : -1
            );
        },
        sortImportanceHigh() {
            // Sorts todos after highest importance by sorting after the importanceNum dependency
            this.todos.sort((a, b) =>
                a.importance.num < b.importance.num ? 1 : -1
            );
        },
        deleteTodo(index) {
            // Prompts the user with a confirm alert when trying to delete a todo
            if (confirm("Are you sure you want to delete this todo?")) {
                // Deletes a todo by removing it from the array
                this.todos.splice(index, 1);
            } else {
                return;
            }
        }
    },
    mounted: function() {
        // Runs updateTime() every 10 seconds to keep the time in the date picker fairly up to date
        this.dateTime = setInterval(() => {
            this.updateTime();
        }, 10000);
    }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Josefin+Sans:wght@300;400;500;600;700&display=swap");
:root {
    --clr-primary: #00485e;
    --clr-secondary: #d600cf;
    --clr-text: #ffffff;
    --clr-text-placeholder: #e6e6e6;
    --clr-accent-light: #50ddc8;
    --clr-accent-darker: #52c9b7;
    --clr-accent-darkest: #4ea699;
}

html {
    font-size: 62.5%;
    scroll-behavior: smooth;
}

body {
    background-color: var(--clr-primary);
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

* {
    font-family: "Josefin Sans", sans-serif;
    margin: 0;
    padding: 0;
    border: none;
    box-sizing: border-box;
}

/** App styles */
#app {
    text-align: center;
    color: #2c3e50;
    width: min(90%, 500px);
}

/** Header section styles */
#app-logo {
    width: max(20rem, 100px);
}

h1,
h2 {
    font-weight: normal;
    color: var(--clr-text);
}

h1 {
    font-size: 5rem;
}

h2 {
    font-size: 2rem;
}

/** Form section styles */
form {
    display: flex;
    flex-direction: column;
}

#form-container {
    margin: 2rem 0;
    box-shadow: 0px 0px 20px var(--clr-accent-darkest);
    border-radius: 10px;
    overflow: hidden;
    max-height: 51px;
    transition: max-height ease-in-out 1s;
}

#form-container input {
    padding: 2rem 2rem;
    border-bottom: 1px solid var(--clr-accent-darker);
    background-color: var(--clr-accent-darkest);
    outline-color: var(--clr-accent-light);
    outline-style: solid;
    color: var(--clr-text);
}

#form-container input::placeholder {
    color: var(--clr-text-placeholder);
}

#user-todo-input {
    border-top-left-radius: 10px;
    border-top-right-radius: 10px;
}

#form-container:focus-within {
    max-height: 280px;
}

#radio-container {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
}

#radio-container input[type="radio"] {
    appearance: none;
    width: 100%;
    height: 5rem;
    display: grid;
    place-content: center;
    cursor: pointer;
}

#radio-container input[type="radio"]::after {
    content: url("https://api.iconify.design/eva/checkmark-circle-outline.svg?color=white");
    scale: 0;
    rotate: 180deg;
    opacity: 0;
    transition: ease-in-out 0.25s;
    margin-top: 5px;
}

#radio-container input[type="radio"]:checked::after {
    scale: 2;
    rotate: 0deg;
    opacity: 1;
}

#todo-deadline::selection {
    background-color: transparent;
}

#todo-deadline[type="datetime-local"] {
    position: relative;
}

#todo-deadline[type="datetime-local"]::after {
    content: url("https://api.iconify.design/akar-icons/calendar.svg?color=white");
}

#todo-deadline[type="datetime-local"]::-webkit-calendar-picker-indicator {
    cursor: pointer;
    position: absolute;
    top: 0;
    right: 0;
    width: 100%;
    height: 100%;
    padding: 0;
    color: transparent;
    background: transparent;
}

#submit-btn {
    padding: 2rem;
    border-bottom-left-radius: 10px;
    border-bottom-right-radius: 10px;
    background-color: var(--clr-accent-darkest);
    transition: background-color ease 0.5s;
    color: #fff;
    cursor: pointer;
    outline-color: var(--clr-accent-light);
    outline-style: solid;
    text-transform: uppercase;
}

#submit-btn.isActive {
    background-color: var(--clr-accent-darker);
}

/** Todo section styles */
#todo-filter-button-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

#todo-filter-button-container button {
    padding: 1rem 2rem;
    color: var(--clr-text);
    background-color: var(--clr-primary);
    border-radius: 10px;
    box-shadow: 0px 0px 20px var(--clr-accent-darkest);
    cursor: pointer;
}

#todo-list {
    list-style-type: none;
    display: flex;
    flex-direction: column;
}
</style>
