<script setup lang="ts">
import { useRouter } from "vue-router";
import { userStore } from "../stores/user";
import { codeStore } from "../stores/code";
import { storeToRefs } from "pinia";

defineProps<{
  showMyCodeDialog: boolean;
}>();
const emit = defineEmits<{
  (event: "closeDialog"): void;
}>();
const closeDialog = (): void => {
  emit("closeDialog");
};
const user = userStore();
const { getName } = storeToRefs(user);
const code = codeStore();
const { getSampleCode } = storeToRefs(code);
const router = useRouter();
const goToGamePage = () => router.push("/game");
</script>

<template>
  <teleport to="body">
    <transition
      enter-active-class="transition ease-out duration-200 transform"
      enter-from-class="opacity-0"
      enter-to-class="opacity-100"
      leave-active-class="transition ease-in duration-200 transform"
      leave-from-class="opacity-100"
      leave-to-class="opacity-0"
    >
      <div
        ref="modal-backdrop"
        class="fixed z-10 inset-0 overflow-y-auto bg-black bg-opacity-50"
        v-show="showMyCodeDialog"
      >
        <div
          class="flex items-start justify-center min-h-screen pt-24 text-center"
        >
          <div
            class="modal-content"
            role="dialog"
            ref="modal"
            aria-modal="true"
            aria-labelledby="modal-headline"
          >
            <div
              class="modal-header flex flex-shrink-0 items-center justify-center p-4 border-b border-gray-200 rounded-t-md"
            >
              <p class="text-2xl font-medium">練習ソースコード</p>
            </div>
            <div
              class="modal-body relative flex flex-col justify-around items-center h-72 px-4 pb-4"
            >
              <div class="flex relative w-10/12 max-w-sm">
                <span class="menu-item">UserName</span>
                <div type="text" class="menu-content">
                  <p>{{ getName }}</p>
                </div>
              </div>
              <div
                class="flex relative mt-4 w-10/12 max-w-2xl rounded-md border overflow-y-scroll h-60"
              >
                {{ getSampleCode }}
              </div>
            </div>
            <div
              class="modal-footer flex flex-shrink-0 flex-wrap items-center justify-between py-4 px-8 border-t border-gray-200 rounded-b-md"
            >
              <button
                @click="closeDialog()"
                type="button"
                class="btn bg-purple-600"
                data-bs-dismiss="modal"
              >
                Close
              </button>
              <button
                type="button"
                class="btn bg-blue-600"
                @click="
                  code.setSampleCode();
                  goToGamePage();
                "
              >
                start
              </button>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </teleport>
</template>

<style scoped>
.btn {
  @apply inline-block px-6 py-2.5 text-white font-medium text-xs leading-tight uppercase rounded shadow-md;
}
.menu-item {
  @apply rounded-l-md inline-flex items-center px-3 border-t bg-white border-l border-b border-gray-300 text-gray-500 shadow-sm text-sm w-24;
}
.menu-content {
  @apply rounded-r-lg flex-1 border border-gray-300 w-full py-2 px-4 bg-white text-gray-700 shadow-sm text-base;
}
.modal-content {
  @apply border-none shadow-lg relative flex flex-col justify-around w-10/12 max-w-3xl h-[600px] pointer-events-auto bg-white bg-clip-padding rounded-md outline-none text-current;
}
</style>
