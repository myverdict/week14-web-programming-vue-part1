<template>
  <h1 class="text-center">{{ activity }} Time Tracker</h1>

  <!-- Add records section -->
  <div id="add-hours" class="card">
    <h2 class="card-header">Add Records</h2>

    <div class="card-body">
      <!-- Display errors section -->
      <!-- use v-show to show this if there are validation errors -->
      <div class="alert alert-danger" v-show="errors.length > 0">
        <!-- show a list of validation errors from the form -->
        <li v-for="error in errors">{{ error }}</li>
      </div>

      <!-- Date input -->
      <div class="form-group">
        <!-- display name of activity in the label -->
        <label class="form-label" for="date">
          What date did you
          <!-- activity name, in lowercase -->
          {{ activity.toLowerCase() }}?
        </label>

        <!-- use v-model to connect this input to dateString data -->
        <input
          id="date"
          class="form-control"
          type="date"
          v-model="dateString"
        />

        <small id="date-help" class="form-text text-muted">
          Date must be today or in the past.
        </small>
      </div>

      <br />

      <!-- Hours input -->
      <div class="form-group">
        <label class="form-label" for="hours">
          How many hours did you practice for?
        </label>

        <!-- use v-model to connect this input to hours data -->
        <input
          id="hours"
          class="form-control"
          type="number"
          min="0"
          max="24"
          v-model.number="hours"
        />

        <small id="hours-help" class="form-text text-muted">
          Enter a number of hours, more than 0, up to a maximum of 24
        </small>
      </div>

      <br />

      <!-- Drop down list for the type of activity -->
      <div class="form-group">
        <label class="form-label" for="activityType">What type?</label>

        <!-- Create select element, use v-model to connect to the types -->
        <select class="form-control" id="activityType" v-model="type">
          <!-- Use v-for to create option elements, one for each type -->
          <option v-for="type in types">{{ type }}</option>
        </select>
      </div>

      <br />

      <!-- Label "What media" for radio buttons: Traditional or Digital -->
      <div class="form-label pb-2">What media?</div>

      <!-- "Traditional" radio buttons -->
      <div class="form-check-inline">
        <!-- v-model and v-bind media -->
        <input
          id="media1"
          class="form-check-input"
          type="radio"
          v-bind:value="media.traditional"
          v-model="medium"
        />

        <label class="form-check-label" for="media1">
          <!--Display text -->
          {{ media.traditional }}
        </label>
      </div>

      <!-- "Digital" radio buttons -->
      <div class="form-check-inline">
        <!-- v-model and v-bind media -->
        <input
          id="media2"
          class="form-check-input"
          type="radio"
          v-bind:value="media.digital"
          v-model="medium"
        />

        <label class="form-check-label" for="media2">
          <!-- Display text -->
          {{ media.digital }}
        </label>
      </div>

      <br /><br />

      <!-- Add a "Completed?" checkbox -->
      <div>Status</div>
      <div class="form-check pb-3 pt-3">
        <input
          class="form-check-input"
          id="completed-practice"
          type="checkbox"
          v-model="completed"
        />
        <label class="form-check-label" for="completed-practice">
          Completed?
        </label>
      </div>

      <br />

      <!-- Add a textarea for "Notes" -->
      <div class="form-group">
        <label for="textareaInput">Notes:</label>
        <textarea
          id="textareaInput"
          class="form-control"
          rows="3"
          v-model="note"
        ></textarea>
      </div>

      <br />

      <!-- Add a "Add record" button -->
      <div>
        <!-- Add v-on:click -->
        <button class="btn btn-primary mt-2" type="button" v-on:click="submit">
          Add record
        </button>
      </div>
    </div>
  </div>
  <!-- end of add records section -->

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
export default {
  data() {
    return {
      // name of the activity
      activity: 'Practice Art',

      // this will be used with v-model to work with form data
      dateString: '',
      hours: 1,
      type: 'Sketching',
      medium: '',
      completed: false,
      note: '',

      // array of activity records
      activityRecords: [],

      // used to create choices - the option elements for select element
      types: ['Sketching', 'Drawing', 'Painting'],

      // used to set the values, and the labels for the radio buttons
      media: {
        traditional: 'Traditional',
        digital: 'Digital',
      },

      // store errors discovered during validation
      errors: [],
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
    submit() {
      // convert the dateString to a Date object
      let date = new Date(this.dateString);

      // Clear the errors array
      this.errors = [];

      // Validation: dates need to be valid, and in the past or today
      if (
        !this.dateString ||
        this.dateString == 'Invalid Date' ||
        date > new Date()
      ) {
        this.errors.push('Select a valid date, today or in the past.');
      }

      // Validation: Hours should be between 0 and 24
      if (this.hours <= 0 || this.hours > 24) {
        this.errors.push(
          'The number of hours must be greater than 0 and less than 24.'
        );
      }

      // Validation: Activity type has to be selected from drop down list
      if (!this.type) {
        this.errors.push('Select a type.');
      }

      // Validation: Activity medium must be selected (for radio buttons)
      if (!this.medium) {
        this.errors.push('Select a media.');
      }

      // if there are no errors, add a record to the summary
      if (this.errors.length == 0) {
        // create a record
        let record = {
          date: date,
          hours: this.hours,
          type: this.type,
          medium: this.medium,
          completed: this.completed,
          note: this.note,
        };

        // Push the record on to the activityRecords array
        this.activityRecords.push(record);

        // sort the records according to date
        this.activityRecords.sort(function (r1, r2) {
          // returns negative value to order r1 before r2
          // returns positive value to order r1 after r2
          // if you want the earliest date at the top of the list
          return r1.date.getTime() - r2.date.getTime(); // this returns a timestamp

          // if you want the most recent date on top of the list
          // return r2.date.getTime() - r1.date.getTime() // this returns a timestamp
        });

        // reset some input fields after a successful submit request
        this.dateString = '';
        this.hours = 1;
        this.medium = '';
        this.completed = false;
        this.note = '';
      }
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
