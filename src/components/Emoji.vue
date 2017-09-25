<template>
  <div class="continer">
    <div class="header">
      <h1 class="title">Get Emoji</h1>
      <input type="text" class="searchIpt" placeholder="search" v-model="newKeyword">
        <emoji-switch class="emoji-type-switch" title="Emoji Type:" onLabel="Code" offLabel="Char" :checked.sync="isEmojiType"></emoji-switch>
    </div>
    <div class="info">
      <sub>Emoji Type 为 Code 时仅在支持 Emoji Code 的应用或网站上可以使用，如 Github。</sub><br>
      <sub>Emoji Type 为 Char 时在任何支持 Emoji 字体的地方都可以使用。不过只是作为字体展示，而不是 Emoji 图片。</sub><br><br>
      <sub>Emoji Type for Code is available only on applications or web sites that support Emoji Code, such as Github.</sub><br>
      <sub>Emoji Type is available for use in any place that supports Emoji fonts when Char is used. But just as a font display, not a Emoji picture.</sub><br><br>
      <strong>点击表情复制符号代码。（ Click the Emoji code and it will be copied to your clipboard.）</strong>
    </div>
    <div class="info-mobile">
      <strong>点击表情复制符号代码。（ Click the Emoji code and it will be copied to your clipboard.）</strong>
    </div>
    <div class="content">
      <section v-for="(value, key) in categories" v-show="checkCategoryShow(key)">
        <h2 class="emoji-category">{{ key }}</h2>
        <ul>
          <li v-for="(e, k) in value" :title="e.name" v-show="checkEmojiShow(e, k, key)" @click="copyEmoji('emoji-'+k)" class="emoji-item"
          :style="{width: isEmojiType ? '340px' : '60px'}" :class="{char: !isEmojiType}">
          <!-- <li v-for="e in value" :title="e.name" v-show="e.isShow"> -->
            <div>
              <span :style="{display: isEmojiType ? 'inline-block' : 'none'}">{{ e['char'] }}</span>
              <span :data-keyword="e.keywords.join(' ')" class="emoji-code" :style="{fontSize: isEmojiType ? 'inherit' : '22px'}" :id="'emoji-'+k" :data-clipboard-text="isEmojiType ? (':' + k + ':') : e['char']" >{{ isEmojiType ? (':' + k + ':') : e['char'] }}</span>
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
import EmojiSwitch from './EmojiSwitch'
var Clipboard = require('clipboard')
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
  return flag
}

export default {
  name: 'Emoji',
  data () {
    return {
      isEmojiType: isPC(),
      categories: categories,
      newKeyword: '',
      keyword: '',
      keywordChangeTimer: null,
      twemojiInited: false
    }
  },
  components: {
    EmojiSwitch
  },
  methods: {
    checkEmojiShow (e, emojiId, categoryId) {
      let keyword = this.keyword && this.keyword.trim()
      if (!keyword) {
        categories[categoryId][emojiId].matched = true
        return true
      }

      let mactch = false
      if (e.keywords.concat([emojiId]).join(' ').indexOf(keyword) > -1) {
        mactch = true
      }
      categories[categoryId][emojiId].matched = mactch
      return mactch
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
        e.clearSelection()
        console.info('Copied:', e.text)
        e.trigger.parentNode.classList = ['copied']
        setTimeout(function () {
          e.trigger.parentNode.classList = []
        }, 1800)
      })
      clipboard.on('error', function (e) {
        console.error('Action:', e.action)
        console.error('Trigger:', e.trigger)
      })
    },
    parse2Twemoji () {
      if (this.twemojiInited) {
        return
      }
      var twemojiScript = document.createElement('script')
      twemojiScript.src = '//twemoji.maxcdn.com/2/twemoji.min.js?2.2.3'
      twemojiScript.onload = function () {
        window.twemoji.parse(document.body)
      }
      document.head.appendChild(twemojiScript)
      this.twemojiInited = true
    }
  },
  mounted () {
    if (this.isEmojiType) {
      this.parse2Twemoji()
    }

    var busuanziScript = document.createElement('script')
    busuanziScript.src = '//dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js'
    busuanziScript.async = 'async'
    document.head.appendChild(busuanziScript)
  },
  watch: {
    isEmojiType (newVal) {
      this.parse2Twemoji()
    },
    newKeyword (newVal) {
      if (this.keywordChangeTimer) {
        clearTimeout(this.keywordChangeTimer)
      }
      this.keywordChangeTimer = setTimeout(() => {
        this.keyword = newVal
        console.log(this.keyword)
        clearTimeout(this.keywordChangeTimer)
      }, 200)
    }
  }
}
</script>
<style>
a {
  text-decoration: none;
    cursor: pointer;
    color: #08c;
}

