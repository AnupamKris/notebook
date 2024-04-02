<template>
  <div class="toolbar" v-if="editor">
    <button @click="editor.chain().focus().toggleBold().run()"
      :disabled="!editor.can().chain().focus().toggleBold().run()" :class="{ 'is-active': editor.isActive('bold') }">
      <span class="material-symbols-outlined"> format_bold </span>
    </button>
    <button @click="editor.chain().focus().toggleUnderline().run()"
      :class="{ 'is-active': editor.isActive('underline') }">
      <span class="material-symbols-outlined"> format_underlined </span>
    </button>
    <button @click="editor.chain().focus().toggleItalic().run()" :class="{ 'is-active': editor.isActive('italic') }">
      <span class="material-symbols-outlined"> format_italic </span>
    </button>
    <button @click="editor.chain().focus().toggleCodeBlock().run()"
      :class="{ 'is-active': editor.isActive('codeBlock') }">
      <span class="material-symbols-outlined"> code_blocks </span>
    </button>
    <button @click="editor.chain().focus().toggleCode().run()" :class="{ 'is-active': editor.isActive('code') }">
      <span class="material-symbols-outlined"> code </span>
    </button>

    <button @click="exportToPdf">
      <span class="material-symbols-outlined"> picture_as_pdf </span>
    </button>
  </div>
</template>

<script setup>



const props = defineProps({
  editor: Object,
  exportToPdf: Function,
});

const getText = () => {
  console.log("riunng");
  console.log(props.editor.getHTML());
};


</script>

<style lang="scss" scoped>
.toolbar {
  position: fixed;

  top: 50%;
  right: 0;

  z-index: 100;

  transform: translateY(-50%);

  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 10px 5px;

  background: var(--color-background-tertiary);

  border-top: 1px solid var(--color-border);
  border-bottom: 1px solid var(--color-border);
  border-left: 1px solid var(--color-border);

  border-radius: 10px 0 0 10px;

  transition: 0.3s;

  button {
    display: flex;
    justify-content: center;
    align-items: center;

    background: transparent;
    outline: none;
    border: none;

    color: var(--color-text);
    border-radius: 5px;
    padding: 5px;
    transition: 0.3s;

    &:hover {
      background: var(--color-background-hover);
    }
  }

  .is-active {
    background: var(--color-primary);
    color: var(--color-background);
  }
}
</style>
