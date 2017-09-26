<template>
  <div class="continer">
    <div class="header">
      <h1 class="title">Get Emoji</h1>
      <p class="emoji-type">
        <i-switch v-model="copyEmojiName" size="large">
          <span slot="open">on</span>
          <span slot="close">off</span>
        </i-switch>
        Copy emoji name
      </p>

      <i-input size="large"  placeholder="search" v-model="keyword" autofocus class="searchIpt">
        <i-button type="text" slot="append" @click="resetKeyword">RESET</i-button>
      </i-input>

    </div>
    <Alert show-icon>点击表情复制符号代码。（ Click the Emoji code and it will be copied to your clipboard.）</Alert>
    <div class="content">
      <section v-for="(value, key) in categories" v-show="checkCategoryShow(key)">
        <h2 class="emoji-category">{{ key }}</h2>
        <ul>
          <!-- <li v-for="(e, k) in value" :title="e.name" v-show="checkEmojiShow(e, k, key)" @click="copyEmoji('emoji-'+k)" class="emoji-item"> -->
          <li v-for="(e, k) in value" :title="e.name" v-show="e.matched" @click="copyEmoji('emoji-'+k)" class="emoji-item">
          <!-- <li v-for="e in value" :title="e.name" v-show="e.isShow"> -->
            <div>
              <span v-show="isPc">{{ e['char'] }}</span>
              <span :data-keyword="e.keywords.join(' ')" class="emoji-code" :id="'emoji-'+k" :data-clipboard-text="copyEmojiName ? (':' + k + ':') : e['char']" >{{ isPc ? (':' + k + ':') : e['char'] }}</span>
            </div>
          </li>
        </ul>
      </section>
    </div>
    <div class="footer">
      <span>Made with <a href="//github.com/16free">@Svend</a>  -|- pv:<span id="busuanzi_value_site_pv"></span>   uv:<span id="busuanzi_value_site_uv"></span></span>
    </div>

  </div>
</template>
<script>
var Clipboard = require('clipboard')
import Switch from 'iview/src/components/switch'
import Alert from 'iview/src/components/alert'
import Input from 'iview/src/components/input'
import Button from 'iview/src/components/Button'
import Message from 'iview/src/components/message'
var emoji = require('emojilib')
var emojis = Object.assign({}, emoji.lib)
console.log(emoji)

var categories = {}
for (let k in emojis) {
  var e = emojis[k]
  e.matched = true
  categories[e.category] = categories[e.category] || {}
  categories[e.category][k] = e
}
delete categories['_custom']

function isPC () {
  var userAgentInfo = navigator.userAgent
  var Agents = ['Android', 'iPhone',
    'SymbianOS', 'Windows Phone',
    'iPad', 'iPod']
  var flag = true
  for (var v = 0; v < Agents.length; v++) {
    if (userAgentInfo.indexOf(Agents[v]) > 0) {
      flag = false
      break
    }
  }
  if (!flag) {
    require('@/styles/emoji.mobile.css')
  }
  return flag
}

export default {
  name: 'Emoji',
  data () {
    return {
      isPc: isPC(),
      copyEmojiName: false,
      categories: categories,
      keyword: '',
      keywordChangeTimer: null
    }
  },
  components: {
    'i-switch': Switch,
    Alert,
    'i-input': Input,
    'i-button': Button
  },
  methods: {
    resetKeyword () {
      this.keyword = ''
    },
    checkEmojiShow () {
      let keyword = this.keyword && this.keyword.trim()
      for (let k in categories) {
        let category = categories[k]
        for (let ck in category) {
          let emoji = category[ck]
          if (!keyword) {
            emoji.matched = true
          }
          let matchKeyWords = emoji.keywords.concat([ck])
          if (matchKeyWords.join(' ').indexOf(keyword) > -1) {
            emoji.matched = true
          } else {
            emoji.matched = false
          }
        }
      }
    },
    checkCategoryShow (categoryId) {
      let category = categories[categoryId]
      for (let k in category) {
        let emoji = category[k]
        if (emoji.matched) {
          return true
        }
      }
      return false
    },
    copyEmoji (emojiCodeId) {
      let clipboard = new Clipboard('#' + emojiCodeId)
      clipboard.on('success', function (e) {
        console.info('Copied:', e.text)
        Message.success('Copied: ' + e.text)
        e.clearSelection()
        clipboard.destroy()
      })
      clipboard.on('error', function (e) {
        console.error('Action:', e.action)
        console.error('Trigger:', e.trigger)
        clipboard.destroy()
      })
    },
    parse2Twemoji () {
      var twemojiScript = document.createElement('script')
      twemojiScript.src = '//twemoji.maxcdn.com/2/twemoji.min.js?2.2.3'
      twemojiScript.onload = function () {
        window.twemoji.parse(document.body)
      }
      document.head.appendChild(twemojiScript)
    }
  },
  mounted () {
    if (this.isPc) {
      this.parse2Twemoji()
    }

    var busuanziScript = document.createElement('script')
    busuanziScript.src = '//dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js'
    busuanziScript.async = 'async'
    document.head.appendChild(busuanziScript)
  },
  watch: {
    keyword (newVal) {
      if (this.keywordChangeTimer) {
        clearTimeout(this.keywordChangeTimer)
      }
      this.keywordChangeTimer = setTimeout(() => {
        console.log(this.keyword)
        this.checkEmojiShow()
        clearTimeout(this.keywordChangeTimer)
      }, 200)
    }
  }
}
</script>
<style src="@/styles/emoji.css">
</style>