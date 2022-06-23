<script>
import fs from 'fs'
import pathModule from 'path'
import { app } from '@electron/remote'
import { computed, ref } from 'vue'

import FilesViewer from './components/FilesViewer'



const formatSize = size => {
  var i = Math.floor(Math.log(size) / Math.log(1024))
  return ((size / Math.pow(1024, i)).toFixed(2) * 1 + ' ' + ['Bite', 'KB', 'MB', 'GB', 'TB'][i]
  )
}

export default {
  name: 'App',
  components: { FilesViewer },
  setup() {
    const path = ref(app.getAppPath())
    const files = computed(() => {
      const fileNames = fs.readdirSync(path.value)
      return fileNames
          .map(file => {
            const stats = fs.statSync(pathModule.join(path.value, file))
            return {
              name: file,
              size: stats.isFile() ? formatSize(stats.size ?? 0) : null,
              directory: stats.isDirectory()
            }
          })
          .sort((a, b) => {
            if (a.directory === b.directory) {
              return a.name.localeCompare(b.name)
            }
            return a.directory ? -1 : 1
          })
    })

    const back = () => {
      path.value = pathModule.dirname(path.value)
    }
    const open = folder => {
      path.value = pathModule.join(path.value, folder)
    }

    const searchString = ref('')
    const filteredFiles = computed(() => {
      return searchString.value ? files.value.filter(s => s.name.startsWith(searchString.value)) : files.value
    })

    return {path, open, back, files, searchString, filteredFiles}
  }
}

</script>

<template>

  <div class="container mt-2 ">
    <h4 class="header">{{ path }}</h4>

    <div class="form-group mt-4 mb-2">
      <input
          v-model="searchString"
          class="form-control form-control-sm "
          placeholder="Search"
      />
    </div>

    <FilesViewer
        :files="filteredFiles"
        :path="path"
        @back="back"
        @folderclick="open($event.name)"
    />
  </div>
</template>

<style>

body {
  color: white !important;
  background-color: black !important;
}

.header{
  font-size: 30px;
}

svg {
  fill: white !important;
}

</style>
