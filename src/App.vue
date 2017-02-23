<template>
    <div id="app" class="container">
        <!--<img src="./assets/logo.png">-->
        <h1 class="page-header">Insight Table Component</h1>
        <insight-table :fields="fields" :data="data" :pagination="pagination">
            <template slot="link" scope="props">
                <a href="#">{{props.rowData.name }}</a>
            </template>
            <template slot="status" scope="props">
            <span class="label" :class="{'label-success': props.rowValue == 1, 'label-danger': props.rowValue == 0}">
                {{ props.rowValue == 1 ? 'ACTIVE' : 'INACTIVE'}}
            </span>
            </template>
        </insight-table>
    </div>
</template>

<script>
    import InsightTable from './components/InsightTable'
    import users from './users'

    export default {
        name: 'app',
        components: {
            InsightTable
        },
        data () {
            return {
                data: require('./users'),
                pagination: {},
                sort: [{direction: 'asc', sortField: 'name', field: 'name'}],
                fields: [
                    {
                        name: 'name',
                        slot: 'link'
                    }, 'email', 'position',
                    {
                        name: 'status',
                        slot: 'status',
                        dataClass: 'text-center',
                        sortField: false
                    },
                    {
                        name: 'created_at',
                        title: 'Created at',
                        callback: this.formatDate
                    },
                    {
                        name: 'last_login',
                        title: 'Last login',
                        callback: this.formatDate
                    }
                ]
            }
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        color: #2c3e50;
        margin-top: 30px;
    }
</style>
