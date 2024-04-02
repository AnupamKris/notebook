<template>
  <!-- <div class="tiptap-wrapper"> -->
  <tip-tap-toolbar :editor="editor" :exportToPdf />
  <editor-content :editor="editor" class="tiptap" id="tiptap" />
  <!-- </div> -->
</template>

<script setup>
import { useEditor, EditorContent } from "@tiptap/vue-3";
import Document from "@tiptap/extension-document";
import Paragraph from "@tiptap/extension-paragraph";
import Text from "@tiptap/extension-text";
import Bold from "@tiptap/extension-bold";
import Italic from "@tiptap/extension-italic";
import Underline from "@tiptap/extension-underline";
import Code from "@tiptap/extension-code";
import CodeBlockLowlight from "@tiptap/extension-code-block-lowlight";
import { createLowlight, all } from "lowlight";
import Placeholder from "@tiptap/extension-placeholder";
import css from "highlight.js/lib/languages/css";
import javascript from "highlight.js/lib/languages/javascript";
import json from "highlight.js/lib/languages/json";
import xml from "highlight.js/lib/languages/xml";
import bash from "highlight.js/lib/languages/bash";
import ts from "highlight.js/lib/languages/typescript";
import python from "highlight.js/lib/languages/python";
import { ref, watch, onMounted, onBeforeUnmount, defineProps, defineEmits } from 'vue'
import html2pdf from "html2pdf.js";

const lowlight = createLowlight();

lowlight.register(all);

const props = defineProps(["modelValue", "title", "exportToPdf"]);

const emits = defineEmits(['update:modelValue'])

const editor = useEditor({
  content: props.modelValue,

  onUpdate: () => {
    emits('update:modelValue', editor.value.getHTML())
  },
  extensions: [
    Document,
    Paragraph,
    Text,
    Bold,
    Italic,
    Underline,
    Placeholder.configure({
      placeholder: 'Start typing...',
    }),
    Code.configure({
      HTMLAttributes: {
        class: "code",
      },
    }),
    CodeBlockLowlight.configure({
      lowlight: lowlight,
      defaultLanguage: "javascript",
      exitOnArrowDown: true,
      HTMLAttributes: {
        class: "code-block",
      },
    }),
  ],
})

// const exportToPdf = () => {
//   var opt = {
//     margin: 1,
//     filename: 'myfile.pdf',
//     image: { type: 'jpeg', quality: 0.98 },
//     html2canvas: { scale: 2 },
//     jsPDF: { unit: 'in', format: 'letter', orientation: 'portrait' }
//   };

//   let htmlString = editor.value.getHTML();
//   let formatted = `
//   <head>
//     <link
//       href="https://cdn.jsdelivr.net/npm/highlight.js@11.9.0/styles/atom-one-dark.min.css" 
//       rel="stylesheet"
//     />
//   </head>
//   <style> 
//   p:empty::before {
//     content: '';
//     display: inline-block;
//   } 
//   * {
//     text-align:justify;
//   }
//   </style>
//   <h1> ${props.title} </h1>
//   ${htmlString}`
//   console.log(formatted);
//   let pdf = html2pdf().set(opt).from(formatted).toPdf();
//   pdf.save(props.title + ".pdf");
// };

watch(() => props.modelValue, (value) => {
  const isSame = editor.value.getHTML() === value

  if (isSame) {
    return
  }

  editor.value.commands.setContent(value, false)
})


onBeforeUnmount(() => {
  editor.value.destroy()
})
</script>

<style lang="scss" scoped></style>
