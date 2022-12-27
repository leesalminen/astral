<template>
  <p>Hello</p>
  <div v-if="files.length > 0">
    <p>World</p>
    <div v-for="(file, idx) in files" v-bind:key="idx">
        <p>{{file.url}}</p>
        <img :src="file.url" alt="">
    </div>
  </div>
</template>

<script>
import helpersMixin from '../utils/mixin'

import WebTorrent from 'webtorrent/webtorrent.min'
import toBlobURL from 'stream-to-blob-url'

const webTorrentClient = new WebTorrent()

export default {
  name: 'BaseWebTorrentImage',
  mixins: [helpersMixin],
  props: {torrent: {type: String, required: true}},
  components: {},

  data() {
    return {
      files: [],
    }
  },

  created() {
    try {
      webTorrentClient.remove(this.torrent)
    } catch {
      console.log('cant remove torrent for whatever stupid reason todo')
    }

    console.log('adding torrent to queue', this.torrent)
    webTorrentClient.add(this.torrent, async (torrent) => {
      const output = []
      torrent.files.forEach(async (file) => {
        const blobUrl = await toBlobURL(file.createReadStream())
        output.push({url: blobUrl})

        this.files = output
      })
    })

    console.log('file array', this.files)
  }
}
</script>

