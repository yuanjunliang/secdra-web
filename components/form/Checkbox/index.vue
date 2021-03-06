<template>
  <label class="checkbox switch" :class="classObject">
    <slot>
      <template v-if="isSwitch">
        <div class="switch-box" :class="{ active: model }">
          <div
            v-ripple="!disabled && { class: `${color}-text` }"
            class="checkbox-inner"
            :class="{ 'dark-fade-text': !model }"
          >
            <div class="content">
              <i class="icon"></i>
            </div>
          </div>
        </div>
      </template>
      <template v-else>
        <div
          v-ripple="{ class: `${color}-text` }"
          class="checkbox-inner"
          :class="{ 'dark-fade-text': !model }"
        >
          <div class="content">
            <i
              class="icon"
              :class="{
                'ali-icon-check-box': !model,
                'ali-icon-check-box-fill': model
              }"
            ></i>
          </div>
        </div>
      </template>
    </slot>
    <span
      v-if="label !== null"
      class="checkbox-label"
      :class="{ 'dark-text': !model }"
      >{{ label }}</span
    >
    <input
      v-model="model"
      type="checkbox"
      class="checkbox-original"
      :disabled="disabled"
    />
  </label>
</template>

<script>
export default {
  componentName: "Checkbox",
  model: {
    prop: "input",
    event: "input"
  },
  props: {
    input: {
      type: Boolean,
      default: false
    },
    label: {
      type: String | Number,
      default: null
    },
    disabled: {
      type: Boolean,
      default: false
    },
    color: {
      type: String,
      default: "default"
    },
    big: {
      type: Boolean,
      default: false
    },
    small: {
      type: Boolean,
      default: false
    },
    isSwitch: {
      type: Boolean,
      default: false
    },
    // 如果value是Object的话valueKey必填
    value: {
      type: String | Number | Object | Boolean,
      default: true
    },
    valueKey: {
      type: String | Number,
      default: null
    }
  },
  data() {
    return {
      checkboxGroup: {},
      selfModel: this.input
    }
  },
  computed: {
    model: {
      get() {
        if (this.isGroup) {
          if (typeof this.value === "object") {
            return this.checkboxGroup.getIndex(this.value, this.valueKey) !== -1
          } else {
            return this.checkboxGroup.getIndex(this.value) !== -1
          }
        } else if (typeof this.value === "object") {
          return this.selfModel[this.valueKey] === this.value[this.valueKey]
        } else {
          return this.selfModel === this.value
        }
      },
      set(val) {
        if (this.isGroup) {
          this.checkboxGroup.getNewModel(this.value, this.valueKey)
        } else {
          this.selfModel = val
          this.$emit("input", val)
        }
      }
    },
    isGroup() {
      let parent = this.$parent
      while (parent) {
        if (parent.$options.componentName !== "CheckboxGroup") {
          parent = parent.$parent
        } else {
          this.checkboxGroup = parent
          return true
        }
      }
      return false
    },
    classObject() {
      const classObject = {}
      classObject[this.color + "-color"] = true
      classObject.small = this.small
      classObject.big = this.big
      classObject.disabled = this.disabled
      return classObject
    }
  },
  watch: {
    input(newVal) {
      this.selfModel = newVal
    }
  }
}
</script>

<style type="text/less" lang="less" scoped></style>
