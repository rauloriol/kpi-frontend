<template>
  <el-form
    ref="fieldForm"
    :model="form"
    label-width="auto"
    class="demo-dynamic"
  >
    <el-form-item
      :label="$t('title')"
      prop="title"
      :rules="[
        {
          required: true,
          message: $t('field-title-needed'),
          trigger: 'blur',
        },
        {
          min: 4,
          max: 255,
          message: $t('field-title-length'),
          trigger: ['blur', 'change'],
        },
      ]"
    >
      <el-input v-model="form.title" role="input-field-title"></el-input>
    </el-form-item>

    <el-form-item
      :label="$t('description')"
      prop="description"
      :rules="[
        {
          required: true,
          message: $t('field-description-needed'),
          trigger: 'blur',
        },
        {
          min: 1,
          message: $t('field-description-length'),
          trigger: ['blur', 'change'],
        },
      ]"
    >
      <el-input type="textarea" v-model="form.description"></el-input>
    </el-form-item>

    <el-form-item :label="$t('required')">
      <el-switch v-model="form.required"></el-switch>
    </el-form-item>

    <el-form-item :label="$t('fillable')">
      <el-switch v-model="form.fillable"></el-switch>
    </el-form-item>

    <el-form-item
      :label="$t('field-type')"
      prop="type"
      :rules="[
        {
          required: true,
          message: $t('field-type-needed'),
          trigger: 'blur',
        },
      ]"
    >
      <el-select
        v-model="form.type"
        :placeholder="$t('field-type-placeholder')"
      >
        <el-option :label="$t('field-type-limit')" value="text"></el-option>
        <el-option :label="$t('field-type-nolimit')" value="textarea"></el-option>
        <el-option :label="$t('field-type-number')" value="number"></el-option>
        <el-option :label="$t('field-type-radio')" value="radio"></el-option>
      </el-select>
    </el-form-item>

    <el-form-item v-if="form.type == 'text'" :label="$t('field-limit')">
      <el-slider :min="min" :max="max" v-model="form.maxlength" show-input>
      </el-slider>
    </el-form-item>

    <el-form-item
      v-if="form.type == 'radio'"
      :label="$t('available-values')"
      prop="values"
      :rules="[
        {
          required: true,
          message: $t('field-values-needed'),
          trigger: 'blur',
        },
      ]"
    >
      <el-tag
        :key="value"
        v-for="value in form.values"
        closable
        :disable-transitions="false"
        @close="handleClose(value)"
      >
        {{ value }}
      </el-tag>
      <el-input
        class="input-new-tag"
        v-if="inputVisible"
        v-model="inputValue"
        ref="saveTagInput"
        size="mini"
        @keyup.enter="handleInputConfirm"
        @blur="handleInputConfirm"
      >
      </el-input>
      <el-button v-else class="button-new-tag" size="small" @click="showInput"
        >+ {{$t('new-value')}}</el-button
      >
    </el-form-item>

    <el-form-item>
      <el-button role="submit-field-form" type="primary" @click="submitForm">{{ submitText }}</el-button>
      <el-button @click="$router.replace('/fields')">{{$t('cancel')}}</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
import useNotify from '@/hooks/notify.js';

export default {
  props: ['id'],
  emits: ['save-data'],
  setup() {
    const { notify } = useNotify();

    return { notify };
  },
  data() {
    return {
      min: 4,
      max: 255,
      inputVisible: false,
      inputValue: '',
      form: {
        title: '',
        description: '',
        required: false,
        fillable: false,
        type: '',
        values: [],
        maxlength: 4,
      },
    };
  },
  computed: {
    submitText() {
      return this.id ? this.$t('update-field') : this.$t('create-field');
    },
    notifyMessage() {
      return this.id
        ? this.$t('update-field-success')
        : this.$t('create-field-success');
    },
    notifyTitle() {
      return this.id
        ? `${this.$t('updated-field')}: ${this.form.title}`
        : `${this.$t('new-field')}: ${this.form.title}`;
    },
  },
  methods: {
    handleClose(tag) {
      this.form.values.splice(this.form.values.indexOf(tag), 1);
    },
    showInput() {
      this.inputVisible = true;
      this.$nextTick(() => {
        this.$refs.saveTagInput.$refs.input.focus();
      });
    },

    handleInputConfirm() {
      let inputValue = this.inputValue;
      if (inputValue) {
        this.form.values.push(inputValue);
      }
      this.inputVisible = false;
      this.inputValue = '';
    },
    submitForm() {
      this.$refs['fieldForm'].validate((valid) => {
        if (valid) {
          if (this.form.type != 'radio') this.form.values = null; // Clear values created when type is different that radio
          if (this.form.type != 'text') this.form.maxlength = null; // Clear maxlength when type is different that text

          this.$emit('save-data', this.form);

          this.notify(this.notifyTitle, this.notifyMessage, 'success');
        } else {
          this.notify(
            this.$t('error'),
            this.$t('form-not-valid'),
            'error'
          );

          return false;
        }
      });
    },
  },

  created() {
    // If id exist, mean this is an update form
    if (this.id) {
      this.form = JSON.parse(
        JSON.stringify(
          this.$store.getters['fields/fields'].find(
            (field) => field.id == this.id
          )
        )
      ); // Clone Field instead of editing it directly
    }
  },
};
</script>

<style scoped>
.el-tag + .el-tag {
  margin-left: 10px;
}
.button-new-tag {
  margin-left: 10px;
  height: 32px;
  line-height: 30px;
  padding-top: 0;
  padding-bottom: 0;
}
.input-new-tag {
  width: 90px;
  margin-left: 10px;
  vertical-align: bottom;
}
</style>
