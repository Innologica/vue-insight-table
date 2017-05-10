<template>
    <div id="app" class="container">
        <!--<img src="./assets/logo.png">-->
        <h1 class="page-header">Insight Table Component</h1>
        <insight-table :fields="fields" :data="data" :pagination="pagination" :sortOrder="sort">
            <template slot="link" scope="props">
                <a href="#">{{props.rowData.name }}</a>
            </template>
            <template slot="status" scope="props">
            <span class="label" :class="{'label-success': props.rowValue == 1, 'label-danger': props.rowValue == 0}">
                {{ props.rowValue == 1 ? 'ACTIVE' : 'INACTIVE'}}
            </span>
            </template>
        </insight-table>
        <insight-link-pager :pagination="pagination" @change-page="page"></insight-link-pager>
    </div>
</template>

<script>
    import InsightTable from './components/InsightTable'
    import InsightLinkPager from './pagers/InsightLinkPager'
    import users from './users'

    export default {
        name: 'app',
        components: {
            InsightTable, InsightLinkPager
        },
        data () {
            return {
                data: require('./users'),
                pagination: {
                    currentPage: 1,
                    pageCount: 2,
                    perPage: 5,
                    totalCount: 0
                },
                sort: [{
                    direction: 'asc', sortField: 'email', field: 'email'
                }],
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
        },
        methods: {
            page (page) {
                this.pagination.currentPage = page
            },
            formatDate (item, index, field) {
                if(!_.get(item, field.name))
                    return null
                let dt = new Date(_.get(item, field.name) * 1000)
                return dt.toLocaleString()
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
