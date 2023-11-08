<template>
  <h1 class="text-center">{{ activity }} Time Tracker</h1>

  <NewActivityForm
    v-on:record-added="newRecordAdded"
    v-bind:activity="activity"
    v-bind:types="types"
    v-bind:media="media"
  />

  <!-- Activity Records list section -->
  <div class="card">
    <h2 class="card-header">Activity Records</h2>

    <div class="card-body">
      <h3>
        <!-- Display number of records: with computed property -->
        {{ totalRecords }}
        <!-- {{ activityRecords.length }} records without computed property -->
      </h3>

      <div id="records">
        <table class="table table-hover">
          <thead>
            <tr>
              <th>Date</th>
              <th>How many hours?</th>
              <th>Type</th>
              <th>Media</th>
              <th>Completed</th>
              <th>Notes</th>
            </tr>
          </thead>

          <!-- use v-for to create one table row for each activity record -->
          <tbody>
            <tr
              v-for="record in activityRecords"
              v-bind:class="{
                sketchingRow: record.type === 'Sketching',
                drawingRow: record.type === 'Drawing',
                paintingRow: record.type === 'Painting',
              }"
            >
              <td>{{ formatDate(record.date) }}</td>
              <td>{{ record.hours.toFixed(2) }}</td>
              <td>{{ record.type }}</td>
              <td>{{ record.medium }}</td>
              <td>{{ checkedBox(record.completed) }}</td>
              <td>{{ textareaDisplayCharacterLimit(record.note) }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
  <!-- end of Activity Records list section -->

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
import NewActivityForm from './components/NewActivityForm.vue';

export default {
  components: {
    NewActivityForm,
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
    checkedBox(completed) {
      if (completed) {
        return `Completed`;
      }
      return `Not completed`;
    },
    textareaDisplayCharacterLimit(text) {
      if (text.length >= 40) {
        return text.substr(0, 40) + '...';
      }
      return text;
    },
    formatDate(date) {
      return Intl.DateTimeFormat('en-US', { timeZone: 'UTC' }).format(date);
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
