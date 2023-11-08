<!-- this is a child component of its parent ActivityTable.vue -->
<template>
  <tr
    v-bind:key="record.id"
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

    <!-- check.png image used for completed, if !completed table cell will be empty -->
    <td>
      <img
        v-if="record.completed"
        src="../assets/check.png"
        class="center"
        alt="completed"
        title="completed"
      />
    </td>

    <td>{{ textareaDisplayCharacterLimit(record.note) }}</td>

    <td v-show="edit">
      <!-- edit button for record row -->
      <b-button class="btn btn-light left" size="sm" v-on:click="requestEdit">
        <img src="../assets/pencil.png" title="Update/Edit" class="actions" />
      </b-button>

      <!-- delete button for record row -->
      <b-button class="btn btn-light right" size="sm" v-on:click="deleteRecord">
        <img src="../assets/delete.png" title="Delete" class="actions" />
      </b-button>
    </td>
  </tr>
</template>

<script>
export default {
  props: {
    record: Object,
    edit: Boolean,
  },
  emits: ['delete-record-row', 'request-edit-record'],
  methods: {
    // when the delete button is clicked in a table row, actions column
    deleteRecord() {
      // displays an alert confirm box
      if (
        confirm(
          `Delete ${this.record.type} activity, with ${
            this.record.hours
          } hours, dated ${this.$options.filters.shortDate(this.record.date)}?`
        )
      ) {
        // emits a message to the parent, ActivityTable.vue, with prop data
        this.$emit('delete-record-row', this.record);
      }
    },
    // when the edit button is clicked in a table row, actions column
    requestEdit() {
      // emit a message to the parent, ActivityTable.vue, with prop data
      this.$emit('request-edit-record', this.record);
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
/* color settings for table rows */
tr.sketchingRow {
  background-color: AliceBlue;
}
tr.drawingRow {
  background-color: LemonChiffon;
}
tr.paintingRow {
  background-color: MistyRose;
}

/* img settings for check.png, delete.png, and pencil.png */
img {
  height: 25px;
}
/* img settings for check.png used in status column */
img.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
}
.left {
  float: left;
}
.right {
  float: right;
}

/* button hover settings for the delete.png & pencil.png (edit) buttons */
.btn:hover {
  background-color: black;
}
</style>
