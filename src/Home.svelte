<script>
    import {user} from './stores';

    let _user;
    let tmpuser;
    let auth_token;
    let checkAccess;    
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

    {#if checkAccess!=undefined}
        <p>{checkAccess}</p>
    {/if}
{/if}
