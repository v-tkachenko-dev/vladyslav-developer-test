<template>
    <div class="container">
        <div class="row">
            <div class="col">
                <div class="card">
                    <div class="card-header">Table to CSV Generator</div>

                    <div class="card-body">
                        <table class="table table-bordered">
                            <thead>
                            <tr>
                                <th v-for="column in columns">
                                    <input type="text"
                                           class="form-control"
                                           :value="column.key"
                                           @input="updateColumnKey(column, $event)"
                                    />
                                </th>
                                <th>
                                    <!-- table actions  -->
                                </th>
                            </tr>
                            </thead>
                            <tbody>
                            <tr v-for="(row, rowIndex) in data" :key="rowIndex">
                                <td v-for="(column) in columns">
                                    <input type="text" class="form-control" v-model="row[column.key]"/>
                                </td>
                                <td class="d-flex justify-content-center">
                                    <button
                                        type="button"
                                        class="btn btn-danger"
                                        @click="removeRow(rowIndex)"
                                    >Remove row</button>
                                </td>
                            </tr>
                            </tbody>
                        </table>

                        <button
                            type="button"
                            class="btn btn-secondary"
                            @click="addColumn"
                        >Add Column</button>

                        <button
                            type="button"
                            class="btn btn-secondary"
                            @click="addRow"
                        >Add Row</button>
                    </div>

                    <div class="card-footer text-right">
                        <button class="btn btn-sm btn-primary" type="button" @click="submit()">Export</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
    export default {
        name: "CSVGenerator",

        data() {
            return {
                data: [
                    {
                        first_name: 'John',
                        last_name: 'Doe',
                        emailAddress: 'john.doe@example.com'
                    },
                    {
                        first_name: 'John',
                        last_name: 'Doe',
                        emailAddress: 'john.doe@example.com'
                    },

                ],
                columns: [
                    {key: 'first_name'},
                    {key: 'last_name'},
                    {key: 'emailAddress'},
                ]
            }
        },

        methods: {
            addRow() {
                console.log('add row data', this.data);
                const row = {};

                this.columnKeys.map(columnKey => {
                    row[columnKey] = '';
                });

                this.data.push(row);
            },

            removeRow(rowIndex) {
                this.data.splice(rowIndex, 1)
            },

            addColumn() {
                const columnKey = Math.random().toString(36).substring(7);
                this.columns.push({
                    key: columnKey
                });

                this.data.forEach(data => {
                    data[columnKey] = '';
                });
                console.log("this.data", this.data);
                console.log("this.columns", this.columns);
            },

            updateColumnKey(column, event) {
                const oldKey = column.key;
                const columnIndex = this.columns.findIndex(column => column.key === oldKey);

                column.key = event.target.value;

                this.data.forEach(row => {
                    if (row[oldKey] !== undefined) {
                        row[column.key] = row[oldKey];
                        delete row[oldKey];
                    }
                })

                this.columns[columnIndex].key = column.key;
            },

            submit() {
                return axios.patch('/api/csv-export', this.data);
            }
        },

        computed: {
            columnKeys() {
                return this.columns.map(column => column.key);
            }
        }
    }
</script>

<style scoped>

</style>
