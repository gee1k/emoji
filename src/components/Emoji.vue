<template>
  <div class="continer">
    <div class="header">
      <h1 class="title">Emoji Sheet</h1>
      <input type="text" class="searchIpt" placeholder="search" v-model="keyword">
      <span class="emoji-type-label">Emoji Type:</span>
      <emoji-switch class="emoji-type-switch" label="Emoji Type" onLabel="Code" offLabel="Char" :checked.sync="isEmojiType"></emoji-switch>
    </div>
    <div class="content">
      <div class="info">
        <sub>Emoji Type 为 Code 时仅在支持 Emoji Code 的应用或网站上可以使用，如 Github。(Emoji Type for Code is available only on applications or web sites that support Emoji Code, such as Github.)</sub><br>
        <sub>Emoji Type 为 Char 时在任何支持 emoji 字体的地方都可以使用。不过只是作为字体展示，而不是 Emoji 图片。(Emoji Type is available for use in any place that supports Emoji fonts when Char is used. But just as a font display, not a Emoji picture.)</sub><br>
        <strong>点击表情复制符号代码。（ Click the emoji code and it will be copied to your clipboard.）</strong>
      </div>
      <section v-for="(value, key) in categories" v-show="checkCategoryShow(key)">
        <h2 class="emoji-category">{{ key }}</h2>
        <ul>
          <li v-for="(e, k) in value" :title="e.name" v-show="checkEmojiShow(e, k, key)" @click="copyEmoji('emoji-'+k)" class="emoji-item"
          :style="{width: isEmojiType ? '340px' : '60px'}">
          <!-- <li v-for="e in value" :title="e.name" v-show="e.isShow"> -->
            <div>
              <span :style="{display: isEmojiType ? 'inline-block' : 'none'}">{{ e['char'] }}</span>
              <input :data-keyword="e.keywords.join(' ')" class="emoji-code" :style="{fontSize: isEmojiType ? 'inherit' : '22px'}" :id="'emoji-'+k" :value="isEmojiType ? (':' + k + ':') : e['char']"  :size="isEmojiType ? (':' + k + ':').length : e['char'].length" readonly>
            </div>
          </li>
        </ul>
      </section>
    </div>
    <div class="footer">
      <span>Made with <a href="//github.com/16free">@Svend</a></span>
    </div>

  </div>
</template>
<script>
import EmojiSwitch from './EmojiSwitch'
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

export default {
  name: 'Emoji',
  data () {
    return {
      isEmojiType: true,
      categories: categories,
      keyword: ''
    }
  },
  components: {
    EmojiSwitch
  },
  methods: {
    emojiTypeSwitch () {
      if (this.emojiType === 'code') {
        this.emojiType = 'char'
      } else {
        this.emojiType = 'code'
      }
    },
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
      let node = document.querySelector('#' + emojiCodeId)
      node.select()
      document.execCommand('copy')
      node.parentNode.classList = ['copied']
      setTimeout(function () {
        node.parentNode.classList = []
      }, 1800)
    }
  },
  mounted () {
    var twemojiScript = document.createElement('script')
    twemojiScript.src = '//twemoji.maxcdn.com/2/twemoji.min.js?2.2.3'
    twemojiScript.onload = function () {
      window.twemoji.parse(document.body)
    }
    document.head.appendChild(twemojiScript)
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

.emoji-type-switch {
  position: absolute;
  top: 50%;
  right: 110px;
  transform: translateY(-50%);
}

.emoji-type-label {
  position: absolute;
  top: 50%;
  right: 200px;
  transform: translateY(-50%);
}

.info {
  margin-top: 8px;
  height: 80px;
  padding: 0 1em;
  color: #6a737d;
  border-left: 0.25em solid #6a737d;
  background-color: #ccc;
  font-size: 12px;
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
  appearance: none;
  -webkit-appearance: none;
  border: 0;
  box-shadow: none;
  background-color: transparent;
  cursor: pointer;
  padding: 10px 10px 10px 0;
  user-select: text;
  -webkit-rtl-ordering: logical;
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
  margin-left: -15px;
  padding: 4px;
  font-size: 11px;
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


