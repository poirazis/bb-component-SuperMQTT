<script>
  import { getContext, onDestroy } from "svelte";
  import mqtt from "mqtt";

  export let host;

  export let protocol = "wss";
  export let username;
  export let password;
  export let keepAlive;
  export let maxReconnect;
  export let topic = "mqtt";
  export let jsonMessage;
  export let showTest;
  export let showStatus = true;

  export let onConnect;
  export let onDisconnect;
  export let onMessage;

  const { styleable, builderStore, Provider } = getContext("sdk");
  const component = getContext("component");

  let url = protocol + "://" + host;

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
  let inBuilder;
  let failedConnectionAttempts = 0;
  let maxFailedAttempts = 5;

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
      },
    );
  };

  const initializeClient = () => {
    if (client || !topic) {
      if (client) {
        client.end();
        client = null;
        connected = undefined;
      }
      if (!topic) return;
      status = "Invalid Topic";
    }

    try {
      client = mqtt.connect(url, options);

      status = "Connecting";

      client.on("connect", function () {
        status = "Connected";
        connected = true;
        failedConnectionAttempts = 0;
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
      });

      client.on("reconnect", function () {
        failedConnectionAttempts++;
        if (failedConnectionAttempts >= maxFailedAttempts) {
          status = "Error: Max reconnection attempts reached";
          client.end();
          client = null;
          connected = false;
          onDisconnect?.();
        } else {
          onDisconnect?.();
          status = `Reconnecting (${failedConnectionAttempts}/${maxFailedAttempts})`;
        }
      });

      client.on("error", function (error) {
        failedConnectionAttempts++;
        if (failedConnectionAttempts >= maxFailedAttempts) {
          status = "Error: Max reconnection attempts reached";
          client.end();
          client = null;
          connected = false;
        } else {
          status = `Connection error: ${error.message}`;
        }
      });
    } catch (error) {
      status = "Error: " + error.message;
    }
  };

  // Watch for changes
  $: if (host || protocol || topic) {
    failedConnectionAttempts = 0;
    url = protocol + "://" + host;
    initializeClient();
  }

  $: inBuilder = $builderStore.inBuilder;

  onDestroy(() => {
    if (connected) onDisconnect?.();
    if (client) client.end();
    client = undefined;
  });
</script>

<div use:styleable={$component.styles}>
  <Provider
    data={{ lastMessage: decoded, at: lastMessageTs, connected, status }}
  />
  {#if inBuilder && showTest}
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
  {:else}
    <div class="super-mqtt" class:connected>
      <i class="ri-plug-fill" />
      {#if showStatus}
        <span>{status}</span>
      {/if}
    </div>
  {/if}
</div>

<style>
  .super-mqtt {
    display: flex;
    justify-items: space-between;
    align-items: center;
    gap: 0.5rem;
    font-size: 13px;
    color: var(--spectrum-global-color-gray-500);

    &.connected {
      color: var(--spectrum-global-color-green-500);
    }
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
    color: var(--spectrum-global-color-gray-500);
  }
</style>
