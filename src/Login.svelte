<script>
    
    import { useNavigate } from "svelte-navigator";
    const navigate = useNavigate();
    import {user} from './stores';

    let username;
    let password;
    let loading;

    let loginResponse={
        error:null,
        success:null,
        profile:null,
        auth_token:null
    }

    export let sdkoptions;

    const navigateToSignup=()=>navigate('/signup')

    const handleSubmit=()=>{
       let loginFields={username,password};
       const endpoint=`http://localhost:8080/api/auth/signin`;
       loading=true;
       loginResponse={
        error:null,
        success:null,
        profile:null,
        auth_token:null
        }
        fetch(endpoint,
        {
            method:'POST',
            headers: {
            'Content-Type': 'application/json'
            },
            body:JSON.stringify(loginFields)
        }).then(response=>response.json())
        .then(data=>{
            console.log(data)
            if(data.ErrorCode){
                if(data.ErrorCode==936){ 
                loginResponse={
                        ...loginResponse,
                        error:data.Message
                    }
                }
                else if(data.ErrorCode==1134){
                    loginResponse={
                        ...loginResponse,
                        error:data.Errors[0].ErrorMessage
                    }
                }
                else if(data.ErrorCode==400){
                    loginResponse={
                        ...loginResponse,
                        error:data.Message
                    }
                }else{

                    loginResponse={
                        ...loginResponse,
                        error:data.Description
                    }
                }
            }else{
                loginResponse={
                    ...loginResponse,
                    success:true,
                    profile:{
                        id:data.id,
                        username:data.username,
                        email:data.email,
                        roles:data.roles,
                    },
                    auth_token:{
                        access_token:data.accessToken,
                        ttl:data.expires_in
                    }
                }
                user.set(loginResponse)
                localStorage.setItem('user',JSON.stringify(loginResponse))
                navigate('/')
            }
        })
        .catch(error=>console.log(error))
        .finally(()=>loading=false);
    }

</script>

<h3>Login </h3>
<form on:submit|preventDefault={handleSubmit}>
    <input class="form-field" bind:value={username} type="text" placeholder="username" >
    <input class="form-field" bind:value={password} type="password" placeholder="Password" >
    <button disabled={loading} class="form-field">
        Login 
    </button>
</form>
{#if loginResponse.error}
	<p class="error">Error ❌ {loginResponse.error}</p>
{/if}
{#if loginResponse.success}
	<p class="success">
        Success ✔
    </p>
{/if}
<p>
    Don't have an account? 
    <strong class="link" on:click={navigateToSignup}>Sign up</strong>
</p>

<style>

</style>
