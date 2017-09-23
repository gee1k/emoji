<template>
  <div class="continer">
    <div class="header">
      <h1 class="title">Emoji Sheet</h1>
      <input type="text" class="searchIpt" placeholder="search" v-model="keyword">
    </div>
    <div class="content">
      <div class="info">
        点击表情复制符号代码。（ Click the emoji code and it will be copied to your clipboard.）
      </div>
      <section v-for="(value, key) in categories" v-show="checkCategoryShow(key)">
        <h2 class="emoji-category">{{ key }}</h2>
        <ul>
          <li v-for="(e, k) in value" :title="e.name" v-show="checkEmojiShow(e, k, key)" @click="copyEmoji('emoji-'+k)" class="emoji-item">
          <!-- <li v-for="e in value" :title="e.name" v-show="e.isShow"> -->
            <div>
              {{ e['char'] }}
              <input :data-keyword="e.keywords.join(' ')" class="emoji-code" :id="'emoji-'+k" :value="':' + k + ':'" readonly>
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
var emoji = require('emojilib')
var emojis = Object.assign({}, emoji.lib)

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
      categories: categories,
      keyword: ''
    }
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
  font-family: monospace;
}

li {
  list-style: none;
  float: left;
  width: 330px;
  margin-bottom: .5em;
  cursor: pointer;

}

.continer {
  width: 100%;
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

.info {
  margin-top: 8px;
  height: 80px;
  line-height: 80px;
  padding: 0 1em;
    color: #6a737d;
    border-left: 0.25em solid #6a737d;
    background-color: #ccc;
}

.emoji{
  width: 22px !important;
  height: 22px !important;
  position: relative;
  top: 9px;
}

.emoji-category {
  margin: 20px 0;
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
}

div.copied::after {
  content: 'Copied';
  color: #63bf8a;
  overflow: hidden;
  animation-duration: 1.8s;
  animation-name: fadeOut;
  animation-iteration-count: 1;
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


