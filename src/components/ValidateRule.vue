<template>
  <div class="validate-rule">
    <div v-if="Object.keys(data.options).indexOf('required')>=0">
      <el-checkbox v-model="required.required">{{$t('fm.config.widget.required')}}</el-checkbox>
      <el-input
        size="mini"
        :disabled="!required.required"
        v-model="required.errorMsg"
        style=" width: 240px;"
        :placeholder="$t('fm.config.widget.errorExplain')"
      ></el-input>
    </div>
    <div v-if="Object.keys(data.options).indexOf('dataType')>=0">
      <span class="sub-text">{{$t('fm.config.widget.normalValidatorName')}}</span>
      <el-checkbox v-model="dataType.required"></el-checkbox>
      <el-select
        :disabled="!dataType.required"
        v-model="data.options.dataType"
        size="mini"
        style="padding-left: 10px;"
      >
        <el-option
          v-for="option in dataTypesOptions"
          :key="option"
          :value="option"
          :label="label(option)" />
      </el-select>
      <el-input
        size="mini"
        :disabled="!dataType.required"
        v-model="dataType.errorMsg"
        style="width: 240px;"
        :placeholder="$t('fm.config.widget.errorExplain')"
      ></el-input>
    </div>

    <div v-if="Object.keys(data.options).indexOf('pattern')>=0">
      <span class="sub-text">{{$t('fm.config.widget.patternValidatorName')}}</span>
      <el-checkbox v-model="pattern.required"></el-checkbox>
      <el-input
        size="mini"
        :disabled="!pattern.required"
        v-model="data.options.pattern"
        style=" width: 240px; padding-left: 10px;"
        :placeholder="$t('fm.config.widget.patternPlaceholder')"
      ></el-input>
      <el-input
        size="mini"
        :disabled="!pattern.required"
        v-model="pattern.errorMsg"
        style=" width: 240px;"
        :placeholder="$t('fm.config.widget.errorExplain')"
      ></el-input>
    </div>
  </div>
</template>

<script>
export default {
  props: ["data"],
  data() {
    return {
      required: {
        required: false,
        errorMsg: "",
      },
      dataType: {
        required: false,
        errorMsg: ""
      },
      pattern: {
        required: false,
        errorMsg: ""
      },
      dataTypes: ['string', 'number', 'boolean', 'integer', 'float', 'url', 'email', 'hex'],
    };
  },
  computed: {
    dataTypesOptions() {
      return this.data.options.dataTypes || this.dataTypes;
    },
  },
  mounted() {
  },
  methods: {
    label(option) {
      const key = `fm.config.widget.${option}`;
      const m = this.$t(key);
      return m === key ? option : m;
    },
    changeRequire() {
      const defaultRule = (this.data.rules || []).filter(e => Object.keys(e).indexOf('required') >= 0)[0];
      const errorMsg = defaultRule ? defaultRule.message : undefined;
      const required = !!(this.data.options ? this.data.options.required : false);
      this.required = {
        required,
        errorMsg: errorMsg ? errorMsg : `${this.data.name} ${this.$t("fm.config.widget.validatorRequired")}`
      };
      this.$emit("validateRequired", this.required.required, this.required.errorMsg);
    },
    changeDataType() {
      const defaultRule = (this.data.rules || []).filter(e => Object.keys(e).indexOf('type') >= 0)[0];
      const errorMsg = defaultRule ? defaultRule.message : undefined;
      const required = !!(this.data.options ? this.data.options.dataType : false);
      this.dataType = {
        required,
        errorMsg: errorMsg ? errorMsg : `${this.data.name} ${this.$t("fm.config.widget.validatorType")}`
      };
      this.$emit("validateDataType", this.dataType.required, this.dataType.errorMsg);
    },
  },
  created() {
    this.changeRequire();
    this.changeDataType();
  },
  watch: {
    "required.required": function(val) {
      this.data.options.required = val;
      this.$emit("validateRequired", this.data.options.required, this.required.errorMsg);
    },
    "dataType.required": function(val) {
      if (this.data.options.dataType !== undefined) {
        this.data.options.dataType = val ? this.data.options.dataType : "";
        this.$emit("validateDataType", this.data.options.dataType, this.dataType.errorMsg);
      }
    },
    "pattern.required": function(val) {
      this.data.options.pattern = val ? this.data.options.pattern : "";
      this.$emit("valiatePattern", this.data.options.pattern);
    },
    "required.errorMsg": function(errMsg) {
      this.$emit("validateRequired", this.data.options.required, errMsg);
    },
    "dataType.errorMsg": function(errMsg) {
      this.$emit("validateDataType", this.data.options.dataType, errMsg);
    },
    "pattern.errorMsg": function(errMsg) {
      this.$emit("valiatePattern", this.data.options.pattern, errMsg);
    },
    "data.model": function() {
      this.changeRequire();
      this.changeDataType();
    },
    "data.name": function() {
      this.changeRequire();
      this.changeDataType();
    },
    "data.options.dataType": function(val) {
      if (val === undefined) {
        this.dataType.errorMsg = ""
      }
      this.$emit("validateDataType", this.data.options.dataType, this.dataType.errorMsg);
    },
    "data.options.pattern": function(val) {
      if (val === undefined) {
        this.pattern.errorMsg = ""
      }
      this.$emit("valiatePattern", this.data.options.pattern, this.pattern.errorMsg);
    }
  }
};
</script>

<style lang="scss">
.sub-text {
  display: block;
  margin: 10px 0;
  border-bottom: 1px dotted #000;
}
</style>