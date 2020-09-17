<svelte:options tag="svelte-suggestion-input" immutable={true} />
<!--
     Bootstrap 4: 資源路徑寫死
-->
<script context="module">
 const component_range = 4096;
 function formatSequence(number, n, paddingSymbol) {
     var len = n - (''+number).length;
     var prefix = '';
     if (len > 0) {
         let times = Math.ceil(len/paddingSymbol.length);
         prefix = paddingSymbol.repeat(times);
     }
     return prefix+number;
 }
</script>
<script>
 const serial = formatSequence(Math.ceil(Math.random()*component_range), 6, '0');
 //$: console.log(`initialized number: ${serial}`);
 let className = '';
 export {className as class_name};
 export function addRecord(sku, count) {
     [newSku, newCount] = [sku, count];
     items.push({ text: sku, value: count });
     items = items.slice(0);
 }
 export let items = [];
 let newSku;
 $: if (items.filter(item => item.text === newSku).length > 1)
     console.warn(`Duplicate text '${newSku}' appeared.`);
 let newCount;
 $: if (items.filter(item => item.text === newSku && item.value === newCount).length > 1)
     console.error(`Dupplicate entry { text: "${newSku}", value: "${newCount}" }.`);
 export let inputElement;
 let input;
 $: filtered =
     (input||'') === '' ? [] :
     items.filter(item => item.text.startsWith(input.trim()));
 $: {
     console.log('---');
     for (var i=0; i < items.length; i++)
         console.log(`SKU = ${items[i].text}, Count = ${items[i].value}`);

     if (inputElement !== undefined)
       console.log(`raw input: ${inputElement.innerText}`);
     console.log(`input: ${input}`);
     console.log(`item length: ${items.length}`);
     console.log(filtered);
 }
 function handleKeyDown(event) {
     console.log(`key down: ${event.which}, ${event.code}`);
     const allowedList =
         [8, 32, 35, 45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 65, 66,
          67, 68, 69, 70, 71, 72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83, 84, 85, 86,
          87,88,89, 90, 97, 98, 99, 100, 101, 102, 103, 104, 105, 106, 107,
          108, 109, 110, 111, 112, 113, 114, 115, 116, 117, 118, 119, 120,
          121, 122, 187, 189, 190, 191, 192, 220];
     if (allowedList.filter(n => n === event.which).length === 0) {
         //console.log(`${event.code}: ${event.which}`);
         event.preventDefault();
     }
 }
 function handleKeyUp(event) {
     //console.log(`key up: ${event.which}, ${event.code}`);
     input = inputElement.innerText
 }
</script>
<div class="clearfix">
<div><slot></slot></div>
<div bind:this={inputElement}
     class="svelte-suggestion-input {className}"
     contenteditable="true"
     id="ssi{serial}"
     on:keydown={handleKeyDown}
     on:keyup={handleKeyUp}
></div>
{#if filtered.length > 0 }
  {#each filtered as {text,value}}
    <div value="{value}">{text}</div>
  {/each}
{/if}
</div>
<style>
 @import url(lib/bootstrap/dist/css/bootstrap.min.css);
</style>
