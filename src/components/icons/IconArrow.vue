<template>
  <div class="circle">
    <svg xmlns="http://www.w3.org/2000/svg" width="46" height="44" viewBox="0 0 46 44">
      <g fill="none" stroke="#FFF" stroke-width="4">
        <path d="M1 22.019C8.333 21.686 23 25.616 23 44M23 44V0M45 22.019C37.667 21.686 23 25.616 23 44" />
      </g>
    </svg>
  </div>
</template>

<style scoped lang="scss">
.circle {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background-color: var(--purple);
  display: flex;
  justify-content: center;
  align-items: center;

  svg {
    width: 25px;
    height: 25px;
  }
}
</style>