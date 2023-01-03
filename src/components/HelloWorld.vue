<template>
  <div class="main-container bg-[#222222] ">
    <div class="loader" v-if="isLoading">
      <img src="../assets/loader.gif" alt="">
    </div>
    <div class="flex flex-wrap flex-row justify-center" v-if="data && !isLoading">
      <div class="w-full flex items-center justify-center">
        <img src="../assets/logo1.png" style="width: 250px;" alt="">
        <p class="self-end mb-5 text-green-500 font-bold">wiki</p>
      </div>
      <div class="w-full flex justify-center items-center border-red-500">
        <label class="relative block">
          <span class="absolute inset-y-0 left-0 flex items-center pl-2">
            <svg class="h-5 w-5 fill-slate-700" width="24px" height="24" viewBox="0 0 48 48"
              xmlns="http://www.w3.org/2000/svg">

              <path d="M0 0h48v48H0z" fill="none" />
              <g id="Shopicon">
                <path d="M18,32c3.144,0,6.036-1.049,8.373-2.799L40,42.829L42.829,40L29.201,26.373C30.951,24.036,32,21.144,32,18
		c0-7.732-6.268-14-14-14S4,10.268,4,18S10.268,32,18,32z M18,8c5.514,0,10,4.486,10,10c0,5.514-4.486,10-10,10
		c-5.514,0-10-4.486-10-10C8,12.486,12.486,8,18,8z" />
              </g>
            </svg>
          </span>
          <input v-model="searchValue"
            class="placeholder:italic placeholder:text-slate-600 block w-64 bg-white border border-slate-300 rounded-md py-2 pl-9 pr-3 shadow-sm focus:outline-none focus:border-green-500 focus:ring-green-500 focus:ring-1 sm:text-sm"
            placeholder="Search..." type="text" name="search" />
        </label>
        <button type="button"
          class="rounded bg-green-500 ml-3 px-3 py-[6px] hover:bg-green-600 ease-in-out duration-200 cursor-pointer"
          @click="searchClicked(searchValue)">Search</button>
      </div>
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
    <div v-if="!isLoading" class="flex justify-center items-center mt-2 pb-2">
      <vue-awesome-paginate :total-items="data.info.count" :items-per-page="20" :max-pages-shown="5"
        v-model="currentPage" :on-click="pageChanged" />
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
      isLoading: true,
      currentPagee: 1,
      currentPage: 1,
      searchValue: "",
    }
  },
  mounted() {
    document.onreadystatechange = () => {
      if (document.readyState == "complete") {
        setTimeout(() => {
          this.isLoading = false;
        }, 2500);

      }
    }
  },
  created() {
    this.getData()
  },
  methods: {
    pageChanged(page) {
      // TODO : PAGİNATION ONLY WORKS FOR 1 FORWARD AND 1 BACKWARD. LOOK AT THE DIFFERENCE AND MAKE A REQUEST ACCORDING TO IT
      if (this.currentPagee < page) {
        axios.get(this.data.info.next).then(x => {
          console.log(x)
          this.currentPagee = page
          this.data = x.data;
          this.characters = x.data.results
          setTimeout(() => {
            this.isLoading = false;
          }, 1000);
        })
      }
      else if (this.currentPagee > page) {
        axios.get(this.data.info.prev).then(x => {
          this.currentPagee = page
          this.data = x.data;
          this.characters = x.data.results
          setTimeout(() => {
            this.isLoading = false;
          }, 1000);
        })
      }
      else {
        console.log("Page", page, "current page : ", this.currentPagee)
        console.log("zortladın knk")
      }

    },
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
    },
    searchClicked(value) {
      let trimmedVal = encodeURIComponent(value.trim())
      axios.get('https://rickandmortyapi.com/api/character/?name=' + trimmedVal).then(x => {
        console.log(x)
        this.data = x.data;
        this.characters = x.data.results
      })
    }

  }
}
</script>
<style scoped>
.loader {
  height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #222222;
}
</style>
<style>
.pagination-container {
  display: flex;
  column-gap: 10px;
}

.paginate-buttons {
  height: 40px;
  width: 40px;
  border-radius: 3px;
  cursor: pointer;
  background-color: rgb(31, 31, 31);
  border: 1px solid rgb(48, 48, 48);
  color: white;
}

.paginate-buttons:hover {
  background-color: #71d68fd4;
}

.active-page {
  background-color: #27e05fd4;
  border: 1px solid #27e05fd4;
  color: white;
}

.active-page:hover {
  background-color: #2988c8;
}

@media screen and (max-width:480px) {
  .pagination-container {
    width: 90vw;
    align-items: center;
    justify-content: center;
    column-gap: 2px;
  }
}
</style>