<template>
  <modal-inner class="modal__inner-1--google-photo" aria-label="Import Google Photo">
    <div class="modal__content">
      <div class="google-photo__tumbnail" :style="{backgroundImage: thumbnailUrl}"></div>
      <form-entry label="Title (optional)">
        <input slot="field" class="textfield" type="text" v-model.trim="title" @keydown.enter="resolve()">
      </form-entry>
      <form-entry label="Size limit (optional)">
        <input slot="field" class="textfield" type="text" v-model.trim="size" @keydown.enter="resolve()">
      </form-entry>
    </div>
    <div class="modal__button-bar">
      <button class="button" @click="reject()">Cancel</button>
      <button class="button" @click="resolve()">Ok</button>
    </div>
  </modal-inner>
</template>

<script>
import { mapGetters } from 'vuex';
import ModalInner from '../common/ModalInner';
import FormEntry from '../common/FormEntry';

const makeThumbnail = (url, size) => `${url}=s${size}`;

export default {
  components: {
    ModalInner,
    FormEntry,
  },
  data: () => ({
    title: '',
    size: '',
  }),
  computed: {
    thumbnailUrl() {
      return `url(${makeThumbnail(this.config.url, 320)})`;
    },
    ...mapGetters('modal', [
      'config',
    ]),
  },
  methods: {
    resolve() {
      let url = this.config.url;
      const size = parseInt(this.size, 10);
      if (!isNaN(size)) {
        url = makeThumbnail(url, size);
      }
      if (this.title) {
        url += ` "${this.title}"`;
      }
      const callback = this.config.callback;
      this.config.resolve();
      callback(url);
    },
    reject() {
      const callback = this.config.callback;
      this.config.reject();
      callback(null);
    },
  },
};
</script>

<style lang="scss">
.google-photo__tumbnail {
  height: 160px;
  background-position: center;
  background-repeat: no-repeat;
  background-size: contain;
}
</style>
