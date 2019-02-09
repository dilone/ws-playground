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
            <div class="version">
              <div class="demo version-section"><a href="https://github.com/cuatrokb/ws-playground" class="github-corner" aria-label="View source on GitHub">
                  <svg width="80" height="80" viewBox="0 0 250 250" style="fill:#151513; color:#fff; position: absolute; top: 0; border: 0; right: 0;" aria-hidden="true">
                    <path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path>
                    <path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2" fill="currentColor" style="transform-origin: 130px 106px;" class="octo-arm"></path>
                    <path d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z" fill="currentColor" class="octo-body"></path>
                  </svg></a>
              </div>
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
                            <button class="button is-primary" @click="startWebSocket()"><i class="fas fa-plug"></i>&nbsp;&nbsp;Connect</button>
                        </p>
                        <p class="control is-marginless" v-show="connected">
                            <button class="button is-danger" @click="stopWebSocket()"><i class="fas fa-power-off"></i>&nbsp;&nbsp;Disconnect</button>
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
                            <button class="button" @click="sendMessage()"><i class="fas fa-paper-plane"></i>&nbsp;&nbsp;Send</button>
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
