<template>
  <div>
    <table>
        <tr>
            <th>#</th>
            <th>Title</th>
            <th>Referenced tasks</th>
        </tr>
        <tr
            v-for="issue in issues"
            :key="issue.node_id"
        >
            <td>
                <a :href="issue.html_url">
                    #{{ issue.number }}
                </a>
            </td>
            <td>
                {{ issue.title }}
            </td>
            <td v-if="!issue.timeline">
                <button
                    @click="loadTimeline(issue)"
                >Load</button>
            </td>
            <td v-else>
                <table>
                    <tr>
                        <th>#</th>
                        <th>Title</th>
                    </tr>
                    <tr
                        v-for="t in issue.timeline"
                        :key="t.id"
                    >
                        <td>
                            <a :href="t.source.issue.html_url">
                                #{{ t.source.issue.number }}
                            </a>
                        </td>
                        <td>
                            {{ issue.title }}
                        </td>

                    </tr>
                </table>
            </td>
        </tr>
    </table>
  </div>
</template>

<script>
    import Vue from 'vue'

    const octokit = require('@octokit/rest')(),
        owner = '...',
        repo = '...';

    export default {
        name: 'GitTable',

        props: {
            username: {
                type: String,
                required: true,
            },
            token: {
                type: String,
                required: true,
            },
        },

        data() {
            return {
                issues: [],
            };
        },

        mounted() {
            octokit.authenticate({
                type: 'token',
                token: this.token,
            })
            const q = `repo:${owner}/${repo} is:issue assignee:${this.username} is:open`;

            octokit.search.issues({q}).then(result => {
                this.issues = result.data.items;
            })
        },
        
        methods: {
            loadTimeline({ number }) {
                octokit.issues.getEventsTimeline({
                    owner, 
                    repo, 
                    number,
                    headers: {
                        accept: 'application/vnd.github.mockingbird-preview+json',
                    },
                }).then(result => {
                    const issue = this.issues.find(issue => issue.number == number)
                    Vue.set(issue, 'timeline', result.data.filter(d => d.event=='cross-referenced'))
                })
                
            },
        },
    }
</script>

<style scoped>
    td, th {
        border: 1px solid black;
    }
</style>