ul {
  display: table;
  padding: 0 0 1em 1.5em;
}

li {
  list-style: none;
  float: left;
  margin-left: 20px;
  margin-bottom: 8px;
  cursor: pointer;

}

.continer {
  max-width: 1500px;
  margin: 0 auto;
}

.header {
  overflow: hidden;
  position: relative;
  height: 70px;
  border-bottom: 1px solid rgba(0, 0, 0, .2);
  padding: 10px 0 7px 0;
}

.header>.title {
  padding-top: 10px;
}

.header>.searchIpt {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  border-radius: 20px;
  border: 1px solid rgba(0, 0, 0, .2);
  height: 30px;
  width: 500px;
  font: inherit;
  padding: 5px 15px;
}

.header>.searchIpt:focus {
  outline: none;
  border: 1px solid #3b99fc;
  box-shadow: 0px 0px 10px 0px #3b99fc;
}

.emoji-type {
  overflow: hidden;
  text-overflow: ellipsis;
  word-break: keep-all;
  white-space: nowrap;
}

.emoji-type-switch {
  position: absolute;
  top: 50%;
  right: 110px;
  transform: translateY(-50%);
}

.info,
.info-mobile {
  margin-top: 8px;
  padding: 1em;
  color: #6a737d;
  border-left: 0.25em solid #6a737d;
  background-color: #ccc;
  font-size: 12px;
}

.info-mobile {
  display: none;
}

@media (max-width: 767px) {
  li.char {
    margin-left: 0;
    width: 40px !important;
  }

  .info{
    display: none;
  }

  .info-mobile {
    display: block;
  }

  .header {
    overflow: hidden;
    position: relative;
    height: initial;
    border-bottom: 1px solid rgba(0, 0, 0, .2);
    padding: 10px 0 7px 0;
    text-align: center;
  }

  .header>.searchIpt {
    position: initial;
    left: initial;
    top: initial;
    transform: initial;
    width: initial;
    border-radius: 20px;
    border: 1px solid rgba(0, 0, 0, .2);
    height: 30px;
    font: inherit;
    margin: 5px auto;
    padding: 5px 15px;
  }

  .emoji-type-switch {
    position: relative;
    top: initial;
    text-align: initial;
    left: 66%;
    transform: translateX(-50%);
  }
}

.emoji{
  width: 18px !important;
  height: 18px !important;
  position: relative;
  top: 6px;
}

.emoji-category {
  margin: 20px 0;
}

.emoji-item> div {
  white-space: nowrap;
}

.emoji-item:hover > div {
  margin-left: -5px;
}

.emoji-code {
  cursor: pointer;
  -webkit-user-select: text;
  -moz-user-select: text;
  -ms-user-select: text;
  font-family: monospace;
}

div.copied::after {
  content: 'Copied';
  color: #63bf8a;
  overflow: hidden;
  animation-duration: 1.8s;
  animation-name: fadeOut;
  animation-iteration-count: 1;
  font-family: "Helvetica Neue",helvetica,arial,sans-serif;
  padding-left: 6px;
  font-size: 12px;
  font-weight: bold;
  user-select: none;
}

.footer {
  border-top: 1px solid rgba(0, 0, 0, .2);
  height: 40px;
  text-align: center;
  padding-top: 10px;
  color: #ccc;
  font-size: 12px;
}

@keyframes fadeOut {
  0% {opacity: 1;}
  70% {opacity: .7;}
  100% {opacity: 0;}
}

@-webkit-keyframes fadeOut {
  0% {opacity: 1;}
  70% {opacity: .7;}
  100% {opacity: 0;}
}

</style>


