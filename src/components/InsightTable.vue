/**
* Created by Nikola nb on 11.02.2017.
*/
<template>
    <table :class="['is-table', css.tableClass]">
        <thead>
        <tr>
            <template v-for="field in fields">
                <template v-if="field.visible">
                    <th @click="field.sortField ? orderBy(field, $event) : null"
                        :class="[field.titleClass, {'sortable': field.sortField }]">
                        {{ field.title }}
                        <i v-if="isInCurrentSortGroup(field) && field.title"
                           :class="sortIcon(field)"></i>
                    </th>
                </template>
            </template>
        </tr>
        </thead>
        <transition-group name="flip-list" tag="tbody">
        <template v-for="(item, index) in data">
            <tr :key="item">
                <template v-for="field in fields">
                    <template v-if="field.visible">
                        <td v-if="field.slot" :class="[field.dataClass]">
                            <slot :name="field.slot" :row-data="item" :row-index="index" :row-field="field"
                                  :row-value="getContent(item, index, field)">
                            </slot>
                        </td>
                        <td v-if="!field.slot" v-html="getContent(item, index, field)" :class="[field.dataClass]"></td>
                    </template>
                </template>
            </tr>
        </template>
        </transition-groupt>
    </table>
</template>

<script>
    import _ from 'lodash'

    export default {
        props: {
            fields: {
                type: Array,
                required: true
            },
            sortOrder: {
                type: Array,
                default () {
                    return []
                }
            },
            data: {
                type: Array,
                default () {
                    return []
                }
            },
            css: {
                type: Object,
                default () {
                    return {
                        tableClass: 'ui blue selectable celled stackable attached table',
                        loadingClass: 'loading',
                        ascendingIcon: 'glyphicon glyphicon-chevron-up',
                        descendingIcon: 'glyphicon glyphicon-chevron-down',
                        detailRowClass: 'is-table-detail-row',
                        sortHandleIcon: 'grey sidebar icon'
                    }
                }
            }
        },
        created () {
            this.normalizeFields()
            if (this.loadOnStart) {
                this.loadData()
            }
        },
        methods: {
            clearSortOrder () {
                this.sortOrder.push({
                    field: '',
                    sortField: '',
                    direction: 'asc'
                })
            },
            orderBy (field, $event) {
                if (this.sortOrder.length === 0) {
                    this.clearSortOrder()
                }

                this.sortOrder.splice(1) // removes additional columns

                if (this.fieldIsInSortOrderPosition(field, 0)) {
                    // change sort direction
                    this.sortOrder[0].direction = this.sortOrder[0].direction === 'asc' ? 'desc' : 'asc'
                } else {
                    // reset sort direction
                    this.sortOrder[0].direction = 'asc'
                }
                this.sortOrder[0].field = field.name
                this.sortOrder[0].sortField = field.sortField

                this.data.sort((a, b) => {
                    let s1 = String(_.get(a, field.name, ''))
                    s1 = _.isNull(s1) ? '' : s1.toLowerCase()
                    let s2 = String(_.get(b, field.name, ''))
                    s2 = _.isNull(s2) ? '' : s2.toLowerCase()
                    return this.sortOrder[0].direction === 'asc' ? s1.localeCompare(s2) : -s1.localeCompare(s2)
                })
            },
            isInCurrentSortGroup (field) {
                return this.currentSortOrderPosition(field) !== false
            },
            sortIcon (field) {
                let cls = {}
                let i = this.currentSortOrderPosition(field)

                if (i !== false) {
                    if (this.sortOrder[i].direction === 'asc') {
                        cls[this.css.ascendingIcon] = true
                    } else {
                        cls[this.css.descendingIcon] = true
                    }
                }

                return cls
            },
            currentSortOrderPosition (field) {
                if (!field.sortField) {
                    return false
                }

                for (let i = 0; i < this.sortOrder.length; i++) {
                    if (this.fieldIsInSortOrderPosition(field, i)) {
                        return i
                    }
                }

                return false
            },
            fieldIsInSortOrderPosition (field, i) {
                return this.sortOrder[i].field === field.name && this.sortOrder[i].sortField === field.sortField
            },
            getContent (item, index, field) {
                if (field.callback) {
                    return field.callback(item, index, field)
                }
                return _.get(item, field.name, '')
            },
            setTitle (str) {
                return this.titleCase(str)
            },
            titleCase (str) {
                return str.replace(/\w+/g, (txt) => {
                    return txt.charAt(0).toUpperCase() + txt.substr(1).toLowerCase()
                })
            },
            normalizeFields () {
                if (typeof (this.fields) === 'undefined') {
                    console.warn('You need to provide "fields" prop.')
                    return
                }

                this.fields.forEach((field, i) => {
                    var obj = {}
                    if (typeof (field) === 'string') {
                        obj = {
                            name: field,
                            title: this.setTitle(field),
                            sortField: field,
                            titleClass: '',
                            dataClass: '',
                            callback: null,
                            visible: true,
                            slot: ''
                        }
                    } else {
                        obj = {
                            name: field.name,
                            title: (field.title === undefined) ? this.setTitle(field.name) : field.title,
                            sortField: (field.sortField === undefined) ? field.name : field.sortField,
                            titleClass: (field.titleClass === undefined) ? '' : field.titleClass,
                            dataClass: (field.dataClass === undefined) ? '' : field.dataClass,
                            callback: (field.callback === undefined) ? '' : field.callback,
                            visible: (field.visible === undefined) ? true : field.visible,
                            slot: field.slot
                        }
                    }
                    this.$set(this.fields, i, obj)
                })
            }
        }
    }
</script>

<style>
    .is-table th.sortable:hover {
        color: #2185d0;
        cursor: pointer;
    }

    .is-table-actions {
        width: 15%;
        padding: 12px 0px;
        text-align: center;
    }

    .is-table-pagination {
        background: #f9fafb !important;
    }

    .is-table-pagination-info {
        margin-top: auto;
        margin-bottom: auto;
    }

    .flip-list-move {
        transition: transform 0.3s;
    }
</style>