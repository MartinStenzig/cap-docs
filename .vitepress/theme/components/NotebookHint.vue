<template>
    <span class="notebook" v-if="supportsNotebook">
      <div class="tip tip-notebook custom-block">
        <p class="custom-block-title">
          This guide is available as a CAP Notebook.
          <text class="notebook-test-msg">
            <text class="sap-icons" title="This guide is automatically tested and verified">&#xe304;</text>
          </text>
        </p>
        <p>
          <a class="link nb-download" @click="linkNotebook($event)">Download the notebook</a> to run it locally in your VS Code editor.
        </p>
      </div>
    </span>
</template>


<script setup>

import { computed } from 'vue'
import { useData } from 'vitepress'

const { frontmatter, site } = useData()

const supportsNotebook = computed(() => !!frontmatter.value['notebook'])

function linkNotebook($event) {
  return typeof localStorage !== 'undefined' ? useNotebook($event) : () => {}
}

function useNotebook(event) {
  function getNotebook() {
    const baseUrl = new URL(window.location);
    const urlParams = new URLSearchParams(window.location.search)
    const implVariant = urlParams.get('impl-variant')
    let base = site.value.base;
    const pathname = base ? baseUrl.pathname.split(base)[1] : baseUrl.pathname;
    const language = implVariant ? implVariant : localStorage['impl-variant'];
    const capnbFile = language ? `${pathname.replace(/\//g, '-')}-${language}.capnb`: `${pathname.replace(/\//g, '-')}.capnb`;
    const notebookUrl = `${baseUrl.origin}${base}notebooks/${capnbFile}`;
    const link = document.createElement('a');
    link.href = notebookUrl;
    link.download = capnbFile;
    link.click();
    URL.revokeObjectURL(link.href);
  }
  return getNotebook(event)
}

</script>

<style scoped>
@font-face {
  font-family: 'SAP-icons';
  src: url('SAP-icons.woff2') format('woff2');
  font-weight: normal;
  font-style: normal;
}
.sap-icons {
  font-family: 'SAP-icons' !important;
  font-size: 15px;
}
.notebook-test-msg {
  padding-left: 10px;
  color: var(--vp-c-text-2);
}
</style>