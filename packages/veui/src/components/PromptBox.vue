<template>
<veui-dialog
  ref="dialog"
  :ui="realUi"
  :overlay-class="mergeOverlayClass('veui-prompt-box')"
  :open.sync="localOpen"
  :priority="priority"
  :closable="false"
  :before-close="beforeClose"
  role="alertdialog"
  @ok="submit"
  @cancel="cancel"
  @afterclose="$emit('afterclose')"
>
  <template slot="title">
    <template v-if="title">
      {{ title }}
    </template>
    <slot
      v-else
      name="title"
    />
  </template>
  <p class="veui-prompt-box-info">
    <slot/>
  </p>
  <div>
    <veui-input
      v-model="localValue"
      autofocus
      class="veui-prompt-box-input"
      @keydown.enter="submit(true)"
    />
  </div>
  <template slot="foot">
    <slot name="foot"/>
  </template>
</veui-dialog>
</template>

<script>
import Input from './Input'
import Dialog from './Dialog'
import { pick } from 'lodash'
import config from '../managers/config'
import ui from '../mixins/ui'
import overlay from '../mixins/overlay'

config.defaults({
  'promptbox.priority': 100
})

export default {
  name: 'veui-prompt-box',
  components: {
    'veui-input': Input,
    'veui-dialog': Dialog
  },
  mixins: [ui, overlay],
  props: {
    ...pick(Dialog.props, ['open', 'title', 'beforeClose']),
    content: {
      type: String,
      default: '请输入'
    },
    value: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      localOpen: this.open,
      priority: config.get('promptbox.priority'),
      localValue: this.value
    }
  },
  watch: {
    open (value) {
      this.localOpen = value
    },
    value (value) {
      this.localValue = value
    },
    localOpen (value) {
      this.$emit('update:open', value)
    },
    localValue (value) {
      this.$emit('input', value)
    }
  },
  methods: {
    submit (close) {
      this.$emit('ok')
      if (close) {
        this.$refs.dialog.close('ok')
      }
    },
    cancel () {
      this.localValue = ''
      this.$emit('cancel')
    }
  }
}
</script>
