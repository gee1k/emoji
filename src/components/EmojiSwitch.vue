<template>
  <div class="switch">
    <input class="switch-checkbox" :id="switchId" type="checkbox" @click="clickSwitch" :checked="checked">
    <label class="switch-label" :for="switchId">
        <span class="switch-inner" :data-on="onLabel" :data-off="offLabel"></span>
        <span class="switch-switch"></span>
    </label>
  </div>
</template>
<script>
export default {
  name: 'emoji-switch',
  props: {
    onLabel: {
      type: String,
      required: true
    },
    offLabel: {
      type: String,
      required: true
    },
    checked: {
      type: Boolean,
      default: true
    }
  },
  data () {
    return {
      switchId: 'switch-' + (Math.random() * 100)
    }
  },
  methods: {
    clickSwitch: function (e) {
      this.$emit('update:checked', e.target.checked)
    }
  }
}
</script>
<style scoped>
.switch {
    width: 90px;
    margin: 0;
    -webkit-user-select:none;
    -moz-user-select:none;
    -ms-user-select: none;
}

.switch-checkbox {
    display: none;
}

.switch-label {
    display: block;
    overflow: hidden;
    cursor: pointer;
    border: 2px solid #999999;
    border-radius: 20px;
}

.switch-inner {
    display: block;
    width: 200%;
    margin-left: -100%;
    transition: margin 0.3s ease-in 0s;
}

.switch-inner::before, .switch-inner::after {
    display: block;
    float: right;
    width: 50%;
    height: 30px;
    padding: 0;
    line-height: 30px;
    font-size: 14px;
    color: white;
    font-family:
    Trebuchet, Arial, sans-serif;
    font-weight: bold;
    box-sizing: border-box;
}

.switch-inner::after {
    content: attr(data-on);
    padding-left: 10px;
    background-color: #00e500;
    color: #FFFFFF;
}

.switch-inner::before {
    content: attr(data-off);
    padding-right: 10px;
    background-color: #EEEEEE;
    color: #999999;
    text-align: right;
}

.switch-switch {
    position: absolute;
    display: block;
    width: 22px;
    height: 22px;
    margin: 4px;
    background: #FFFFFF;
    top: 0;
    bottom: 0;
    right: 56px;
    border: 2px solid #999999;
    border-radius: 20px;
    transition: all 0.3s ease-in 0s;
}

.switch-checkbox:checked + .switch-label .switch-inner {
    margin-left: 0;
}

.switch-checkbox:checked + .switch-label .switch-switch {
    right: 0px;
}
</style>

