<script>
import IconFile from './IconFile'
import IconFolder from './IconFolder'
import IconFolderOpen from './IconFolderOpen'


export default {
  props: {
    files: { type: Array, default: () => [] },
    path: {type: String}
  },
  components: { IconFile, IconFolder, IconFolderOpen },
  setup(_, { emit }) {
    const onFileClick = file => {
      if (file.directory) emit('folderclick', file)
    }
    return { onFileClick,  }
  }
}
</script>

<template>
  <table class="table text-light">
    <tbody>
      <tr  @click="$emit('back')" :class="[path === '/Users' ? 'unclickable': 'clickable']">
        <td :class="[path === '/Users' ? 'unclickable': 'clickable']">
          <IconFolderOpen class="icon-folder" />
        </td>
      </tr>

      <tr
        v-for="file in files"
        :key="file.name"
        :class="{ clickable: file.directory }"
        @click="onFileClick(file)"
      >
        <td class="icon-row">
          <IconFolder v-if="file.directory" class="icon-folder" />
          <IconFile v-else class="icon-file" />
        </td>
        <td>{{ file.name }}</td>
        <td>
          <span class="float-end">{{ file.size }}</span>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<style scoped>

td{
  font-size: 20px;
}

.unclickable {
  cursor: default;
  pointer-events: none;
}

.clickable {
  cursor: pointer;
}

.icon-row {
  width: 2em;
}

.icon-folder {
  width: 2em;
}

.icon-file {
  width: 2em;
}

</style>
