<template>
    <li
        class="todo-list-item"
        :class="{
            'lvl-clr-green': importance.color === 'green',
            'lvl-clr-yellow': importance.color === 'yellow',
            'lvl-clr-red': importance.color === 'red',
            editMode: isEditing === true
        }"
    >
        <div
            class="todo-checked-overlay"
            :class="{ isChecked: overlay === true }"
        ></div>
        <div class="printed-todo-header-container">
            <h3 v-if="!isEditing" class="printed-todo-title">
                {{ title }}
            </h3>
            <input
                v-else
                type="text"
                name="todoEditTitle"
                class="printed-todo-editing-title"
                maxlength="25"
                v-model="title"
                @keyup.enter="() => submitEdit()"
            />
            <time v-if="!isEditing" class="printed-todo-date"
                >Due {{ date }}</time
            >
            <input
                v-else
                type="datetime-local"
                name="editDeadline"
                class="printed-todo-editing-deadline"
                v-model="date"
            />
            <div class="printed-todo-checkbox-container">
                <div class="printed-todo-checkbox-wrapper">
                    <input
                        type="checkbox"
                        name="todoCheckbox"
                        class="todo-checkbox"
                        @click="() => toggleOverlay()"
                    />
                </div>
            </div>
        </div>
        <div class="printed-todo-line"></div>
        <div class="printed-todo-dropdown-container">
            <p v-if="!isEditing" class="printed-todo-desc">
                {{ description }}
            </p>
            <textarea
                v-else
                type="text"
                name="todoEditDesc"
                class="printed-todo-editing-desc"
                maxlength="200"
                v-model="description"
                @keyup.enter="() => submitEdit()"
            />
            <div class="printed-todo-button-container">
                <div class="printed-todo-button-wrapper">
                    <input
                        type="checkbox"
                        name="todoEdit"
                        @click="() => submitEdit()"
                        class="printed-todo-edit-checkbox"
                    />
                    <button
                        class="printed-todo-delete-btn"
                        aria-label="Delete"
                        @click="() => $emit('remove')"
                    >
                        <svg
                            xmlns="http://www.w3.org/2000/svg"
                            width="24"
                            height="24"
                            preserveAspectRatio="xMidYMid meet"
                            viewBox="0 0 448 512"
                            aria-hidden="true"
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
</template>

<script>
export default {
    name: "TodoItem",

    props: ["title", "description", "importance", "date"],

    data() {
        return {
            isEditing: false,
            overlay: false
        };
    },

    methods: {
        toggleTodoEditing() {
            this.isEditing = !this.isEditing;
        },
        submitEdit() {
            // Submit redigeret kode her..!
            this.toggleTodoEditing();
        },
        toggleOverlay() {
            this.overlay = !this.overlay;
        }
    }
};
</script>

<style scoped>
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

.todo-list-item.editMode {
    height: 160px;
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

.todo-checked-overlay.isChecked {
    opacity: 1;
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