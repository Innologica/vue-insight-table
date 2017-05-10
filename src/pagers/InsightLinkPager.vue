/**
* Created by Nikola nb on 12.02.2017.
*/
<template>
    <ul  v-if="pagination && pagination.pageCount > 1" :class="css.wrapperClass">
        <li>
            <a @click="loadPage(1)"
               :class="['btn-nav', css.linkClass, isOnFirstPage ? css.disabledClass : '']">
                <i v-if="icons.first != ''" :class="[icons.first]"></i>
                <span v-else>&laquo;</span>
            </a>
        </li>
        <li>
            <a @click="loadPage('prev')"
               :class="[css.linkClass, isOnFirstPage ? css.disabledClass : '']">
                <i v-if="icons.next != ''" :class="[icons.prev]"></i>
                <span v-else>&nbsp;&lsaquo;</span>
            </a>
        </li>
        <template v-if="notEnoughPages">
            <template v-for="n in totalPage">
                <li :class="[isCurrentPage(n) ? css.activeClass : '']">
                    <a @click="loadPage(n)"
                       v-html="n">
                    </a>
                </li>
            </template>
        </template>
        <template v-else>
            <template v-for="n in windowSize">
                <li :class="[isCurrentPage(windowStart+n-1) ? css.activeClass : '']">
                    <a @click="loadPage(windowStart+n-1)"
                       v-html="windowStart+n-1">
                    </a>
                </li>
            </template>
        </template>
        <li>
            <a @click="loadPage('next')"
               :class="[css.linkClass, isOnLastPage ? css.disabledClass : '']">
                <i v-if="icons.next != ''" :class="[icons.next]"></i>
                <span v-else>&rsaquo;&nbsp;</span>
            </a>
        </li>
        <li>
            <a @click="loadPage(totalPage)"
               :class="[css.linkClass, isOnLastPage ? css.disabledClass : '']">
                <i v-if="icons.last != ''" :class="[icons.last]"></i>
                <span v-else>&raquo;</span>
            </a>
        </li>
    </ul>
</template>

<script>
    import InsightPaginationMixin from '../mixins/InsightPaginationMixin'

    export default {
        mixins: [InsightPaginationMixin]
    }
</script>

<style>
    ul.pagination li {
        cursor: pointer;
    }
</style>