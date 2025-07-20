<script setup lang="ts">
import { ref, watch, onMounted } from "vue";
import { Editor, EditorContent } from "@tiptap/vue-3";
import StarterKit from "@tiptap/starter-kit";

const props = defineProps<{
  modelValue: string;
}>();

const emit = defineEmits(["update:modelValue"]);

const editor = ref<Editor>();

onMounted(() => {
  editor.value = new Editor({
    content: props.modelValue,
    extensions: [StarterKit],
    onUpdate: ({ editor }) => {
      emit("update:modelValue", editor.getHTML());
    },
  });
});

watch(
  () => props.modelValue,
  (value) => {
    if (editor.value && editor.value.getHTML() !== value) {
      editor.value.commands.setContent(value);
    }
  }
);
</script>

<template>
  <div class="rounded border p-3 min-h-[200px] bg-white focus-within:ring-1 focus-within:ring-blue-500">
    <EditorContent v-if="editor" :editor="editor" class="prose max-w-none" />
  </div>
</template>

<style scoped>
/* Optional: basic styling for editor content */
.prose:focus {
  outline: none;
}
</style>
