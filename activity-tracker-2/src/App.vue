<template>
  <h1 class="text-center">{{ activity }} Time Tracker</h1>

  <NewActivityForm
    v-on:record-added="newRecordAdded"
    v-bind:activity="activity"
    v-bind:types="types"
    v-bind:media="media"
  />

  <ActivityTable
    v-bind:activityRecords="activityRecords"
    v-bind:types="types"
    v-bind:media="media"
    v-on:delete-record-table="deleteRecord"
    v-on:update-record-table="updateRecord"
  />

  <ActivitySummary
    v-bind:activityRecords="activityRecords"
    v-bind:types="types"
    v-bind:media="media"
  />

  <!-- Attributes -->
  <footer>
    <div>
      Pencil Icons made by
      <a
        href="https://www.flaticon.com/authors/dinosoftlabs"
        title="DinosoftLabs"
        target="_blank"
      >
        DinosoftLabs
      </a>
      from
      <a href="https://www.flaticon.com/" title="Flaticon"> www.flaticon.com</a>
    </div>

    <div>
      Remove Icons made by
      <a
        href="https://www.flaticon.com/authors/pixel-perfect"
        title="Pixel perfect"
        target="_blank"
      >
        Pixel perfect
      </a>
      from
      <a href="https://www.flaticon.com/" title="Flaticon"> www.flaticon.com</a>
    </div>

    <div>
      Green check mark icon from
      <a
        href="https://www.iconsdb.com/green-icons/check-mark-3-icon.html"
        target="_blank"
      >
        Icons DB
      </a>
    </div>
  </footer>
</template>

<script>
import ActivitySummary from './components/ActivitySummary.vue';
import ActivityTable from './components/ActivityTable.vue';
import NewActivityForm from './components/NewActivityForm.vue';

export default {
  components: {
    NewActivityForm,
    ActivityTable,
    ActivitySummary,
  },
  data() {
    return {
      // name of the activity
      activity: 'Practice Art',

      // array of activity records
      activityRecords: [],

      // used to create choices - the option elements for select element
      types: ['Sketching', 'Drawing', 'Painting'],

      // used to set the values, and the labels for the radio buttons
      media: {
        traditional: 'Traditional',
        digital: 'Digital',
      },
    };
  },
  methods: {
    newRecordAdded(record) {
      // Push the record on to the activityRecords array
      this.activityRecords.push(record);

      // set the mostRecentRecord to current record
      // this.mostRecentRecord = record;            // ASK PROF: Do we need this for ActivitySummary.vue page

      // sort the records according to date
      this.activityRecords.sort(function (r1, r2) {
        // returns negative value to order r1 before r2
        // returns positive value to order r1 after r2
        // earliest records at the beginning of the list
        // return r1.date.getTime() - r2.date.getTime();
        // recent records at the beginning of the list
        return r2.date.getTime() - r1.date.getTime();
      });
    },
    deleteRecord(record) {
      // filter returns a new array of all records for which the function returns true
      this.activityRecords = this.activityRecords.filter(function (rec) {
        if (rec != record) {
          return true;
        }
      });
    },
    updateRecord() {
      // find the record in this.activityRecords, set completed value
      let updatedRecord = this.activityRecords.find(function (rec) {
        console.log('Edit record: ', rec);
      });
    },
  },
};
</script>

<style>
footer {
  font-size: small;
  background-color: lightgrey;
  font-weight: bold;
  margin-top: 25px;
  padding: 10px;
}
</style>
