<template>
  <ClientOnly>
    <div class="p-12 max-lg:p-4">
      <GlowBorder
        class="relative flex h-[500px] w-full flex-col items-center justify-center overflow-hidden rounded-lg border bg-background md:shadow-xl"
        :color="['#A07CFE', '#FE8FB5', '#FFBE7B']"
      >
        <span
          class="pointer-events-none whitespace-pre-wrap bg-gradient-to-b from-black to-gray-300/80 bg-clip-text text-center text-7xl font-semibold leading-none text-transparent dark:from-white dark:to-zinc-700/75"
        >
          Glow Border
        </span>
      </GlowBorder>
    </div>
  </ClientOnly>
</template>

<script setup lang="ts">
import { cn } from "@/lib/utils";
import { computed } from "vue";

interface Props {
  borderRadius?: number;
  color?: string | Array<string>;
  borderWidth?: number;
  duration?: number;
  class?: string;
}

const props = withDefaults(defineProps<Props>(), {
  borderRadius: 10,
  color: "#FFF",
  borderWidth: 2,
  duration: 10,
});

const parentStyles = computed(() => {
  return { "--border-radius": `${props.borderRadius}px` };
});

const childStyles = computed(() => ({
  "--border-width": `${props.borderWidth}px`,
  "--border-radius": `${props.borderRadius}px`,
  "--glow-pulse-duration": `${props.duration}s`,
  "--mask-linear-gradient": `linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0)`,
  "--background-radial-gradient": `radial-gradient(circle, transparent, ${
    props.color instanceof Array ? props.color.join(",") : props.color
  }, transparent)`,
}));
</script>

<style scoped>
.glow-border::before {
  animation: glow-pulse var(--glow-pulse-duration) infinite linear;
  background-image: var(--background-radial-gradient);
}

@keyframes glow-pulse {
  0% {
    background-position: 0% 0%;
  }
  50% {
    background-position: 100% 100%;
  }
  100% {
    background-position: 0% 0%;
  }
}
</style>
