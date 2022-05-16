<script>
  import { user } from './user';

  let username;
  let password;

  let strength = 0;
  let validations = [];
  let showPassword = false;

  function validatePassword(e) {
    const password = e.target.value;

    validations = [
      password.length > 5,
      password.search(/[A-Z]/) > -1,
      password.search(/[0-9]/) > -1,
      password.search(/[!@#$%^&*]/) > -1
    ];

    strength = validations.reduce(
		(acc, current) => acc + current);
  }

  function login() {
    user.auth(username, password, ({ err }) => err && alert(err));
  }

  function signup() {
    user.create(username, password, ({ err }) => {
      if (err) {
        alert(err);
      } else {
        login();
      }
    });
  }
  const navigateToSignup=()=>{}

</script>

<style>

  form {
    --text-color: #d08f8f;
    max-width: 500px;
  }

  .field {
    width: 100%;
    position:relative;
    border-bottom: 2px dashed var(--text-color);
    margin: 4rem 37rem 3rem;
  }
  .field2 {
    width: 100%;
    position:relative;
   
    margin: 2rem 42rem 3rem;
  }

  .label {
    color: var(--text-color);
    font-size: 1.2rem;
  }
  .strength {
    display: flex;
    height: 20px;
    width: 100%;
    margin: 3rem 37rem 1rem;
  }
  .bar-1 {
    background: linear-gradient(to right, red, orangered);
  }
  .bar-2 {
    background: linear-gradient(to right, orangered, yellow);
  }
  .bar-3 {
    background: linear-gradient(to right, yellow, yellowgreen);
  }
  .bar-4 {
    background: linear-gradient(to right, yellowgreen, green);
  }
  .bar {
    margin-right: 5px;
    height: 100%;
    width: 25%;
    transition: box-shadow 500ms;
    box-shadow: inset 0px 20px #1f1f1f;
  }
  .bar-show {
    box-shadow: none;
  }
  .label {
    z-index: -1;
    position: absolute;
    transform: translateY(-2rem);
    transform-origin: 0%;
    transition: transform 400ms;
  }
  .field:focus-within .label,
  .input:not(:placeholder-shown) + .label {
    transform: scale(0.8) translateY(-5rem);
  }

  .field::after {
    content: "";
    position: relative;
    display: block;
    height: 4px;
    width: 100%;
    background: red;
    transform: scaleX(0);
    transform-origin: 0%;
    /* left to right  */

    transition: transform 500ms ease;
    top: 2px;
  }

  .field:focus-within {
    border-color: transparent;
  }
  .field:focus-within::after {
    transform: scaleX(1);
  }

  .input {
    outline: none;
    border: none;
    overflow: hidden;
    margin: 0;
    padding: 0.25rem 0;
    background: none;
    color: white;
    font-size: 1.2rem;
    font-weight: bold;
  }
  

.button{
  
	background-color: #282c34; 
	border: none;
	color: white;
	padding: 15px 32px;
	text-align: center;
	text-decoration: none;
	display:flow-root;
	cursor: pointer;
	font-size: 1.25rem;
	margin: 2rem 50rem ;
  
}


  .input:valid {
    color: white;
  }
  .input:invalid {
    color: orangered;
  }
</style>


<main>
  <form>
    <div class="field">
      <input name="username" placeholder="" class="input" bind:value={username} />
      <label for="username" class='label'>Username</label>
    </div>

    <div class="field">
      <input type="password" name="password" placeholder="" class="input" on:input={validatePassword} bind:value={password} />
      <label for="password" class="label">Password</label>
    </div>

    <div class="strength">
      <span class="bar bar-1" class:bar-show={strength > 0}/>
      <span class="bar bar-2" class:bar-show={strength > 1}/>
      <span class="bar bar-3" class:bar-show={strength > 2}/>
      <span class="bar bar-4" class:bar-show={strength > 3}/>

    </div>
    

    <ul>
      <div class=field2>
      <li>{validations[0] ? '✔️' : '❌'} Must be at least 6 characters</li>
      <li>{validations[1] ? '✔️' : '❌'} Must contain a capital letter</li>
      <li>{validations[2] ? '✔️' : '❌'} Must contain a number</li>
      <li>{validations[3] ? '✔️' : '❌'} Must contain a special character</li>
    </div>
    </ul>
    
    <div class=field2>
      <input type="button" value="Log In" class="app-button" on:click={login}>
      <input type="button" value="Sign Up" class="app-button" on:click={signup}>
 </div>
      
  </form>
</main>


<!--<button class="login" on:click={login}>Login</button>
<button class="login"  on:click={signup}>Sign Up</button>-->
  
