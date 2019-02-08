<template>
<div id="app">
    <section class="hero is-primary has-text-centered">
        <div class="hero-body">
            <div class="container">
                <h1 class="title">
                    Websocket Playground
                </h1>
                <h2 class="subtitle">
                    Online WebSocket Tester
                </h2>
            </div>
        </div>
    </section>
    <section class="section ">
        <div class="column is-8 is-offset-2">
            <div class="box form-box">
                <div class="field">
                    <p class="control is-expanded">
                        <label class="label">Socket URL</label>
                    </p>
                </div>
                <div class="field is-grouped">
                    <p class="control is-expanded form-input">
                        <input type="text" v-model="socket_path" placeholder="ws://127.0.0.1:3000/ws" class="input">
                    </p>
                    <p class="control is-marginless" v-show="!connected">
                        <button class="button is-primary" @click="startWebSocket()">Connect&nbsp;&nbsp;<i class="fas fa-plug"></i></button>
                    </p>
                    <p class="control is-marginless" v-show="connected">
                        <button class="button is-danger" @click="stopWebSocket()">Disconnect&nbsp;&nbsp;<i class="fas fa-power-off"></i></button>
                    </p>
                </div>
                <div class="field" v-if="connected">
                    <p class="control is-expanded">
                        <label class="label">Message</label>
                    </p>
                </div>

                <div class="field is-grouped" v-if="connected">
                    <p class="control is-expanded form-input">
                        <input type="text" class="input" v-model="message">
                    </p>
                    <p class="control">
                        <button class="button" @click="sendMessage()">Send &nbsp;&nbsp;<i class="fas fa-paper-plane"></i></button>
                    </p>
                </div>
            </div>
            <div class="box result-box">
                <label class="label">Results</label>
                <div class="content">
                    <div class="field" v-for="item in results">
                        <p>{{ JSON.stringify(item) }}</p>
                    </div>
                </div>
            </div>
        </div>
    </section>
</div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'

export default {
    name: 'app',
    data() {
        return {
            ws: null,
            socket_path: '',
            connected: false,
            message: '',
            results: [],
        }
    },
    methods: {
        startWebSocket() {
            this.ws = new WebSocket(this.socket_path);
            this.ws.onopen = event => {
                this.connected = true;
            };
            this.ws.onmessage = event => {
                this.loading = false;
                if (event.data) {
                    this.results.push(JSON.parse(event.data));
                }
            };
            this.ws.onclose = e => {
                this.connected = false;
                this.ws.close();
            };
        },
        stopWebSocket() {
            this.ws.close();
        },
        sendMessage() {
            this.ws.send(this.message);
        }
    }
}
</script>

<style media="screen">
html {
    background-color: #e7ecf0;
}

.result-box .content {
    border: 2px solid #dbdbdb;
    border-radius: 2px;
    padding: 1rem 0rem;
    height: 40vh;
    overflow: scroll;
    overflow-x: hidden;
}

.form-input {
    max-width: 610px;
}

.is-grouped button {
    min-width: 150px;
}

.result-box .content .field:not(:last-child) {
    margin-bottom: .75rem;
    padding-bottom: 1rem;
    border-bottom: 1px solid #ececec;
}

.result-box .content .field p {
    padding-left: 1rem;
}
.footer {
    background-color: #e7ecf0;
    padding: 2rem 1.5rem 1rem;
}
</style>
