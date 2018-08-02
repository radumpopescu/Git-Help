<template>
    <div id="app">
        <div>
            Username:
            <input ref="usernameInput"/>
            <button @click="setUsername">Set</button>
        </div>
        <div>
            Token:
            <input ref="tokenInput"/>
            <button @click="setToken">Set</button>
        </div>

        <git-table
            v-if="username && token"
            :username="username"
            :token="token"
        ></git-table>
  </div>
</template>

<script>
import GitTable from './components/GitTable.vue'

export default {
    name: 'app',
    data() {
        return {
            username: '',
            token: '',
        };
    },
    mounted() {
        this.username = localStorage.getItem('myUsername');
        this.$refs.usernameInput.value = this.username;

        this.token = localStorage.getItem('myToken');
        this.$refs.tokenInput.value = this.token;
    },
    methods: {
        setUsername() {
            const username = this.$refs.usernameInput.value;
            localStorage.setItem('myUsername', username);
            this.username = username
        },

        setToken() {
            const token = this.$refs.tokenInput.value;
            localStorage.setItem('myToken', token);
            this.token = token
        },
    },
    components: {
        GitTable
    }
}
</script>

