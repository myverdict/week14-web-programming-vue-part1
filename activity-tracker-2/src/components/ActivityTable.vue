<template>
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
        <!-- sisplay records with computed property -->
        {{ totalRecords }}
        <!-- display records without computed property -->
        <!-- {{ activityRecords.length }} -->
      </h3>

      <div id="records table-responsive">
        <table class="table table-bordered table-hover table-light">
          <thead>
            <tr>
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
              v-on:update-record-row="updateRecord"
            />
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import ActivityRow from './ActivityRow.vue';

export default {
  components: { ActivityRow },
  props: {
    // received from App.vue
    activityRecords: Array,
    types: Array,
    media: Object,
  },
  data() {
    return {
      editTable: false,
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
  emits: ['delete-record-table', 'update-record-table'],
  methods: {
    deleteRecord(record) {
      // emits a message to the parent App.vue
      this.$emit('delete-record-table', record);
    },
    updateRecord(record) {
      // emits a message to the parent App.vue
      this.$emit('update-record-table', record);
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
  background-color: gray;
  color: white;
}
</style>
