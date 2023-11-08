<template>
  <div class="card">
    <h2 class="card-header section-header">Summary</h2>

    <div class="card-body">
      <div>
        <!-- add computed property to calculate the total hours -->
        <p>
          You have practiced a total of
          <!-- display total hours -->
          <span v-if="totalHours === 1">
            {{ totalHours.toFixed(2) }} hour.
          </span>

          <span v-else> {{ totalHours.toFixed(2) }} hours. </span>
        </p>

        <!-- display a list of total hours for each activity type.  -->
        <p v-if="activityRecords.length != 0">
          Practice hours for each activity:

          <li v-for="eachActivityHours in totalHoursForEachActivityRecord">
            {{ eachActivityHours.typeOfActivity }}:
            {{ eachActivityHours.numOfHours.toFixed(2) }}
          </li>
        </p>
      </div>

      <div class="charts"></div>
    </div>
  </div>
</template>

<script>
export default {
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
  },
};
</script>

<style scoped>
.section-header {
  background-image: linear-gradient(45deg, violet, yellow);
}
</style>
