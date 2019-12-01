<script>
  import TopAppBar, {
    Row,
    Section,
    Title,
    FixedAdjust
  } from "@smui/top-app-bar";
  import IconButton from "@smui/icon-button";
  import Checkbox from "@smui/checkbox";
  import FormField from "@smui/form-field";
  import Card, {
    Content,
    PrimaryAction,
    Media,
    MediaContent,
    Actions,
    ActionButtons,
    ActionIcons
  } from "@smui/card";
  import Button from "@smui/button";
  import Textfield from "@smui/textfield";


  var client = mqtt.connect("ws://127.0.0.1:1884");

  client.on("connect", function() {
    client.subscribe("user/other", function(err) {
      if (!err) {
      }else{
        console.error(err)
      }
    });

     client.subscribe("user/me", function(err) {
      if (!err) {
      }else{
        console.error(err)
      }
    });
  });

  client.on("message", function(topic, received, packet) {
		// message is Buffer
    console.log(arguments);
    if(topic == "user/me") {
      message = ""
    }
    
		messages[messages.length] = { text: received.toString(), mine: topic === "user/me" ? true : false };
		
    // client.end();
  });

  /** @type {String} */
  let message = "";
  let messages = [];

  function add(event) {
    if (message.trim() === "") {
      return;
    }
		// messages[messages.length] = { text: message, mine: true };
    client.publish("user/me", message);
		
  }

  let title = "Messages";
  let adjust = FixedAdjust;
</script>

<style>
  .container:last-child {
    padding-bottom: 15px;
  }

  main {
    /* display: flex; */
    /* align-content: center; */
    /* flex-direction: row; */
    height: 100%;
  }
</style>

<app>
  <TopAppBar variant="static">
    <Row>
      <Section>
        <IconButton class="material-icons">menu</IconButton>
        <Title>{title}</Title>
      </Section>
      <Section align="end" toolbar>
        <IconButton class="material-icons" aria-label="Download">
          file_download
        </IconButton>
        <IconButton class="material-icons" aria-label="Print this page">
          print
        </IconButton>
        <IconButton class="material-icons" aria-label="Bookmark this page">
          bookmark
        </IconButton>
      </Section>
    </Row>
  </TopAppBar>
  <div
    style="display: flex; flex-direction: column; max-height: 100%; height:
    calc(100% - 64px); width: 45%; margin: auto;">
    <div
      clas="container"
      style="flex: 1; display: flex; flex-direction: column; overflow-y: auto;">
      {#each messages as message}
        <Card
          style="padding: 10px; margin: 15px 15px 0px 15px; width: max-content; {message.mine == false ? "background-color: var(--mdc-theme-primary,#6200ee); color: #fff; align-self: flex-start;" : "align-self: flex-end;"}">
          {message.text}
        </Card>
      {/each}
    </div>
    <Card
      style="flex: 0 0; padding: 10px; align-items: center; margin: 15px;
      display: flex; flex-direction: row;">
      <Textfield
        fullwidth
        lineRipple={false}
        style="flex: 1;"
        bind:value={message}
        color="primary"
        label="Message" 
        on:keypress={(event) => { if(event.key === "Enter") { add() } } }/>
      <Button variant="raised" style="flex: 0 100px;" on:click={add}>
        Send
      </Button>
    </Card>

  </div>
</app>
