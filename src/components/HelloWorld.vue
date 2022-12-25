<template>
  <div class="main-container bg-slate-900 ">
    <div class="flex flex-wrap flex-row justify-center" v-if="data">
      <div v-for="char in characters" :key="char.id"
        class="mx-5 my-5 bg-slate-700 border-2 rounded border-solid group border-green-500 drop-shadow-lg ease-in-out duration-200 hover:drop-shadow-[0_35px_35px_rgba(6,214,160,0.55)] cursor-pointer"
        @click='characterDetail(char)'>
        <div class="container p-4">
          <div>
            <img class="rounded" :src="char.image" alt="">
          </div>
          <div class="my-4">
            <div class="text-xl font-extrabold ease-in-out duration-200 group-hover:text-green-500 text-white">
              {{ char.name }} </div>
            <p class="text-slate-300"> {{ char.status }} - {{ char.species }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
  <template>
    <TransitionRoot appear :show="isCharacterModalVisible" as="template">
      <Dialog @close="!isCharacterModalVisible" class="relative z-50">
        <TransitionChild as="template" enter="duration-300 ease-out" enter-from="opacity-0" enter-to="opacity-100"
          leave="duration-200 ease-in" leave-from="opacity-100" leave-to="opacity-0">
          <div class="fixed inset-0 bg-black bg-opacity-75" />
        </TransitionChild>
        <TransitionChild as="template" enter="duration-300 ease-out" enter-from="opacity-0 scale-95"
          enter-to="opacity-100 scale-100" leave="duration-200 ease-in" leave-from="opacity-100 scale-100"
          leave-to="opacity-0 scale-95">
          <div class="fixed inset-0 flex items-center justify-center w-full p-4">
            <DialogPanel
              class="w-full max-w-xl transform overflow-hidden rounded-2xl bg-white p-6 text-left align-middle shadow-xl transition-all">
              <DialogTitle class="text-lg font-extrabold leading-6 text-gray-900 mt-0 my-2">
                {{ this.characterInformation.name }}</DialogTitle>
              <div class="flex">
                <DialogDescription class="mr-4 mb-4">
                  <img class="rounded max-w-[200px] " :src="this.characterInformation.image" alt="">
                </DialogDescription>
                <div>
                  <div class="flex">
                    <p> <span class="font-semibold">Gender : </span>{{ this.characterInformation.gender }}</p>
                  </div>
                  <div>
                    <p> <span class="font-semibold">Last Seen : </span>{{ this.characterInformation.location.name }}</p>
                  </div>
                  <div>
                    <p> <span class="font-semibold">Originally From : </span>{{ this.characterInformation.origin.name }}
                    </p>
                  </div>
                  <div>
                    <p>Seen on {{ this.characterInformation.episode.length }} episodes.</p>
                  </div>
                </div>
              </div>
              <button type="button"
                class="inline-flex justify-center rounded-md border border-transparent bg-green-100 px-4 py-2 text-sm font-medium text-green-900 hover:bg-green-200 focus:outline-none focus-visible:ring-2 focus-visible:ring-green-500 focus-visible:ring-offset-2"
                @click="isCharacterModalVisible = false">Close</button>
            </DialogPanel>
          </div>
        </TransitionChild>
      </Dialog>
    </TransitionRoot>
  </template>
</template>

<script>
import axios from 'axios'
import {
  Dialog,
  DialogPanel,
  DialogTitle,
  DialogDescription,
  TransitionRoot,
  TransitionChild,
} from '@headlessui/vue'
export default {
  name: 'HelloWorld',
  components: {
    Dialog,
    DialogPanel,
    DialogTitle,
    DialogDescription,
    TransitionRoot,
    TransitionChild,
  },
  data() {
    return {
      data: null,
      characters: null,
      isCharacterModalVisible: false,
      characterInformation: null,
    }
  },
  created() {
    this.getData()
  },
  methods: {
    getData() {
      axios.get('https://rickandmortyapi.com/api/character').then(x => {
        console.log(x)
        this.data = x.data;
        this.characters = x.data.results
      })
    },
    characterDetail(char) {
      this.characterInformation = char;
      this.isCharacterModalVisible = true;
    }

  }
}
</script>
<style scoped>

</style>
