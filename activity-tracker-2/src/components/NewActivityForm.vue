<template>
  <!-- Add records section -->
  <div id="add-hours" class="card">
    <h2 class="card-header section-header">Add Records</h2>

    <!-- START of the actual form section -->
    <div class="card-body">
      <!-- Display errors section -->
      <!-- use v-show to show this if there are validation errors -->
      <div class="alert alert-danger" v-show="errors.length > 0">
        <!-- show a list of validation errors from the form -->
        <li v-for="error in errors">{{ error }}</li>
      </div>

      <!-- 1. Date input -->
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

      <!-- 2. Hours input -->
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

      <!-- 3. Drop down list for the type of activity -->
      <div class="form-group">
        <label class="form-label" for="activityType">What type?</label>

        <!-- Create select element, use v-model to connect to the types -->
        <select class="form-control" id="activityType" v-model="type">
          <!-- Use v-for to create option elements, one for each type -->
          <option v-for="type in types">{{ type }}</option>
        </select>
      </div>

      <br />

      <!-- 4. Medium of instruction (radio buttons) -->
      <!-- Label "What media" for radio buttons: Traditional or Digital -->
      <div class="form-label pb-2">What media?</div>

      <!-- Option 1: "Traditional" radio button -->
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

      <!-- Option 2: "Digital" radio button -->
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

      <!-- 5. Add a "Completed?" checkbox -->
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

      <!-- 6. Add a textarea for "Notes" -->
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

      <!-- Submit Button for form: Add a "Add record" button -->
      <div>
        <button class="btn btn-dark mt-2" type="button" v-on:click="submit">
          Add record
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    activity: String,
    types: Array,
    media: Object,
  },
  data() {
    return {
      // set initial values
      // these will be used with v-model to work with form data
      dateString: '', // date input from the client-side is always a string
      hours: 1,
      type: 'Sketching',
      medium: '',
      completed: false,
      note: '',

      // store errors in an array discovered during validation
      errors: [],
    };
  },
  emits: ['record-added'],
  methods: {
    // submit method for the "Add record" button in the form section
    submit() {
      // Assumption: at the beginning of the validation, there are no errors
      this.errors = [];

      // convert the dateString to a Date object
      let date = new Date(this.dateString);

      // Validation 1: dates need to be valid, and in the past or today
      if (
        !this.dateString ||
        this.dateString == 'Invalid Date' ||
        date > new Date()
      ) {
        this.errors.push('Select a valid date, today or in the past.');
      }

      // Validation 2: Hours should be between 0 and 24
      if (this.hours <= 0 || this.hours > 24) {
        this.errors.push(
          'Number of hours must be greater than 0 and less than 24.'
        );
      }

      // Validation 3: Activity type has to be selected from drop down list
      if (!this.type) {
        this.errors.push('Select an activity type.');
      }

      // Validation 4: Activity medium of instruction must be selected (for radio buttons)
      if (!this.medium) {
        this.errors.push('Select a media.');
      }

      // if there are no errors, add a record to the activityRecords in the parent App.vue
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

        // emit a message to parent App.vue to add the record to the activityRecords data
        this.$emit('record-added', record);

        // reset data values to initial values
        this.dateString = '';
        this.hours = 1;
        this.type = 'Sketching';
        this.medium = '';
        this.completed = false;
        this.note = '';
      }
    },
  }, // END of methods
};
</script>

<style scoped>
.section-header {
  background-image: linear-gradient(45deg, violet, yellow);
}
</style>
