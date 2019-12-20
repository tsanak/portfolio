---
date: 2019-12-19
author: Nichlas W. Andersen
title: Make your life easier with Vue.js
excerpt: Transitioning from Jquery to Vue.js

---
## Context
The purpose of this mini project is to showcase the value of a modern frontend framework. In this mini project we will build the same app twice; once with Jquery and once with Vue.
This is a very basic application (no routing, no store). Also this is the first post I have ever written so please excuse any typos and syntactical errors 🙂.

## Application Requirements
1. a list with all the todos
2. a counter that says how many todos are left
3. a way to insert new todos
4. a way to remove todos
5. a message when there are no todos left

Both approaches have some preinserted todo items.

## Let's get started
### Jquery implementation

I ended up with the below html code:

```html
    <div class="center-me">
        <div id="container">
            <h2 class="text-center">Best todo app</h2>
            <h3 class="text-center"><span id="total-todos"></span> things to do</h3>
            <div class="text-center">
                <input type="text" id="newTodo" placeholder="Go to the gym">
                <button id="addTodo">Add todo</button>
            </div>
            <div id="todos">
                <p class="text-center" id="no-todos">You don't have anything to do :(</p>
                <ul id="todo-list"></ul>
            </div>
        </div>
    </div>
```
and the below javascript code:
```javascript
//Array to hold the todo items
    var todos = [
      { id:1, text: 'Learn JavaScript' },
      { id:2, text: 'Learn Vue' },
      { id:3, text: 'Build something awesome' },
      { id:4, text: 'Go to London' },
      { id:5, text: 'Kick ass' },
      { id:6, text: 'Much Profit' },
      { id:7, text: 'Wow' },
      { id:8, text: 'Manemizjef' },
      { id:9, text: 'etc' },
    ];
//Add todo items in the #todo-list
    function addTodosInDOM() {
        for (var i = 0; i < todos.length; i++) {
            var todo = todos[i];
            $('#todo-list').append("<li todo-id='" + todo.id + "'><span>" + todo.text + "</span> <span class='delete-todo'>X</span></li>");
        }
        if(todos.length == 0) {
            $('#no-todos').show();
        }
    }
//Remove a todo when clicking on #delete-todo
    function removeTodo(id) {
        for (var i = 0; i < todos.length; i++) {
            var todo = todos[i];
            if(todo.id == id) {
                todos.splice(i, 1);
                break;
            }
        }
    }
//Add todo item in the todos array and update the dom
    function addTodo() {
        var newId = todos.length;
            var todo = {
                id: newId,
                text: "",
            }
        //Get new todo text
        var todoText = $('#newTodo').val();
        if(todoText == "") return;
        todo.text = todoText;
        todos.push(todo);
        //Update the dom
        $('#no-todos').hide();    
        $('#todo-list').append("<li todo-id='" + todo.id + "'><span>" + todo.text + "</span> <span class='delete-todo'>X</span></li>");
        $('#newTodo').val("");
        $('#total-todos').text(todos.length);
    }

//When the DOM is ready for JavaScript code to execute
    $(document).ready(function(){
        addTodosInDOM();
        $('#total-todos').text(todos.length);

        $('body').on('click', '.delete-todo', function(e){
            var target = e.target;
            var li = $(target).parent();
            var id = $(li).attr('todo-id');
            //Remove element from dom
            $(li).remove();
            //Remove todo from the local data
            removeTodo(id);
            if(todos.length == 0) {
                $('#no-todos').show();
            }
            //Update todo counter
            $('#total-todos').text(todos.length);
        });
        //When clicking 'enter' inside the input addTodo
        $( "#newTodo" ).keyup(function(event) {
            if(event.which == 13) addTodo();
        });

        $('#addTodo').click(function(){
            addTodo();
        });
    });
```
As you can see we need to manually update the DOM each time the todo items change.

### Vue implementation

