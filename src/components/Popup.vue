<script>
  export default {
    name: 'Popup',
    props: ['del','ind'],
    methods: {
      close() {
        this.$emit('close');
      },
    },
  };
</script>

<template>
  <transition name="modal-fade">
    <div class="modal-backdrop">
      <div class="modal"
        role="dialog"
        aria-labelledby="modalTitle"
        aria-describedby="modalDescription"
      >
        <header
          class="modal-header"
          id="modalTitle"
        >
          <slot name="header">
            Удалить пользователя
          </slot>
          <button
            type="button"
            class="btn-close"
            @click="close"
            aria-label="Close modal"
          >
            x
          </button>
        </header>

        <section
          class="modal-body"
          id="modalDescription"
        >
          <slot name="body">
            Вы действительно хотите удалить пользователя?
          </slot>
        </section>

        <footer class="modal-footer">
          <button
            type="button"
            class="btn-submit"
            @click="del(ind); close()"
            aria-label="Close modal"
          >
            Да
          </button>
          <button
            type="button"
            class="btn-submit"
            @click="close"
            aria-label="Close modal"
          >
            Нет
          </button>
        </footer>
      </div>
    </div>
  </transition>
</template>

<style>
  .modal-backdrop {
    font-family: 'Zona Pro';
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: rgba(0, 0, 0, 0.3);
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .modal {
    background: #FFFFFF;
    box-shadow: 2px 2px 20px 1px;
    overflow-x: auto;
    display: flex;
    flex-direction: column;
  }

  .modal-header,
  .modal-footer {
    padding: 15px;
    font-family: 'Zona Pro';
    display: flex;
  }

  .modal-header {
    position: relative;
    border-bottom: 1px solid #eeeeee;
    color: #4AAE9B;
    justify-content: space-between;
  }

  .modal-footer {
    border-top: 1px solid #eeeeee;
    flex-direction: row;
    justify-content: space-between;
  }

  .modal-body {
    position: relative;
    padding: 20px 10px;
  }

  .btn-close {
    position: absolute;
    top: 6px;
    right: 10px;
    border: none;
    font-size: 20px;
    padding: 5px;
    cursor: pointer;
    font-weight: bold;
    color: #4AAE9B;
    background: transparent;
  }

  .btn-submit {
    color: white;
    font-family: 'Zona Pro';
    padding: 10px;
    width: 20%;
    cursor: pointer;
    border: none;
    border-radius: 10px;
  }

  .btn-submit:nth-child(1) { 
    background-color: rgb(19, 134, 34);
  }

  .btn-submit:nth-child(2) { 
    background-color: rgb(211, 65, 65);
  }

  .modal-fade-enter,
  .modal-fade-leave-to {
    opacity: 0;
  }

  .modal-fade-enter-active,
  .modal-fade-leave-active {
    transition: opacity .2s ease;
  }
</style>