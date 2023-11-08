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
      <img
        src="../assets/pencil.png"
        title="Update/Edit"
        v-on:click="updateRecord"
      />
      <img
        src="../assets/delete.png"
        title="Delete"
        v-on:click="deleteRecord"
      />
    </td>
  </tr>
</template>

<script>
export default {
  // props data has to be provided by its parent, ActivityTable.vue
  // do not change the props
  props: {
    record: Object,
    edit: Boolean,
  },
  emits: ['delete-record-row', 'update-record-row'],
  methods: {
    formatDate(date) {
      return Intl.DateTimeFormat('en-US', { timeZone: 'UTC' }).format(date);
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
    deleteRecord(record) {
      if (
        confirm(
          `Delete ${this.record.type} activity, with ${
            this.record.hours
          } hours, dated ${this.record.date.toLocaleDateString()}?`
        )
      ) {
        // emits a message to the parent ActivityTable.vue
        this.$emit('delete-record-row', this.record);
      }
    },
    updateRecord(record) {
      // emits a message to the parent ActivityTable.vue
      this.$emit('update-record-row', record);
    },
  },
};
</script>

<style scoped>
tr.sketchingRow {
  background-color: AliceBlue;
}
tr.drawingRow {
  background-color: LemonChiffon;
}
tr.paintingRow {
  background-color: MistyRose;
}
img {
  height: 25px;
}
img.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
}
</style>
