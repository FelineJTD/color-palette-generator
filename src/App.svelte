<script lang="ts">
  const handleGenerate = (e: Event) => {
    e.preventDefault();
    console.log('Generate');
  };
</script>

<main>
  <!-- TITLE -->
  <h1 class="mb-4">Copykitten's Color Palette Generator</h1>
  <!-- DESCRIPTION -->
  <p class="mb-12">Use this website to generate color palette for your illustrations. The calculation is based on <a href="https://www.clipstudio.net/how-to-draw/archives/156922" target="_blank" rel="noopener noreferrer">Ann Maulina&apos;s color palette guide</a>, with modification to fit my own style.</p>
  <!-- MAIN CONTENT -->
  <section class="flex">
    <!-- COLOR INPUT -->
    <form on:submit={handleGenerate}>
      <label for="color" class="block mb-2 text-left text-2xl">Base color</label>
      <input type="color" id="color" class="block mb-8" />
      <label for="temp-shift" class="block mb-2 text-left text-2xl">Temperature (hue) shift</label>
      <input type="range" id="temp-shift" class="block mb-8" />
      <label for="sat-shift" class="block mb-2 text-left text-2xl">Saturation shift</label>
      <input type="range" id="sat-shift" class="block mb-8" />
      <button class="block">Generate</button>
    </form>
    <!-- RESULT -->
    <div></div>
  </section>
</main>

<style>

</style>
