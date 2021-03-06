<template>
  <div class="setting-text">
    <div v-if="input === null || !input.length" class="font-italic text-black-50">
      Empty
    </div>
    <div v-else>
      {{ input }}
    </div>
    <b-modal class="setting-object-modal" v-model="showModal" size="lg" @hidden="onModalHidden" @shown="onModalShown">
      <template v-slot:modal-header class="d-block">
        <div>
          <h5 class="mb-0" v-if="setting.name">{{ $t(setting.name) }}</h5>
          <h5 class="mb-0" v-else>{{ setting.key }}</h5>
          <small class="form-text text-muted" v-if="setting.helper">{{ $t(setting.helper) }}</small>
        </div>
        <button type="button" aria-label="Close" class="close" @click="onCancel">×</button>
      </template>
      <b-form-input ref="input" v-model="transformed" @keyup.enter="onSave" spellcheck="false" autocomplete="off"></b-form-input>
      <div slot="modal-footer" class="w-100 m-0 d-flex">
        <button type="button" class="btn btn-outline-secondary ml-auto" @click="onCancel">
            {{ $t('Cancel') }}
        </button>
        <button type="button" class="btn btn-secondary ml-3" @click="onSave" :disabled="! changed">
            {{ $t('Save')}}
        </button>
      </div>
    </b-modal>
  </div>
</template>

<script>
import settingMixin from "../mixins/setting";

export default {
  mixins: [settingMixin],
  props: ['value', 'setting'],
  data() {
    return {
      input: null,
      showModal: false,
      transformed: null,
    };
  },
  computed: {
    variant() {
      if (this.disabled) {
        return 'secondary';
      } else {
        return 'success';
      }
    },
    changed() {
      return JSON.stringify(this.input) !== JSON.stringify(this.transformed);
    }
  },
  watch: {
    value: {
      handler: function(value) {
        this.input = value;
      },
    }
  },
  methods: {
    onCancel() {
      this.showModal = false;
    },
    onEdit() {
      this.showModal = true;
    },
    onModalHidden() {
      this.transformed = this.copy(this.input);
    },
    onModalShown() {
      this.$refs.input.focus();
    },
    onSave() {
      this.input = this.copy(this.transformed);
      this.showModal = false;
      this.$emit('change', this.input);
      this.$emit('input', this.input);
    },
  },
  mounted() {
    if (this.value === null) {
      this.input = '';
    } else {
      this.input = this.value;
    }
    this.transformed = this.copy(this.input);
  }
};
</script>

<style lang="scss" scoped>
  @import '../../../../sass/colors';
  
  $disabledBackground: lighten($secondary, 20%);
  
  .btn:disabled,
  .btn.disabled {
    background: $disabledBackground;
    border-color: $disabledBackground;
    opacity: 1 !important;
  }
</style>