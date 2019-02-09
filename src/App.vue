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
    <section class="section main-content">
        <div class="columns">
            <div class="column is-8 is-offset-2 pt2">
                <div class="box form-box">
                    <div class="field">
                        <p class="control is-expanded">
                            <label class="label">Socket URL</label>
                        </p>
                    </div>
                    <div class="field is-grouped">
                        <p class="control is-expanded">
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
                        <p class="control is-expanded">
                            <input type="text" class="input" v-model="message">
                        </p>
                        <p class="control">
                            <button class="button" @click="sendMessage()">Send &nbsp;&nbsp;<i class="fas fa-paper-plane"></i></button>
                        </p>
                    </div>
                </div>
                <div class="box result-box">
                    <label class="label">
                        Results
                        <span class="has-text-success" v-if="connected">CONNECTED&nbsp;&nbsp;<i class="fas fa-dot-circle"></i></span>
                        <span class="has-text-danger" v-else>DISCONNECTED&nbsp;&nbsp;<i class="fas fa-dot-circle"></i></span>
                    </label>
                    <div class="content">
                        <ul>
                            <li class="field" :key="index" v-for="(item, index) in results.slice().reverse()">
                                {{ item }}
                            </li>
                        </ul>
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
            socket_path: 'ws://echo.websocket.org',
            connected: false,
            message: 'OMG! HTML5 WebSocket works',
            results: [],
        }
    },
    watch: {
        results: function() {
            if (this.results.length > 20) {
                this.results.shift();
            }
        }
    },
    methods: {
        startWebSocket() {
            if (this.socket_path && this.socket_path.trim().length) {
                this.newSocket()
            } else {
                this.results.push('INVALID SOCKET URL');
            }
        },
        newSocket() {
            this.ws = new WebSocket(this.socket_path);
            this.ws.onopen = event => {
                this.results.push("CONNECTED");
                this.connected = true;
            };
            this.ws.onmessage = event => {
                this.results.push(JSON.stringify(event.data));
            };
            this.ws.onclose = e => {
                this.results.push(e);
                this.connected = false;
                this.ws.close();
            };
            this.ws.onerror = e => {
                this.results.push(e);
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

.main-content {
    padding: 1.5rem 1.5rem;
}

.result-box .content {
    border: 2px solid #dbdbdb;
    border-radius: 2px;
    padding: 1rem 0rem;
    height: 40vh;
    overflow: scroll;
    overflow-x: hidden;
}

.result-box label span {
    float: right;
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
