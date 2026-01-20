<script setup>
import * as XLSX from 'xlsx'
import { ref } from 'vue'

const headers = ref([])
const rows = ref([])

/**
 * Hàm Flutter sẽ gọi
 */
function loadExcelFromFlutter(base64) {
  const binary = atob(base64)
  const bytes = new Uint8Array(binary.length)

  for (let i = 0; i < binary.length; i++) {
    bytes[i] = binary.charCodeAt(i)
  }

  const workbook = XLSX.read(bytes, { type: 'array' })
  const sheet = workbook.Sheets[workbook.SheetNames[0]]

  const data = XLSX.utils.sheet_to_json(sheet, {
    header: 1,
    defval: ''
  })

  headers.value = data[0]
  rows.value = data.slice(1)
}

// expose cho Flutter
window.loadExcelFromFlutter = loadExcelFromFlutter
</script>

<template><!-- NÚT DOWNLOAD APK -->
    <div style="margin-bottom: 16px">
      <a
        href="/office_viewer.apk"
        download
      >
        <button>
          ⬇️ Download App (.apk)
        </button>
      </a>
    </div>
  <div style="padding: 16px">
    <h2>Excel Viewer (Vue 3)</h2>
    <table v-if="rows.length" border="1" width="100%">
      <thead>
        <tr>
          <th v-for="(h, i) in headers" :key="i">{{ h }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, r) in rows" :key="r">
          <td v-for="(cell, c) in row" :key="c">
            {{ cell }}
          </td>
        </tr>
      </tbody>
    </table>

    <p v-else>Chưa có dữ liệu Excel</p>
  </div>
</template>
