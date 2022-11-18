<script setup lang="ts">
import { onMounted, ref } from 'vue'
import 'mathjax/es5/tex-chtml';

const value = ref('x = {-b \\pm \\sqrt{b^2-4ac} \\over 2a}.');
const output = ref<HTMLDivElement | null>(null);

onMounted(() => convert());

function convert() {
      //
      //  Get the TeX input
      //
      const input = value.value.trim();
      //
      //  Disable the display and render buttons until MathJax is done
      //
      // var display = document.getElementById("display");

      //  Clear the old output
      //

      //
      //  Reset the tex labels (and automatic equation numbers, though there aren't any here).
      //  Get the conversion options (metrics and display settings)
      //  Convert the input to CommonHTML output and use a promise to wait for it to be ready
      //    (in case an extension needs to be loaded dynamically).
      //
      if (output.value?.childNodes.length) {
        output.value?.removeChild(output.value.childNodes[0]);
      }
      if (MathJax.texReset) {
        MathJax.texReset();
      }
      var options = MathJax.getMetricsFor(output.value);
      // options.display = display.checked;
      MathJax.tex2chtmlPromise(input, options).then(function (node: HTMLElement) {
        //
        //  The promise returns the typeset node, which we add to the output
        //  Then update the document to include the adjusted CSS for the
        //    content of the new equation.
        //
        output.value?.appendChild(node);
        MathJax.startup.document.clear();
        MathJax.startup.document.updateDocument();
      }).catch(function (err: any) {
        //
        //  If there was an error, put the message into the output instead
        //
        output.value?.appendChild(document.createElement('pre')).appendChild(document.createTextNode(err.message));
      });
    }

</script>

<template>

  <div class="card">
    <input v-model="value"/>
    <button type="button" @click="convert()">Convert</button>
    <div id="output" ref="output"></div>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
