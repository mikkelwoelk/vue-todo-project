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
                    v-model.lazy="newTodo.todoTitle"
                />
                <input
                    type="text"
                    name="todoDesc"
                    id="user-desc-input"
                    placeholder="Describe it further if needed.."
                    maxlength="200"
                    v-model.lazy="newTodo.todoDesc"
                />
                <div id="radio-container">
                    <input
                        class="importance-radio"
                        type="radio"
                        name="importance"
                        id="todo-importance-radio-green"
                        style="background: rgb(111, 200, 65);"
                        checked
                        @change="onChange($event, 'green', 1)"
                    />
                    <input
                        class="importance-radio"
                        type="radio"
                        name="importance"
                        id="todo-importance-radio-yellow"
                        style="background: rgb(197, 200, 65);"
                        @change="onChange($event, 'yellow', 2)"
                    />
                    <input
                        class="importance-radio"
                        type="radio"
                        name="importance"
                        id="todo-importance-radio-red"
                        style="background-color: rgb(200, 65, 65);"
                        @change="onChange($event, 'red', 3)"
                    />
                </div>
                <input
                    type="datetime-local"
                    name="deadline"
                    id="todo-deadline"
                    v-model="newTodo.todoDate"
                />
                <button
                    id="submit-btn"
                    type="submit"
                    :class="[newTodo.todoTitle ? activeClass : '']"
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
                <li
                    class="todo-list-item"
                    :class="{
                        'lvl-clr-green': todo.todoImportance.color === 'green',
                        'lvl-clr-yellow':
                            todo.todoImportance.color === 'yellow',
                        'lvl-clr-red': todo.todoImportance.color === 'red'
                    }"
                    v-for="(todo, index) in todos"
                    :key="`todo-${index}`"
                >
                    <div class="todo-checked-overlay"></div>
                    <div class="printed-todo-header-container">
                        <h3
                            v-if="isEditing !== index"
                            class="printed-todo-title"
                        >
                            {{ todo.todoTitle }}
                        </h3>
                        <input
                            v-else
                            type="text"
                            name="todoEditTitle"
                            class="printed-todo-editing-title"
                            maxlength="25"
                            v-model="todo.todoTitle"
                            @keyup.enter="isEditing = -1"
                        />
                        <time
                            v-if="isEditing !== index"
                            class="printed-todo-date"
                            >Due {{ todo.todoDate }}</time
                        >
                        <input
                            v-else
                            type="datetime-local"
                            name="editDeadline"
                            class="printed-todo-editing-deadline"
                            v-model="todo.todoDate"
                        />
                        <div class="printed-todo-checkbox-container">
                            <div class="printed-todo-checkbox-wrapper">
                                <input
                                    type="checkbox"
                                    name="todoCheckbox"
                                    class="todo-checkbox"
                                />
                            </div>
                        </div>
                    </div>
                    <div class="printed-todo-line"></div>
                    <div class="printed-todo-dropdown-container">
                        <p v-if="isEditing !== index" class="printed-todo-desc">
                            {{ todo.todoDesc }}
                        </p>
                        <textarea
                            v-else
                            type="text"
                            name="todoEditDesc"
                            class="printed-todo-editing-desc"
                            maxlength="200"
                            v-model="todo.todoDesc"
                            @keyup.enter="isEditing = -1"
                        />
                        <div class="printed-todo-button-container">
                            <div class="printed-todo-button-wrapper">
                                <input
                                    type="checkbox"
                                    name="todoEdit"
                                    @click="editTodo(index)"
                                    class="printed-todo-edit-checkbox"
                                />
                                <button
                                    class="printed-todo-delete-btn"
                                    aria-label="Delete"
                                    @click="deleteTodo(index)"
                                >
                                    <svg
                                        xmlns="http://www.w3.org/2000/svg"
                                        width="24"
                                        height="24"
                                        preserveAspectRatio="xMidYMid meet"
                                        viewBox="0 0 448 512"
                                    >
                                        <path
                                            fill="currentColor"
                                            d="M160 400c0 8.8-7.2 16-16 16s-16-7.2-16-16V192c0-8.8 7.2-16 16-16s16 7.2 16 16v208zm80 0c0 8.8-7.2 16-16 16s-16-7.2-16-16V192c0-8.8 7.2-16 16-16s16 7.2 16 16v208zm80 0c0 8.8-7.2 16-16 16s-16-7.2-16-16V192c0-8.8 7.2-16 16-16s16 7.2 16 16v208zm-2.5-375.06L354.2 80H424c13.3 0 24 10.75 24 24c0 13.3-10.7 24-24 24h-8v304c0 44.2-35.8 80-80 80H112c-44.18 0-80-35.8-80-80V128h-8c-13.25 0-24-10.7-24-24c0-13.25 10.75-24 24-24h69.82l36.68-55.06C140.9 9.357 158.4 0 177.1 0h93.8c18.7 0 36.2 9.358 46.6 24.94zM151.5 80h145l-19-28.44c-1.5-2.22-4-3.56-6.6-3.56h-93.8c-2.6 0-6 1.34-6.6 3.56L151.5 80zM80 432c0 17.7 14.33 32 32 32h224c17.7 0 32-14.3 32-32V128H80v304z"
                                        />
                                    </svg>
                                </button>
                            </div>
                        </div>
                    </div>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
