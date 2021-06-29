<template>
  <div>
    <h2>Test Popup</h2>

    <el-row>
      返回结果：
      <el-card class="box-card">
        <div class="text item">
          {{ msgFromPopup }}
        </div>
      </el-card>
      <el-button type="primary" @click="openPopup">打开Popup</el-button>
      <el-button type="danger" @click="closePopup">关闭Popup</el-button>
    </el-row>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

const popupUrl = 'https://popup-test-child.vercel.app'

let _popup: Window | undefined = undefined
export default Vue.extend({
  data() {
    return {
      msgFromPopup: '',
    }
  },
  created() {
    window.addEventListener('message', this.receiveMessage, false)
  },
  methods: {
    receiveMessage(event: any) {
      if (event.origin !== popupUrl) return

      console.log('event.data', event.data)
      if (event.data === 'connect') {
        _popup?.postMessage('message to be processed', popupUrl)
        return
      }

      this.msgFromPopup = event.data
      if (_popup) {
        _popup.close()
        _popup = undefined
      }
    },
    openPopup() {
      if (_popup !== undefined && !_popup.closed) return

      window.name = 'parent'
      _popup = window.open(
        popupUrl,
        '_blank',
        'location, resizable, width=460, height=675',
      ) as Window
    },
    closePopup() {
      if (_popup !== undefined) {
        _popup.close()
        _popup = undefined
      }
    },
  },
})
</script>
