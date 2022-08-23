<script>
import CustomButton from './CustomButton.svelte';

    import {user} from './stores';

    let _user;
    let tmpuser;
    let auth_token;
    let checkAccess;
    let userId;
    let todo;

    user.subscribe(data=>_user=data)

    const checkPublic=()=>{
        tmpuser=JSON.parse(window.localStorage.getItem('user'))
        auth_token=(tmpuser["auth_token"])["access_token"]
        const endpoint="http://localhost:8080/api/test/all"
        fetch(endpoint,
            {
                method:'GET',
                headers: {
                    'Content-Type': 'text/plain',
                    'Authorization': 'Bearer '+ auth_token
            }}).then(response=>response.text())
            .then(data=>{
                console.log(data)
                if(data.ErrorCode){
                    if(data.ErrorCode==401){
                        checkAccess="Unauthorized"
                    } else {
                        checkAccess="Error occurred while making request"
                    }
                } else {
                    checkAccess=data
                }
            })
    }

    const checkMod=()=>{
        tmpuser=JSON.parse(window.localStorage.getItem('user'))
        auth_token=(tmpuser["auth_token"])["access_token"]
        const endpoint="http://localhost:8080/api/test/mod"
        fetch(endpoint,
            {
                method:'GET',
                headers: {
                    'Content-Type': 'text/plain',
                    'Authorization': 'Bearer '+ auth_token
            }}).then(response=>response.text())
            .then(data=>{
                console.log(data)
                if(data.ErrorCode){
                    if(data.ErrorCode==401){
                        checkAccess="Unauthorized"
                    } else {
                        checkAccess="Error occurred while making request"
                    }
                } else {
                    checkAccess=data
                }
            })
    }

    const checkAdmin=()=>{
        tmpuser=JSON.parse(window.localStorage.getItem('user'))
        auth_token=(tmpuser["auth_token"])["access_token"]
        const endpoint="http://localhost:8080/api/test/admin"
        fetch(endpoint,
            {
                method:'GET',
                headers: {
                    'Content-Type': 'text/plain',
                    'Authorization': 'Bearer '+ auth_token
            }}).then(response=>response.text())
            .then(data=>{
                console.log(data)
                if(data.ErrorCode){
                    if(data.ErrorCode==403){
                        checkAccess="Unauthorized"
                    } else {
                        checkAccess="Error occurred while making request"
                    }
                } else {
                    checkAccess=data
                }
            })
    }

    function loadTodos() {
        tmpuser=JSON.parse(window.localStorage.getItem('user'))
        userId = (tmpuser["profile"])["id"]
        const endpoint="http://localhost:8761/api/todo/"+userId
        console.log(endpoint)
        fetch(endpoint,
            {
                method:'GET'
            }).then(data => {
                return data.json();
            })
            .then(data2 => {
                todo = data2;
                console.log(todo)
                checkAccess="todo"
            });
        }

    function changeStatus(todo) {
        if (todo.status=="Y") {
            todo.status="N";
        } else {
            todo.status="Y";
        }
    }

    function editTodo() {
        console.log(todo);
    }

    const logout=()=>{
        user.set(null);
        localStorage.clear();
    }
   
</script>

<style>

    #header {
        position:sticky;
        top:10px;
        margin-bottom: 50px;
        background-color:plum;
    }

    #todo-section {
        display:table;
    }

    /* #todo-item {
        display:table-cell;
        border-radius: 10%;
        border: 1px black solid;
        width: 200px;
    } */

    .active-item {
        background: greenyellow;
    }

    .ended-item {
        background:indianred;
    }

</style>

{#if user && _user}
    <div id="header">
        <h3>Welcome {_user?.profile?.username}</h3>
        <button on:click={logout}>Logout </button>
        <button on:click={checkPublic}>Check public access</button>
        <button on:click={checkMod}>Check moderator access</button>
        <button on:click={checkAdmin}>Check admin access</button>
        <button on:click={loadTodos}>Load Todos</button>
    </div>

    {#if checkAccess!=undefined && checkAccess!="todo"}
        <p>{checkAccess}</p>
    {:else if checkAccess=="todo"}
        <div id ="todo-section">
            {#each todo as todoItem, i}
                {#if todoItem.status=="Y"}
                    <CustomButton item={todoItem}  on:click={editTodo}/>
                    <!-- <div on:click={changeStatus(item)} id="todo-item" class="active-item">
                        <h3>{item["name"]}</h3>
                        <p>{item["description"]}</p>
                        <p>{item["reminderDate"]}</p>
                    </div> -->
                {:else}
                    <CustomButton item={todoItem} />

                    <!-- <div id="todo-item" class="ended-item">
                        <h3>{item["name"]}</h3>
                        <p>{item["description"]}</p>
                        <p>{item["reminderDate"]}</p>
                    </div> -->
                {/if}
            {/each}
        </div>
    {/if}
{/if}
