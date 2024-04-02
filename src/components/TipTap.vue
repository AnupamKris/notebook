<template>
  <tip-tap-toolbar :editor />
  <editor-content :editor="editor" class="tiptap" />
</template>

<script setup>
import { useEditor, EditorContent } from "@tiptap/vue-3";
import Document from "@tiptap/extension-document";
import Paragraph from "@tiptap/extension-paragraph";
import Text from "@tiptap/extension-text";
import Bold from "@tiptap/extension-bold";
import Italic from "@tiptap/extension-italic";
import Underline from "@tiptap/extension-underline";
import CodeBlockLowlight from "@tiptap/extension-code-block-lowlight";
import { createLowlight, all } from "lowlight";
import css from "highlight.js/lib/languages/css";
import javascript from "highlight.js/lib/languages/javascript";
import json from "highlight.js/lib/languages/json";
import xml from "highlight.js/lib/languages/xml";
import bash from "highlight.js/lib/languages/bash";
import ts from "highlight.js/lib/languages/typescript";
import python from "highlight.js/lib/languages/python";

const lowlight = createLowlight();

// console.log(lowlight.listLanguages());
lowlight.register({ css, javascript, json, xml, bash, ts, python });

const editor = useEditor({
  content: `<p>I’m running Tiptap with Vue.js. 🎉</p><pre><code class="language-js"></code></pre><pre><code class="language-python">for i in range(10):
    print("Hwllow rodl")</code></pre><p></p><p></p>`,
  extensions: [
    Document,
    Paragraph,
    Text,
    Bold,
    Italic,
    Underline,
    CodeBlockLowlight.configure({
      lowlight: lowlight,
      defaultLanguage: "javascript",
      exitOnArrowDown: true,
      HTMLAttributes: {
        class: "code-block",
      },
    }),
  ],
});
</script>

<style lang="scss" scoped></style>
