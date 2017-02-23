/**
* Created by Nikola nb on 12.02.2017.
*/

<script>
    export default {
        methods: {
            loadPage (page) {
                if (this.pagination && page === 'next' && !this.isOnLastPage) {
                    this.$emit('change-page', ++this.currentPage)
                } else if (this.pagination && page === 'prev' && !this.isOnFirstPage) {
                    this.$emit('change-page', --this.currentPage)
                } else if (page !== 'next' && page !== 'prev' && page !== this.currentPage) {
                    this.$emit('change-page', page)
                }
            },
            isCurrentPage (page) {
                return page === this.currentPage
            }
        },
        computed: {
            currentPage () {
                return parseInt(this.pagination.currentPage)
            },
            isOnFirstPage () {
                return this.currentPage === 1
            },
            notEnoughPages () {
                return this.pagination.pageCount < (this.onEachSide * 2) + 4
            },
            totalPage () {
                return this.pagination === null ? 0 : parseInt(this.pagination.pageCount)
            },
            isOnLastPage () {
                return this.pagination === null
                    ? false
                    : this.pagination.currentPage === this.pagination.pageCount
            }
        },
        props: {
            onEachSide: {
                type: Number,
                default () {
                    return 2
                }
            },
            css: {
                type: Object,
                default () {
                    return {
                        wrapperClass: 'pagination pull-right',
                        activeClass: 'active',
                        disabledClass: 'disabled',
                        pageClass: '',
                        linkClass: ''
                    }
                }
            },
            icons: {
                type: Object,
                default () {
                    return {
                        first: '',
                        prev: '',
                        next: '',
                        last: ''
                    }
                }
            },
            pagination: {
                type: Object,
                default () {
                    return {
                        currentPage: 1,
                        pageCount: 1,
                        perPage: 20,
                        totalCount: 0
                    }
                }
            }
        }
    }
</script>