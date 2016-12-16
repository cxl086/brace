<template>
  <div id='vue-bulma-editor'></div>
</template>

<script>
import * as brace from 'brace'
import 'brace/ext/modelist'
import 'brace/ext/themelist'
var modelist = brace.acequire('ace/ext/modelist')
var themelist = brace.acequire('ace/ext/themelist')
var editor

var regMap = {
  isInt: new RegExp('^\\d+$')
}

export default {
  props: {
    mode: {
      type: String,
      default: 'json',
      validator: (val) => modelist.modes.findIndex((mode) => mode.name === val) > -1
    },
    theme: {
      type: String,
      default: 'github',
      validator: (val) => themelist.themes.findIndex((theme) => theme.name === val) > -1
    },
    // todo editor split
    fontsize: {
      type: String,
      default: '12px',
      validator: (val) => parseInt(val) > 11 && parseInt(val) < 25
    },
    codefolding: {
      type: String,
      default: 'markbegin',
      validator: (val) => ['manual', 'markbegin', 'markbeginend'].indexOf(val) > -1
    },
    // todo key binding
    softwrap: {
      type: String,
      default: 'free',
      validator: (val) => ['off', 'free'].indexOf(val) > -1 || regMap.isInt.test(val)
    },
    selectionstyle: {
      type: String,
      default: 'text',
      validator: (val) => ['text', 'line'].indexOf(val) > -1
    },
    highlightline: {
      type: Boolean,
      default: true
    }
    // todo a lot of other things...
  },

  methods: {
    setMode () {
      let modeObj = modelist.modesByName[this.mode]

      if (modeObj) {
        require('brace/mode/' + modeObj.name)
        editor.getSession().setMode(modeObj.mode)
      }
    },
    setTheme () {
      let themeObj = themelist.themesByName[this.theme]

      if (themeObj) {
        require('brace/theme/' + themeObj.name)
        editor.setTheme(themeObj.theme)
      }
    }
  },

  mounted () {
    editor = brace.edit('vue-bulma-editor')
    this.setMode()
    this.setTheme()
    editor.$blockScrolling = Infinity
  },

  watch: {
    mode () {
      this.setMode()
    },
    theme () {
      this.setTheme()
    },
    fontsize (newVal) {
      editor.setFontSize(newVal)
    },
    codefolding (newVal) {
      editor.session.setFoldStyle(newVal)
      editor.setShowFoldWidgets(newVal !== 'manual')
    },
    softwrap (newVal) {
      editor.setOption('wrap', newVal)
    },
    selectionstyle (newVal) {
      editor.setOption('selectionStyle', newVal)
    },
    highlightline (newVal) {
      editor.setHighlightActiveLine(newVal)
    }
  }
}
</script>