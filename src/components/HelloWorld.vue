<template>
  <div>
      <p class="text-success" v-if="connected">Connected!</p>
      <p class="text-danger" v-else>Not connected!</p>

      <input type="text" v-model="ws_address" />
      <br />
      <button @click="disconnect" class="btn btn-danger" v-if="connected">Disconnect!</button>
      <button @click="connect" class="btn btn-success" v-else>Connect!</button>
  </div>
</template>

<script>
import ROSLIB from "roslib";

export default {
  name: "test",
  data() {
    return {
      ws_address: import.meta.env.VITE_WEBSOCKET_ADDRESS,
      connected: false,
      ros: null,
    }
  },
  methods: {
    connect() {
      this.ros.on("connection", () => {
       this.connected = true;
        console.log("Connected!");
      });
      this.ros.on("error", (error) => {
        console.log("Error connecting to websocket server: ", error);
      });
      this.ros.on("close", () => {
        this.connected = false;
        console.log("Connection to websocket server closed.");
      });
    },
    disconnect() {
      this.ros.close();
    }
  },
  mounted() {
    this.ros = new ROSLIB.Ros({
        url: this.ws_address,
    })

    this.ros.on("connection", () => {
      this.connected = true;
      console.log("Connected!");
    });

    let listener = new ROSLIB.Topic({
      ros : this.ros,
      name : '/tf',
      messageType : 'tf2_msgs/msg/TFMessage'
    });

    listener.subscribe(function(message) {
      console.log(message);
    });
    console.log(listener);
    console.log('123');
  }
}
</script>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