I changed the html a little bit and ended up with the below code:
```html
    <div class="center-me" id="app">
        <div id="container">
            <h2 class="text-center">Best todo app</h2>
            <h3 class="text-center">{{ totalTodos }} things to do</h3>
            <div class="text-center">
                <input type="text" placeholder="Go to the gym" v-model.trim="newTodo" @keyup.enter="addTodo">
                <button @click="addTodo">Add todo</button>
            </div>
            <div id="todos">
                <p class="text-center" v-if="noTodos">You don't have anything to do :(</p>
                <ul id="todo-list" v-else>
                    <li v-for="(todo,index) in todos">
                        <span>{{ todo.text }}</span> <span class="delete-todo" @click="deleteTodo(index)">X</span>
                    </li>
                </ul>
            </div>
        </div>
    </div>
```
and the below javascript code:
```javascript
//Initialize the vue instance
var app = new Vue({
        el: '#app',
        data: {
            //Preinserted todo items
            todos: [
                { text: 'Learn JavaScript' },
                { text: 'Learn Vue' },
                { text: 'Build something awesome' },
                { text: 'Go to London' },
                { text: 'Kick ass' },
                { text: 'Much Profit' },
                { text: 'Wow' },
                { text: 'Manemizjef' },
                { text: 'etc' },
            ],
            newTodo: ''
        },
/*
Computed properties provides us with a way to show the data dependecies
and bind our data to some properties.
E.g: the value of totalTodos is depended on the length of the todos array;
     so when an item is added/deleted from the todos array(the length of the array changes)
     Vue updates the value of the computed property totalTodos.
     Hence we do not need to worry about manually updating the DOM whenever 
     todos array changes
Read more about computed properties: https://vuejs.org/v2/guide/computed.html
*/
        computed: {
            noTodos() {
                return this.todos.length == 0;
            },
            totalTodos() {
                return this.todos.length;
            }
        },
        methods: {
            deleteTodo(index) {
                this.todos.splice(index, 1);
            },
            addTodo() {
                if(this.newTodo == "") return;
                this.todos.push({
                    text: this.newTodo
                });
                this.newTodo = '';
            }
        }
    });
```
### Small explanation for `{{}}`, v-if, v-else, v-for and other strange symbols.
#### {{ example }}
Vue uses the mustache (`{{ }}`) template syntax to declaratively render data to the DOM.
```html
<h3 class="text-center">{{ totalTodos }} things to do</h3>

<!-- 
    Will output 
-->
<h3 class="text-center">9 things to do</h3>
<!-- 
    if we add one more item to the todos array, it will output
-->
<h3 class="text-center">10 things to do</h3>
```
#### v-if & v-else
Vue uses these directives for managing the conditional render of the elements. 
   e.g 
   ```html 
<!-- will only appear when noTodos === true -->
   <p class="text-center" v-if="noTodos">You don't have anything to do :(</p>
<!-- else the todo list will be rendered -->
   <ul id="todo-list" v-else>
       <li v-for="(todo,index) in todos">
           <span>{{ todo.text }}</span> <span class="delete-todo" @click="deleteTodo(index)">X</span>
       </li>
   </ul>
   ```

#### v-for
The v-for directive can be used for displaying a list of items using the data from an Array
```html
<!-- Loops through the todos array and outputs a li element foreach todo item -->
<li v-for="(todo,index) in todos">
<span>{{ todo.text }}</span> <span class="delete-todo" 
@click="deleteTodo(index)">X</span>
</li>
```

#### v-model
Creates a two-way data binding between the input and the newTodo.
Whenever one of them changes the other one gets updated.
    .trim is a modifier and it automatically trims the user input.
```html
<input type="text" placeholder="Go to the gym" v-model.trim="newTodo" @keyup.enter="addTodo">
```

#### @ / v-on
@ is a shorthand of v-on.
It's usage is to bind a method/ function to an event
```html
<!-- Here we bind the click event to the deleteTodo method -->
<span class="delete-todo" 
@click="deleteTodo(index)">X</span>
<!-- 
    Here we bind the keyup event to the addTodo method 
    .enter is a modifier on the keyup event, it means that we bind the addTodo,
    only when the user releases the 'enter' key
-->
<input type="text" placeholder="Go to the gym" v-model.trim="newTodo" @keyup.enter="addTodo">
```

## Reference
- Official documentation:  https://vuejs.org/v2/guide/ 

I personally found the official documentation to be very well written and helpful.

You can find the code on gihub: https://github.com/tsanak/todoApp