<script setup lang="ts">
import {Menubar, MenubarMenu, MenubarTrigger} from '@/components/ui/menubar';

const openHelp = () => {
  window.open('https://github.com/bekirbostanci/vda5050_validator', '_blank');
};
</script>

<template>
  <Menubar>
    <div class="flex items-center">
      <img src="/vda.svg" alt="logo" class="w-7 h-7" />
    </div>
    <MenubarMenu>
      <MenubarTrigger>Open</MenubarTrigger>
    </MenubarMenu>
    <MenubarMenu>
      <MenubarTrigger>Validate</MenubarTrigger>
    </MenubarMenu>
    <MenubarMenu>
      <MenubarTrigger @click="openHelp">Help</MenubarTrigger>
    </MenubarMenu>
  </Menubar>
</template>
<style scoped>
#top-bar {
  background-color: white;
  z-index: 10;
}

@media (min-width: 768px) {
  .hide-on-desktop {
    display: none;
  }
}

@media (max-width: 767px) {
  .hide-on-mobile {
    display: none;
  }
}
</style>
