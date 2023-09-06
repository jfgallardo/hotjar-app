<template>
  <div class="greetings">
    <h1 class="green">{{ msg }}</h1>
    <h3>You’ve successfully created a project with</h3>
    <button
      class="bg-blue-600 text-white font-semibold px-4 py-2 mt-4"
      @click="showModal = true"
    >
      Feedback
    </button>
  </div>

  <ModalComment v-if="showModal" @close="showModal = false">
    <template #header> Dê seu feedback em nosso site </template>
    <template #body>
      <div class="space-y-12 p-4">
        <div class="border-b border-gray-900/10 pb-12">
          <div class="grid grid-cols-1 gap-x-6 gap-y-8 sm:grid-cols-6">
            <div class="sm:col-span-4">
              <label class="block text-sm font-medium leading-6 text-gray-900"
                >Usuario</label
              >
              <div class="mt-2">
                <div
                  class="flex rounded-md shadow-sm ring-1 ring-inset ring-gray-300 focus-within:ring-2 focus-within:ring-inset focus-within:ring-indigo-600 sm:max-w-md"
                >
                  <input
                    type="text"
                    class="block flex-1 border-0 bg-transparent py-1.5 pl-2 text-gray-900 placeholder:text-gray-400 focus:ring-0 sm:text-sm sm:leading-6"
                    placeholder="Usuario"
                    v-model="form.name"
                  />
                </div>
              </div>
            </div>

            <div class="sm:col-span-4">
              <label
                for="username"
                class="block text-sm font-medium leading-6 text-gray-900"
                >Email</label
              >
              <div class="mt-2">
                <div
                  class="flex rounded-md shadow-sm ring-1 ring-inset ring-gray-300 focus-within:ring-2 focus-within:ring-inset focus-within:ring-indigo-600 sm:max-w-md"
                >
                  <input
                    type="text"
                    class="block flex-1 border-0 bg-transparent py-1.5 pl-2 text-gray-900 placeholder:text-gray-400 focus:ring-0 sm:text-sm sm:leading-6"
                    placeholder="Email"
                    v-model="form.email"
                  />
                </div>
              </div>
            </div>

            <div class="col-span-full">
              <label
                for="about"
                class="block text-sm font-medium leading-6 text-gray-900"
                >Comment</label
              >
              <div class="mt-2">
                <textarea
                  v-model="form.comment"
                  id="about"
                  name="about"
                  rows="3"
                  class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
                ></textarea>
              </div>
              <p class="mt-3 text-sm leading-6 text-gray-600">
                Escreva o que quiser sobre nosso site.
              </p>
            </div>

            <div class="col-span-full">
              <div class="mt-2 flex items-center gap-x-3">
                <button
                  @click="takePicture"
                  type="button"
                  class="rounded-md bg-white mb-3 px-2.5 py-1.5 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                >
                  ScreenS
                </button>
              </div>
              <div id="showCanvas"></div>
            </div>
          </div>
        </div>
      </div>

      <div class="mt-6 flex items-center justify-end gap-x-6 p-4">
        <button
          @click="showModal = false"
          type="button"
          class="text-sm font-semibold leading-6 text-gray-900"
        >
          Cancel
        </button>
        <button
          :disabled="disabledSave"
          @click="save"
          class="disabled:bg-indigo-200 disabled:cursor-not-allowed rounded-md bg-indigo-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
        >
          Save
        </button>
      </div>
    </template>
  </ModalComment>
</template>

<script setup>
import { ref, computed } from "vue";
import ModalComment from "./ModalComment.vue";
import html2canvas from "html2canvas";
import axiosClient from '@/plugins/axios';

defineProps({
  msg: {
    type: String,
    required: true,
  },
});

const showModal = ref(false);
const form = ref({});

const disabledSave = computed(() => {
  return (
    !form.value.name ||
    !form.value.email ||
    !form.value.comment ||
    !form.value.screenshot
  );
});

const save = () => {
  axiosClient.post("/comments", form.value).then(() => {
    showModal.value = false;
    alert("Comentário salvo com sucesso!");
  });
};

const takePicture = () => {
  const showCanvasDiv = document.getElementById("showCanvas");
  const modal = document.getElementById("modalback");

  modal.style.display = "none";

  const resizeObserver = new ResizeObserver(() => {});
  resizeObserver.observe(showCanvasDiv);

  html2canvas(document.body, {
    windowWidth: "1024",
    windowHeight: "768",
  }).then(function (canvas) {
    showCanvasDiv.innerHTML = "";
    showCanvasDiv.appendChild(canvas);

    modal.style.display = "flex";
    form.value.screenshot = canvas.toDataURL("image/jpeg", 0);
    resizeObserver.disconnect();
  });
};
</script>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
