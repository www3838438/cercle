<template>
  <modal :large="largeSize" :small="smallSize" :show="open" v-bind:class="[windowClass]" :backdrop=false>
    <div slot="modal-header" class="modal-header" v-if="!!options.display_header">
      {{options.title}}
      <button type="button" class="close" @click="close()" v-if="options.closed_in_header">
        <span>&times;</span>
      </button>
    </div>
    <span slot="modal-header" v-if="!options.display_header"></span>
    <div slot="modal-body" class="modal-body">
      <button type="button" class="close" @click="close()"  v-if="!options.closed_in_header" style="padding:6px;">
        <span>&times;</span>
      </button>
      <component keep-alive v-bind:is="view" v-bind="modalData" v-on:close="close()" ref="view">
      </component>
    </div>
    <span slot="modal-footer"></span>
  </modal>

</template>
<script>
import ContactShow from '../contacts/show.vue';
import CardShow from '../cards/show.vue';
import NewContact from '../contacts/new.vue';
import NewCard from '../cards/new.vue';
export default {
  data() {
    return {
      windowClass: '',
      open: false,
      modalData: {},
      view: null,
      options: {}
    };
  },
  methods: {
    sizeModal() { return this.options.size || 'large'; },
    close() {
      let vm = this;
      vm.open = false;
      vm.$glmodal.$emit('onCloseModal');
      Vue.nextTick(function(){
        if (vm.$refs.view) {
          vm.$refs.view.$emit('onClose');
        }
      });
    }

  },
  computed: {
    largeSize: function() { return this.sizeModal() === 'large'; },
    smallSize: function() { return this.sizeModal() === 'small'; }

  },
  components: {
    'modal': VueStrap.modal,
    'contact-show': ContactShow,
    'card-show': CardShow,
    'new-contact-form': NewContact,
    'new-card-form': NewCard
  },
  mounted() {
    let vm = this;
    vm.$glmodal.$on('open', function(options){
      vm.view = options['view'];
      vm.modalData = options['data'];
      vm.windowClass = options['class'];
      vm.options = options;
      vm.open = true;

      Vue.nextTick(function(){
        vm.$refs.view.$emit('onOpen');
      });
    });
    vm.$root.$on('esc-keyup', () => { vm.close(); });
    vm.$glmodal.$on('close', function(options){
      vm.close();
    });
  }
};
</script>

<style lang="sass">

</style>
