<script>
    import {user} from './stores';

    let _user;
    let tmpuser;
    let auth_token;
    let checkAccess;
    let userId;
    let todo;
    let todoName;
    let todoDesc;

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



    const logout=()=>{
        user.set(null);
        localStorage.clear();
    }
   
</script>

{#if user && _user}
    <h3>Welcome {_user?.profile?.username}</h3>
	<button on:click={logout}>Logout </button>
    <button on:click={checkPublic}>Check public access</button>
    <button on:click={checkMod}>Check moderator access</button>
    <button on:click={checkAdmin}>Check admin access</button>
    <button on:click={loadTodos}>Load Todos</button>

    {#if checkAccess!=undefined && checkAccess!="todo"}
        <p>{checkAccess}</p>
    {:else if checkAccess=="todo"}
        {#each todo as item}
            <h3>{item["name"]}</h3>
            <p>{item["description"]}</p>
        {/each}
    {/if}
{/if}
