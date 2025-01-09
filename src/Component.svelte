<script>
  import { getContext, onDestroy } from "svelte";

  export let host;
  export let port;
  export let username;
  export let password;
  export let keepAlive;
  export let maxReconnect;
  export let topic = "mqtt";
  export let jsonMessage;

  export let onConnect;
  export let onDisconnect;
  export let onMessage;

  const { styleable, builderStore, Provider } = getContext("sdk");
  const component = getContext("component");

  import mqtt from "mqtt/dist/mqtt.esm";

  $: url = "ws://" + host + ":" + (port || "8083") + "/mqtt";

  const options = {
    // Clean session
    clean: true,
    connectTimeout: 4000,
    keepAlive,
  };

  let connected;
  let status = "Offline";
  let statusTopic = "Not Subscribed";
  let client;
  let testResultPost;
  let testResultReceive;
  let decoded;
  let lastMessageTs;

  $: inBuilder = $builderStore.inBuilder;
  $: initializeClient(url, options, topic);

  const handleTest = (e) => {
    testResultPost = "Posting...";
    testResultReceive = "Waiting to receive...";
    client.publish(
      topic,
      JSON.stringify({ Budibase: "is awesome !" }),
      function (error) {
        if (!error) {
          testResultPost = "Posted Successfully - " + new Date().toUTCString();
        }
      }
    );
  };

  const initializeClient = (url, options) => {
    if (client || !topic) {
      client.end();
      client = null;
      connected = undefined;
      if (!topic) return;
    }

    client = mqtt.connect(url, options);

    client.on("connect", function () {
      status = "Connected";
      connected = true;
      onConnect?.();
      // Subscribe to a topic
      client.subscribe(topic, function (err) {
        if (!err) {
          statusTopic = "Subscribed";
        } else console.log(err);
      });
    });

    // Receive messages
    // message comes in as a buffer and needs to be converted to string.
    client.on("message", function (topic, message) {
      lastMessageTs = new Date();

      if (testResultPost)
        testResultReceive =
          "Received Successfully - " + new Date().toUTCString();

      if (jsonMessage) {
        try {
          var payload = message?.toString();
          decoded = JSON.parse(payload ?? "{}");
        } catch (ex) {
          decoded = {};
        }
      } else {
        decoded = message?.toString();
      }

      onMessage?.({ topic, message: decoded });
      console.log("Message Received : ", decoded);
    });

    client.on("reconnect", function () {
      onDisconnect?.();
      console.log("Reconnecting...");
    });
  };

  onDestroy(() => {
    if (connected) onDisconnect?.();
    client.end();
    client = undefined;
  });
</script>

<div use:styleable={$component.styles}>
  <Provider data={{ lastMessage: decoded, at: lastMessageTs, connected }} />
  {#if inBuilder}
    <div class="super-mqtt">
      <div class="super-mqtt-status">
        <span class:connected>{status} - {host}</span>
        <span>{statusTopic} - {topic}</span>
      </div>
      <button
        class="spectrum-Button spectrum-Button--fill spectrum-Button--accent spectrum-Button--sizeM"
        on:click={handleTest}
      >
        Test
      </button>

      {#if testResultPost || testResultReceive}
        <div class="super-mqtt-test">
          <span>{testResultPost}</span>
          <span>{testResultReceive}</span>
        </div>
      {/if}
    </div>
  {/if}
</div>

<style>
  .super-mqtt {
    padding: 1rem;
    display: flex;
    justify-items: space-between;
    align-items: center;
    font-size: 16px;
  }
  .super-mqtt-test {
    padding-left: 2rem;
    display: flex;
    flex-direction: column;
    justify-items: space-between;
  }
  .super-mqtt-status {
    padding-right: 3rem;
    display: flex;
    flex-direction: column;
    justify-items: space-between;
  }
  .connected {
    color: var(--spectrum-global-color-green-500);
  }
</style>