import moment from "moment";
// import { todoList } from "./components";
export default {
    name: "app", // Points to the HTML elements in with to place everything
    data() {
        // Stores all of the data to be used in the vue instance
        return {
            header: "Todo List", // Sets the innerHTML of the h1
            subheader:
                "Make a new todo! You can also add a description and choose importance.", // Sets the innerHTML of the h2
            newTodo: {
                // Object for a new todo
                todoTitle: "", // Value for todo title
                todoDesc: "", // Value for todo description
                todoImportance: { color: "green", num: 1 }, // Value for todo importance (Defaulting to green)
                todoDate: moment().format("YYYY[-]MM[-]DDTHH:mm") // Value for date picker (Sets the value to the date and time as it is right now when the page is loaded)
            },
            todos: [
                // Array of todo objects
                {
                    todoTitle: "Make a todo list",
                    todoDesc: "It has to have alot of nice features",
                    todoImportance: { color: "green", num: 1 },
                    todoDate: moment()
                        .add("4", "weeks")
                        .fromNow()
                },
                {
                    todoTitle: "This is a medium todo",
                    todoDesc: "It has the same color as a banana",
                    todoImportance: { color: "yellow", num: 2 },
                    todoDate: moment()
                        .add("10", "days")
                        .fromNow()
                },
                {
                    todoTitle: "This is a max length todo",
                    todoDesc:
                        "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Duis convallis justo eros, a ullamcorper orci rhoncus sed. Phasellus blandit risus elit, et hendrerit ipsum tempor venenatis. Vestibulum dictum",
                    todoImportance: { color: "red", num: 3 },
                    todoDate: moment()
                        .add("1", "hours")
                        .fromNow()
                }
            ],
            activeClass: "isActive", // Class style for when the submit button is active (Activates when the user types something in the todo title input)
            dateTime: "", // Empty string for storing the date and time (updateTime() updates the date/time in the date picker every minute)
            isEditing: -1 // Value for a todo NOT being in editing mode, changes to the index for the todo, to show the input fields instead, when clicking the edit icon
        };
    },
    methods: {
        // Object that stores functinos
        addTodo() {
            // Adds a new todo object to the todos array, as well as getting and setting the date/time from the date picker into the todoDate. Then resets the inputs for the next todo (except the importance)
            this.newTodo.todoDate = moment(this.newTodo.todoDate).fromNow(); // Sets the date/time of the todo to the user selected input and shows the time till its due
            this.todos.push(this.newTodo); // Pushes the todo object into the todo array
            this.newTodo = {
                // Resets all of the input in some way
                todoTitle: "",
                todoDesc: "",
                todoImportance: {
                    color: this.newTodo.todoImportance.color,
                    num: this.newTodo.todoImportance.num
                }, // Except this one because reasons..
                todoDate: moment().format("YYYY[-]MM[-]DDTHH:mm")
            };
        },
        onChange(e, color, num) {
            // Gets the color value from the selected importance radio button and sets the todoImportance to said color
            // Also sets the number for the importance sort the todos later (1 = low importance, 3 = high importance)
            if (e.target.checked) {
                this.newTodo.todoImportance.color = color;
                this.newTodo.todoImportance.num = num;
            }
        },
        editTodo(index) {
            // Checks whether or not the edit button is clicked on a todo and therefore changing the elements from text elements to inputs and vice versa
            if (this.isEditing !== index) {
                this.isEditing = index;
            } else {
                this.isEditing = -1;
            }
        },
        deleteTodo(index) {
            // Prompts the user with a confirm alert when trying to delete a todo
            if (confirm("Are you sure you want to delete this todo?")) {
                // Deletes a todo by removing it from the array
                this.todos.splice(index, 1);
            } else {
                return;
            }
        },
        updateTime() {
            // Gets the actual date/time from moments.js
            this.newTodo.todoDate = moment().format("YYYY[-]MM[-]DDTHH:mm");
        },
        sortImportanceLow() {
            // Sorts todos after lowest importance by sorting after the importanceNum dependency
            this.todos.sort((a, b) =>
                a.todoImportance.num > b.todoImportance.num ? 1 : -1
            );
        },
        sortImportanceHigh() {
            // Sorts todos after highest importance by sorting after the importanceNum dependency
            this.todos.sort((a, b) =>
                a.todoImportance.num < b.todoImportance.num ? 1 : -1
            );
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

.todo-list-item {
    position: relative;
    display: flex;
    flex-direction: column;
    width: 100%;
    padding: 2.3rem 2rem 2rem;
    margin: 1rem 0;
    box-shadow: 0px 0px 20px var(--clr-accent-darkest);
    border-radius: 10px;
    color: #fff;
    overflow: hidden;
    height: 77px;
    transition: height ease-in-out 0.3s;
}

.todo-checked-overlay {
    position: absolute;
    left: 0;
    top: 0;
    bottom: 0;
    right: 0;
    background-color: rgba(0, 0, 0, 0.5);
    transition: opacity ease-in-out 0.25s;
    z-index: 100;
    pointer-events: none;
    opacity: 0;
}

.todo-list-item:hover {
    height: 160px;
}

.lvl-clr-green {
    background: rgb(111, 200, 65);
    background: linear-gradient(
        90deg,
        rgba(111, 200, 65, 0.8491771708683473) 0%,
        rgba(111, 200, 65, 0) 35%
    );
}

.lvl-clr-yellow {
    background: rgb(197, 200, 65);
    background: linear-gradient(
        90deg,
        rgba(197, 200, 65, 0.8491771708683473) 0%,
        rgba(197, 200, 65, 0) 35%
    );
}

.lvl-clr-red {
    background: rgb(200, 65, 65);
    background: linear-gradient(
        90deg,
        rgba(200, 65, 65, 0.8491771708683473) 0%,
        rgba(200, 65, 65, 0) 35%
    );
}

.printed-todo-header-container {
    display: grid;
    grid-template-columns: 3.5fr 1.5fr 0.25fr;
    gap: 1rem;
    align-items: center;
}

.printed-todo-title,
.printed-todo-editing-title {
    text-align: left;
    margin-left: 5px;
    font-size: 2rem;
}

.printed-todo-date {
    font-size: 1.2rem;
    justify-self: start;
    padding: 0 1rem;
}

.printed-todo-editing-deadline {
    position: relative;
}

.printed-todo-editing-deadline[type="datetime-local"]::-webkit-calendar-picker-indicator {
    cursor: pointer;
    position: absolute;
    padding: 0;
    width: 100%;
    height: 100%;
    color: transparent;
    background: transparent;
}

.printed-todo-editing-deadline[type="datetime-local"]::after {
    content: url("https://api.iconify.design/akar-icons/calendar.svg?color=white");
}

.printed-todo-checkbox-container {
    display: grid;
    place-items: center;
}

.printed-todo-checkbox-wrapper {
    display: grid;
    justify-self: end;
    place-items: center;
    padding: 0.5rem;
}

.todo-checkbox[type="checkbox"] {
    position: relative;
    appearance: none;
    width: 2.3rem;
    height: 2.3rem;
    border: 0.2rem solid #fff;
    border-radius: 0.4rem;
    display: grid;
    place-content: center;
    cursor: pointer;
}

.todo-checkbox[type="checkbox"]::before {
    position: absolute;
    content: "";
    width: 12px;
    height: 14px;
    background-color: var(--clr-primary);
    right: -4px;
    top: -4px;
    border-radius: 50px;
}

.todo-checkbox[type="checkbox"]::after {
    content: url("https://api.iconify.design/bi/check.svg?color=white");
    scale: 0;
    rotate: -45deg;
    opacity: 0;
    transition: ease-in-out 0.25s;
    margin: 6px -1px 0 0;
}

.todo-checkbox[type="checkbox"]:checked::after {
    scale: 2.2;
    rotate: -12deg;
    opacity: 1;
    transform: translate(1px, -0.5px);
}

.todo-checkbox[type="checkbox"]:checked ~ .todo-checked-overlay {
    opacity: 1;
}

.printed-todo-line {
    width: 100%;
    height: 1px;
    background: radial-gradient(
        closest-side,
        var(--clr-accent-light),
        transparent
    );
    margin: 1rem 0;
}

.printed-todo-dropdown-container {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: flex-start;
}

.printed-todo-desc,
.printed-todo-editing-desc {
    width: 75%;
    text-align: left;
    margin-left: 5px;
    font-size: 1.2rem;
    height: 6rem;
    padding-top: 5px;
}

.printed-todo-editing-title,
.printed-todo-editing-desc,
.printed-todo-editing-deadline {
    background: linear-gradient(90deg, transparent, #4ea69986);
    color: var(--clr-text);
    padding: 5px 5px 5px 0;
}

.printed-todo-editing-title {
    font-weight: 700;
}

.printed-todo-button-container {
    align-self: end;
}

.printed-todo-button-wrapper {
    display: flex;
    flex-direction: row;
    align-items: baseline;
    justify-content: flex-end;
}

.printed-todo-edit-checkbox,
.printed-todo-delete-btn {
    background-color: transparent;
    color: #fff;
    padding: 0.5rem 0.5rem 0 0.5rem;
    cursor: pointer;
}

.printed-todo-edit-checkbox[type="checkbox"] {
    appearance: none;
    content: "";
    margin-right: 5px;
}

.printed-todo-edit-checkbox[type="checkbox"] {
    position: relative;
    appearance: none;
    width: 2.3rem;
    height: 2.3rem;
    border: 0.2rem solid #fff;
    border-radius: 0.4rem;
    display: grid;
    place-content: center;
    cursor: pointer;
}

.printed-todo-edit-checkbox[type="checkbox"]::before {
    position: absolute;
    content: "";
    width: 12px;
    height: 14px;
    background-color: var(--clr-primary);
    right: -4px;
    top: -4px;
    border-radius: 50px;
}

.printed-todo-edit-checkbox[type="checkbox"]::after {
    content: url("https://api.iconify.design/humbleicons/pencil.svg?color=white");
    scale: 1.5;
    opacity: 1;
    transition: ease-in-out 0.25s;
    margin: 6px -1px 0 0;
    transform: translate(3.5px, -5px);
}

.printed-todo-edit-checkbox[type="checkbox"]:checked::after {
    animation: todo-edit-anim 0.5s;
}

@keyframes todo-edit-anim {
    0% {
        rotate: 0deg;
    }
    50% {
        rotate: 10deg;
    }
    100% {
        rotate: 0deg;
    }
}

.printed-todo-delete-btn {
    margin-left: 5px;
}
</style>
