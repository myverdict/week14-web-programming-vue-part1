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

  <!-- Summary section -->
  <div class="card">
    <h2 class="card-header">Summary</h2>

    <div class="card-body">
      <!-- add computed property to calculate the total hours -->
      <p>
        You have practiced a total of
        <!-- display total hours -->
        {{ totalHours.toFixed(2) }} hours.
      </p>

      <!-- Challenge question! Display a list of the total hours for each type.  -->
      <p v-if="activityRecords.length != 0">
        Practice hours for each activity:

        <li v-for="eachActivityHours in totalHoursForEachActivityRecord">
          {{ eachActivityHours.typeOfActivity }}:
          {{ eachActivityHours.numOfHours.toFixed(2) }}

          <!-- <span v-if="eachActivityHours.hours > 0">{{ eachActivityHours }}</span> -->
        </li>
      </p>
    </div>
  </div>
</template>

<script>
import ActivityTable from './components/ActivityTable.vue';
import NewActivityForm from './components/NewActivityForm.vue';

export default {
  components: {
    NewActivityForm,
    ActivityTable,
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
  computed: {
    totalRecords() {
      if (this.activityRecords.length == 1) {
        return `1 record`;
      } else {
        return this.activityRecords.length + ' records';
      }
    },
    totalHours() {
      let total = 0;
      this.activityRecords.forEach((record) => {
        total = total + record.hours;
      });
      return total;
    },
    totalHoursForEachActivityRecord() {
      let arrayOfObjects = []; // empty array for objects
      let objectInArray = {};

      this.activityRecords.forEach((record) => {
        this.types.forEach((activityType) => {
          // if the record entered is equal to one of the activity types
          if (record.type === activityType) {
            // if there are no items/objects in the arrayOfObjects
            if (arrayOfObjects.length === 0) {
              // create a new object
              objectInArray = {
                typeOfActivity: record.type,
                numOfHours: record.hours,
              };

              // push the object on to the arrayOfObjects
              arrayOfObjects.push(objectInArray);
            }
            // else if there are items/objects in the arrayOfObjects,
            // i.e., arrayOfObjects.length != 0
            else {
              let found = false; // initial boolean to record not found

              // loop through the array to find if the item/object already exists
              for (let i = 0; i < arrayOfObjects.length; i++) {
                // if the record exists in the arrayOfObjects
                if (record.type === arrayOfObjects[i].typeOfActivity) {
                  // then just update the number of hours for that item/object
                  arrayOfObjects[i].numOfHours += record.hours;
                  found = true; // record is found
                }
              }

              // if the record is not found in the arrayOfObjects
              if (!found) {
                // create a new object
                objectInArray = {
                  typeOfActivity: record.type,
                  numOfHours: record.hours,
                };

                // push the object on to the arrayOfObjects
                arrayOfObjects.push(objectInArray);
              }
            }
          }
        }); // end of types array forEach
      }); // end of activityRecords array forEach

      return arrayOfObjects;
    },
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

<style scoped>
#records {
  max-height: 250px;
  overflow: scroll;
}
.sketchingRow {
  background-color: yellow;
}
.drawingRow {
  background-color: greenyellow;
}
.paintingRow {
  background-color: orange;
}
</style>
