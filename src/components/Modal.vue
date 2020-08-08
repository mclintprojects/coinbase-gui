<template>
  <div v-if="show" class="modal-background">
    <div class="modal-content">
      <div class="modal-header">
        <div>
          <h2>{{title}}</h2>
          <label>{{subtitle}}</label>
        </div>
        <button class="modal-header__close" @click="$emit('close')"><font-awesome-icon icon="times" /></button>
      </div>
      <slot></slot>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    show: {
      type: Boolean,
      default: false
    },
    title: {
      type: String,
      required: true
    },
    subtitle: {
      type: String,
      required: true
    }
  }
}
</script>

<style lang="scss" scoped>
  .modal{
    &-background{
      position: absolute;
      top: 0;
      background: rgba(0, 0, 0, 0.4);
      z-index: 1000;
      width: 100vw;
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    &-content{
      background: white;
      padding: 2.4rem;
      border-radius: 1.6rem;
      min-width: 35%;
    }

    &-header{
      padding-bottom: 1.6rem;
      margin-bottom: 3rem;
      border-bottom: 0.1rem solid rgb(231, 231, 231);
      display: flex;
      justify-content: space-between;
      align-items: center;

      &__close{
        background: transparent;
        border: 0;
        background: #eee;
        border-radius: 50%;
        height: 3rem;
        width: 3rem;

        &:hover{
          cursor: pointer;
        }
      }
    }
  }

  @media screen and (max-width: 480px){
    .modal-content{
      width: 90%;
      padding: 1.6rem;
    }
  }
</style>