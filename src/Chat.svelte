<script>
  import Login from './Login.svelte';
  import ChatMessage from './ChatMessage.svelte';
  import { onMount } from 'svelte';
  import { username, user } from './user';
  import debounce from 'lodash.debounce';
  import {create} from 'ipfs-http-client'
  const client = create('https://ipfs.infura.io:5001/api/v0')
  import GUN from 'gun';
  const db = GUN();

  let newMessage;
  let messages = [];

  let scrollBottom;
  let lastScrollTop;
  let canAutoScroll = true;
  let unreadMessages = false;

  function autoScroll() {
    setTimeout(() => scrollBottom?.scrollIntoView({ behavior: 'auto' }), 50);
    unreadMessages = false;
  }

  function watchScroll(e) {
    canAutoScroll = (e.target.scrollTop || Infinity) > lastScrollTop;
    lastScrollTop = e.target.scrollTop;
  }

  $: debouncedWatchScroll = debounce(watchScroll, 1000);

  onMount(() => {
    var match = {
      // lexical queries are kind of like a limited RegEx or Glob.
      '.': {
        // property selector
        '>': new Date(+new Date() - 1 * 1000 * 60 * 60 * 3).toISOString(), // find any indexed property larger ~3 hours ago
      },
      '-': 1, // filter in reverse
    };

    // Get Messages
    db.get('chat33')
      .map(match)
      .once(async (data, id) => {
        if (data) {
          // Key for end-to-end encryption
          const key = '#foo';

          var message = {
            // transform the data
            who: await db.user(data).get('alias'), // a user might lie who they are! So let the user system detect whose data it is.
            what: (await SEA.decrypt(data.what, key)) + '', // force decrypt as text.
            when: GUN.state.is(data, 'what'), // get the internal timestamp for the what property.
          };

          if (message.what) {
            messages = [...messages.slice(-100), message].sort((a, b) => a.when - b.when);
            if (canAutoScroll) {
              autoScroll();
            } else {
              unreadMessages = true;
            }
          }
        }
      });
  });

  async function sendMessage() {
    const secret = await SEA.encrypt(newMessage, '#foo');
    const message = user.get('all').set({ what: secret });
    const index = new Date().toISOString();
    db.get('chat33').get(index).put(message);
    newMessage = '';
    canAutoScroll = true;
    autoScroll();
  }

  async function sendMessagelink(string) {
    const secret = await SEA.encrypt(newMessage, '#foo');
    const message = user.get('all').set({ what: secret });
    const index = new Date().toISOString();
    db.get('chat33').get(index).put(message);
    newMessage = '';
    canAutoScroll = true;
    autoScroll();
  }



  async function test() {
    const data2 = await document.getElementById("file").files[0];
    
    
    

// add your data to to IPFS - this can be a string, a Buffer,
// a stream of Buffers, etc
    let results = await client.add(data2);
    
// we loop over the results because 'add' supports multiple 
// additions, but we only added one entry here so we only see
// one log line in the output

  // CID (Content IDentifier) uniquely addresses the data
  // and can be used to get it again.
  const fileHash = results.cid.string;

  console.log(results)
    }
    
    
</script>

<div class="container">
  {#if $username}
    <main on:scroll={debouncedWatchScroll}>
      {#each messages as message (message.when)}
        <ChatMessage {message} sender={$username} />
      {/each}

      <div class="dummy" bind:this={scrollBottom} />
    </main>
<div class="center">
    <form on:submit|preventDefault={sendMessage}>
      <input type="text" placeholder="Type a message..." bind:value={newMessage} maxlength="100" />

      <button type="submit" class='send-message' disabled={!newMessage}>Send Message</button>
    </form>

</div>


<div class="center">
  <form method="post" enctype=multipart/form-data on:submit={test}>
    <input type="file" name="file" id="file">
    <button type="submit" class='send-message'>Upload File</button>

    
  </form>

</div>



    {#if !canAutoScroll}
    <div class="scroll-button">
      <button on:click={autoScroll} class:red={unreadMessages}>
        {#if unreadMessages}
          💬
        {/if}

        👇
      </button>
    </div>
   {/if}
  {:else}
    <main>
      <Login />
    </main>
  {/if}
</div>
