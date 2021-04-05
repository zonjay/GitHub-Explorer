<template>
  <span :style="{ height, width }" class="skelenton-box" />
</template>

<script>
export default {
  name: "SkeletonBox",
  props: {
    height: {
      default: "1em",
      type: String,
    },
    width: {
      default: null,
      type: String,
    },
  },
};
</script>

<style lang="scss">
.skelenton-box {
  position: relative;
  display: inline-block;
  overflow: hidden;
  vertical-align: middle;
  background-color: rgba(221, 219, 221, 0.4);
  border-radius: 0.75rem;

  @media all and (max-width: 951px) {
    width: 100% !important;
  }
  
  &::after {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    content: '';
    background-image: linear-gradient(
      90deg,
      rgba(#fff, 0) 0,
      rgba(#fff, 0.2) 20%,
      rgba(#fff, 0.5) 60%,
      rgba(#fff, 0)
    );
    transform: translateX(-100%);
    animation: loading 2s infinite;
  }
}
@keyframes loading {
  100% {
    transform: translateX(100%);
  }
}
</style>