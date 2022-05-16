<script>
  import { username, user } from './user';

  function signout() {
    user.leave();
    username.set('');
  }
</script>


<header>
<h1><center>A P2P Chat App</center></h1>
  {#if $username}
    <div class="user-bio">
      
      <span>Hello, <strong>{$username}</strong></span>
      <img src={`https://avatars.dicebear.com/api/initials/${$username}.svg`} alt="avatar" /> 
    

    <button class='signout-button' on:click={signout}>Sign Out</button>
</div>
    {:else}

      <h3><center>Powered by Gun.js</center></h3>
  {/if}
</header>