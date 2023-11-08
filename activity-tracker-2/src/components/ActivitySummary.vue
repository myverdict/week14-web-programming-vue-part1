<template>
  <div class="card">
    <h2 class="card-header section-header">Summary</h2>

    <div class="card-body">
      <div>
        <p>
          You have practiced a total of
          <!-- display total hours with computed property -->
          <span v-if="totalHours === 1">
            {{ totalHours.toFixed(2) }} hour.
          </span>

          <span v-else> {{ totalHours.toFixed(2) }} hours. </span>
        </p>

        <!-- display a list of total hours for each activity type. -->
        <p v-if="activityRecords.length != 0">
          Practice hours for each activity:

          <li v-for="eachActivityHours in totalHoursForEachActivityRecord">
            {{ eachActivityHours.typeOfActivity }}:
            {{ eachActivityHours.numOfHours.toFixed(2) }}
          </li>
        </p>
      </div>

      <!-- Charts section -->
      <div class="charts" v-if="activityRecords.length != 0">
        <BarChart :chartData="chartDataForHours" />
        <br />
        <hr />
        <DoughnutChart :chartData="chartDataForMedia" />
        <br />
        <hr />
      </div>
    </div>
  </div>
</template>

<script>
import BarChart from './charts/BarChart.vue';
import DoughnutChart from './charts/DoughnutChart.vue';

export default {
  components: {
    BarChart,
    DoughnutChart,
  },
  props: {
    activityRecords: Array,
    types: Array,
    media: Object,
  },
  computed: {
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
    // Data for the bar chart - each activity and total hours for each activity
    chartDataForHours() {
      let colors = ['orchid', 'teal', 'yellowgreen'];
      let activityArray = this.totalHoursForEachActivityRecord;

      // create 2 empty arrays for the activity type and activity hours
      let activityTypeNames = [];
      let activityNumHours = [];

      // add each activity type name and each activity total hours to its respective arrays
      activityArray.forEach(function (eachActivity) {
        activityTypeNames.push(eachActivity.typeOfActivity);
        activityNumHours.push(eachActivity.numOfHours);
      });

      // return data in format expected by chartJS
      return {
        labels: activityTypeNames, // this is an array value
        datasets: [
          {
            label: 'Hours practiced', // the label that will show in the tooltip on hover
            // barThickness: 20,
            data: activityNumHours, // array value
            backgroundColor: colors, // array value
          },
        ],
      };
    }, // END OF BAR CHART COMPUTED PROPERTY
    // Data for the doughnut chart - medium of instruction
    chartDataForMedia() {
      let colors = ['#DAF7A6', '#FFC300'];

      // [ traditionalCount, digitalCount ]
      let activityMediaCount = [];
      let traditionalCount = 0;
      let digitalCount = 0;

      // add number of times media type appears to each medium
      this.activityRecords.forEach((eachActivity) => {
        if (eachActivity.medium === this.media.traditional) {
          traditionalCount += 1;
        } else {
          digitalCount += 1;
        }
      });

      // Push the counts onto the activityMediaCount array
      activityMediaCount.push(traditionalCount);
      activityMediaCount.push(digitalCount);

      // return data in format expected by chartJS
      return {
        labels: [this.media.traditional, this.media.digital], // an array value
        datasets: [
          {
            data: activityMediaCount, // an array value
            backgroundColor: colors, // an array value
          },
        ],
      };
    }, // END OF DOUGHNUT CHART COMPUTED PROPERTY
  },
};
</script>

<style scoped>
.section-header {
  background-image: linear-gradient(45deg, violet, yellow);
}
.charts {
  border: 2px solid violet;
  padding: 0.5rem;
  width: 50%;
  margin: auto;
}
</style>
