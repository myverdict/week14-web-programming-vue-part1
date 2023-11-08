<template>
  <!-- v-bind types & media is binding a prop from EditModal.vue to a prop in ActivityTable.vue -->
  <EditModal
    ref="editRecordModal"
    v-bind:initialRecordInfo="recordToEdit"
    v-bind:types="types"
    v-bind:media="media"
    v-on:save-edited-one-record-from-modal="recordEditSaved"
  />

  <!-- List of Activity Records TABLE section -->
  <div class="card">
    <h2 class="card-header text-white bg-dark">Activity Records</h2>

    <!-- checkbox for 'Edit table' option -->
    <div class="edit-table-toggle form-check editing-checkbox">
      <input
        id="edit-table"
        type="checkbox"
        class="form-check-input"
        v-model="editTable"
      />
      <label for="edit-table" class="form-check-label">Edit table?</label>
    </div>

    <div class="card-body">
      <h3>
        <!-- display records with computed property -->
        {{ totalRecords }}
        <!-- display records without computed property -->
        <!-- {{ activityRecords.length }} -->
      </h3>

      <div id="records">
        <table class="table table-sm table-bordered table-hover table-light">
          <thead>
            <tr class="bg-danger">
              <th>Date</th>
              <th>Hours</th>
              <th>Type</th>
              <th>Media</th>
              <th>Status</th>
              <th>Notes</th>
              <th v-show="editTable">Actions</th>
            </tr>
          </thead>

          <tbody>
            <!-- use v-for to create one table row for each activity record -->
            <ActivityRow
              v-for="record in activityRecords"
              v-bind:key="record.id"
              v-bind:record="record"
              v-bind:edit="editTable"
              v-on:delete-record-row="deleteRecord"
              v-on:request-edit-record="requestEditRecord"
            />
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import ActivityRow from './ActivityRow.vue';
import EditModal from './EditModal.vue';

export default {
  components: { ActivityRow, EditModal },
  // do not modify a prop: props data has to be provided by its parent, App.vue
  // to avoid modification of props v-model in the template should not be attached to props
  props: {
    activityRecords: Array, // array of record objects received from App.vue
    types: Array, // array of activity types received from App.vue
    media: Object, // object of medium of instruction received form App.vue
  },
  data() {
    return {
      editTable: false, // initial setting of the 'Edit table?' checkbox
      recordToEdit: {}, // initial empty variable for record to be edited
    };
  },
  computed: {
    // set the plurality of the word 'record(s)' in the table title section of the application
    totalRecords() {
      if (this.activityRecords.length == 1) {
        return `1 record`;
      } else {
        return this.activityRecords.length + ' records';
      }
    },
  },
  emits: ['delete-record-table', 'save-edited-one-record-from-table'],
  methods: {
    // when the delete button is clicked in a table row
    deleteRecord(record) {
      // emits a message to the parent App.vue
      this.$emit('delete-record-table', record);
    },

    // when the edit button is clicked in a table row, the form will show
    // the fields with the specific table row data
    requestEditRecord(record) {
      // set data that the modal will display
      // avoid v-model directly to the data that is being changed, or you'll have to think how
      // to get the original data back if the user changes the data but then changes their mind
      // and wants to cancel the edit.
      this.recordToEdit = record;
      this.$refs.editRecordModal.show();
    },

    // save the updated record to the table to the same record id
    recordEditSaved(record) {
      // emits a message to the parent App.vue
      this.$emit('save-edited-one-record-from-table', record);
    },
  },
};
</script>

<style scoped>
#records {
  max-height: 250px;
  overflow: scroll;
}
.editing-checkbox {
  margin: 20px;
}
th {
  text-align: center;
  color: white;
}
</style>
