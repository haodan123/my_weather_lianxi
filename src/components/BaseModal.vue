<template>
  <!-- 弹窗组件 -->
  <!-- Teleport是吧这个组件放到body上 -->
  <Teleport to="body">
    <Transition name="modal-outer">
      <div v-show="modalActive"
        class=" absolute w-full h-screen bg-black bg-opacity-30 top-0 left-0 flex justify-center px-8">
        <Transition name="modal-inner">
          <div v-if="modalActive" class=" p-4 bg-white self-start mt-32 max-w-screen-md">
            <slot></slot>

            <button @click="$emit('close-modal')" class=" text-white py-2 px-6 bg-weather-primary"> 关闭</button>

          </div>
        </Transition>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
  defineEmits(['close-modal'])
  defineProps({
    modalActive: {
      type: Boolean,
      default: false
    }
  })
</script>

<style scoped>
  .modal-outer-enter-active {
    transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02) 0.15s;

  }

  .modal-outer-leave-active {
    transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
  }

  .modal-outer-enter-from {
    opacity: 0;
  }

  .modal-outer-leave-to {
    opacity: 0;
  }


  .modal-inner-enter-active {
    transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02) 0.15s;

  }

  .modal-inner-leave-active {
    transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
  }

  .modal-inner-enter-from {
    opacity: 0;
    transform: scale(0.8);
  }

  .modal-inner-leave-to {
    /* opacity: 1; */
    transform: scale(0.8);

  }
</style>