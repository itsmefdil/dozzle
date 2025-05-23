<template>
  <ScrollableView :scrollable="scrollable" v-if="stack.name">
    <template #header>
      <div class="mx-2 flex items-center gap-2 md:ml-4">
        <div class="@container flex flex-1 items-center gap-1.5 md:gap-2">
          <ph:stack />
          <div class="font-mono text-sm font-semibold">{{ stack.name }}</div>
          <ContainerDropdown :containers="stack.containers">
            {{ $t("label.container", stack.containers.length) }}
          </ContainerDropdown>
        </div>
        <MultiContainerStat class="ml-auto" :containers="stack.containers" />
        <MultiContainerActionToolbar class="max-md:hidden" @clear="viewer?.clear()" />
      </div>
    </template>
    <template #default>
      <ViewerWithSource
        ref="viewer"
        :stream-source="useStackStream"
        :entity="stack"
        :visible-keys="new Map<string[], boolean>()"
      />
    </template>
  </ScrollableView>
</template>

<script lang="ts" setup>
import { Stack } from "@/models/Stack";
import ViewerWithSource from "@/components/LogViewer/ViewerWithSource.vue";
import { ComponentExposed } from "vue-component-type-helpers";
const { name, scrollable = false } = defineProps<{
  scrollable?: boolean;
  name: string;
}>();

const viewer = ref<ComponentExposed<typeof ViewerWithSource>>();
const store = useSwarmStore();
const { stacks } = storeToRefs(store) as unknown as { stacks: Ref<Stack[]> };
const stack = computed(() => stacks.value.find((s) => s.name === name) ?? new Stack("", [], []));
provideLoggingContext(
  toRef(() => stack.value.containers),
  { showContainerName: true, showHostname: false },
);
</script>
