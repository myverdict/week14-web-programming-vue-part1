<template>
  <h1 class="text-center">{{ activity }} Time Tracker</h1>

  <!-- use v-bind with child props as attributes to send data from App.vue to child -->
  <!-- use v-on to listen to events from child components -->
  <NewActivityForm
    v-on:record-added="newRecordAdded"
    v-bind:activity="activity"
    v-bind:types="types"
    v-bind:media="media"
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
      <a href="https://www.flaticon.com/" title="Flaticon">
        www.flaticon.com
      </a>
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
import ActivityTable from './components/ActivityTable.vue';
import NewActivityForm from './components/NewActivityForm.vue';

export default {
  components: {
    NewActivityForm,
    ActivityTable,
  },
  data() {
    return {
      // Name of the activity, e.g., sport, exercise, language, etc.
      // used in App.vue and in the NewActivityForm.vue as props
      activity: 'Practice Art',

      // Array of activity records displayed in the activity table summary
      // used in ActivityTable.vue and ActivitySummary.vue
      activityRecords: [],

      // used to create choices (drop-down list): the option elements for select for question 3
      // used in the NewActivityForm.vue, ActivityTable.vue, ActivitySummary.vue, and EditModal.vue as props
      types: ['Sketching', 'Drawing', 'Painting'],

      // used to set the values and the labels for the radio buttons for question 4
      // used in NewActivityForm.vue, ActivityTable.vue, ActivitySummary.vue, and EditModal.vue as props
      media: {
        traditional: 'Traditional',
        digital: 'Digital',
      },
    };
  },
  mounted() {
    this.updateAllRecords();
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
    // this method adds an activity record to the database and updates the api
    newRecordAdded(record) {
      // the $activity_record_api variable is taken from main.js &
      // the addActivityRecord method is taken from the ActivityService.vue
      this.$activity_record_api
        .addActivityRecord(record)
        .then((record) => {
          this.updateAllRecords();
        })
        .catch((err) => {
          let msg = err.response.data.join(', ');
          alert('Error adding activity record.\n' + msg);
        });
    }, // END of newRecordAdded method

    // this method deletes one record from the database & updates the api
    deleteRecord(record) {
      // the $activity_record_api variable is from main.js, &
      // the deleteActivityRecord method is from the ActivityService.vue
      this.$activity_record_api.deleteActivityRecord(record.id).then(() => {
        this.updateAllRecords();
      });
    },

    // item coming from b-modal --> table --> App.vue to replace array with 1 updated table record row
    updateOneItem(record) {
      // the $activity_record_api variable is from main.js &
      // the updateActivityRecord method is from the ActivityService.vue
      this.$activity_record_api.updateActivityRecord(record).then(() => {
        this.updateAllRecords();
      });
    },

    // this method updates all activity records
    updateAllRecords() {
      // the $activity_record_api variable is from main.js &
      // the getAllActivityRecords method is from the ActivityService.vue
      this.$activity_record_api.getAllActivityRecords().then((records) => {
        this.activityRecords = records;
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
footer {
  font-size: small;
  background-color: lightgrey;
  font-weight: bold;
  margin-top: 25px;
  padding: 10px;
}
</style>
