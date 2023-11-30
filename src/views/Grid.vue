<script setup>
import { ref, computed } from 'vue'

// (1) props 선언
const props = defineProps({
  data: Array,
  columns: Array,
  filterKey: String
})

// (2) ref(state) 선언
const sortRef = ref('') 
const sortOrders = ref(
  props.columns.reduce((o, key) => {
    console.log('o:::'+o, 'key:::'+key, 'value:::'+o[key])
    return ((o[key] = 1), o)
}, {})
)

// (3) data props -> computed: filteredData
const filteredData = computed(() => {
  let { data, filterKey } = props
  if (filterKey) {
    filterKey = filterKey.toLowerCase()
    data = data.filter((row) => {
      return Object.keys(row).some((key) => {
        return String(row[key]).toLowerCase().indexOf(filterKey) > -1
      })
    })
  }
  const key = sortRef.value
  if (key) {
    const order = sortOrders.value[key]
    data = data.slice().sort((a, b) => {
      a = a[key]
      b = b[key]
      return (a === b ? 0 : a > b ? 1 : -1) * order
    })
  }
  return data
})

function sortBy(sortTitle) {
  sortRef.value = sortTitle
  sortOrders.value[sortTitle] *= -1
}

</script>

<template>
  <table v-if="filteredData.length">
    <thead>
      <tr>
        <th v-for="sortTitle in columns"
          @click="sortBy(sortTitle)" 
          :class="{ active: sortRef == sortTitle }">
          {{ sortTitle }}
          <span class="arrow" :class="sortOrders[sortTitle] > 0 ? 'asc' : 'dsc'">
          </span>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="entry in filteredData">
        <td v-for="key in columns">
          {{entry[key]}}
        </td>
      </tr>
    </tbody>
  </table>
  <p v-else>No matches found.</p>
</template>

<style>
table {
  border: 2px solid #42b983;
  border-radius: 3px;
  background-color: #fff;
}

th {
  background-color: #42b983;
  color: rgba(255, 255, 255, 0.66);
  cursor: pointer;
  user-select: none;
}

td {
  background-color: #f9f9f9;
}

th,
td {
  min-width: 120px;
  padding: 10px 20px;
}

th.active {
  color: #fff;
}

th.active .arrow {
  opacity: 1;
}

.arrow {
  display: inline-block;
  vertical-align: middle;
  width: 0;
  height: 0;
  margin-left: 5px;
  opacity: 0.66;
}

.arrow.asc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-bottom: 4px solid #fff;
}

.arrow.dsc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-top: 4px solid #fff;
}
</style>