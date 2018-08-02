<template>
  <div>
    <table>
        <tr>
            <th>#</th>
            <th>Title</th>
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
        </tr>
    </table>
  </div>
</template>

<script>
    const octokit = require('@octokit/rest')()

    export default {
        name: 'GitTable',

        data() {
            return {
                issues: [],
            };
        },

        mounted() {
            octokit.authenticate({
                type: 'token',
                token: '...'
            })
            const q = 'repo:repo/project is:issue assignee:myUsername is:open';

            octokit.search.issues({q}).then(result => {
                this.issues = result.data.items;
            })

        },
    }
</script>

<style scoped>
    td, th {
        border: 1px solid black;
    }
</style>

