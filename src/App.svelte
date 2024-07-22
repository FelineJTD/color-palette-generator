<script lang="ts">
  let results: string[] = [];

  const hexToHSL = (hex: string) => {
    let r = 0, g = 0, b = 0;
    // 3 digits
    if (hex.length === 4) {
      r = parseInt(hex[1] + hex[1], 16);
      g = parseInt(hex[2] + hex[2], 16);
      b = parseInt(hex[3] + hex[3], 16);
    }
    // 6 digits
    else if (hex.length === 7) {
      r = parseInt(hex[1] + hex[2], 16);
      g = parseInt(hex[3] + hex[4], 16);
      b = parseInt(hex[5] + hex[6], 16);
    }

    r /= 255;
    g /= 255;
    b /= 255;

    const max = Math.max(r, g, b);
    const min = Math.min(r, g, b);
    let h = (max + min) / 2;
    let s = (max + min) / 2;
    let l = (max + min) / 2;

    if (max === min) {
      h = s = 0;
    } else {
      const d = max - min;
      s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
      switch (max) {
        case r:
          h = (g - b) / d + (g < b ? 6 : 0);
          break;
        case g:
          h = (b - r) / d + 2;
          break;
        case b:
          h = (r - g) / d + 4;
          break;
      }
      h /= 6;
    }

    h = Math.round(h * 360);
    s = Math.round(s * 100);
    l = Math.round(l * 100);

    return [h, s, l];
  };

  const generateOneLinePalette = (color: string, tempShift: number, satShift: number, numColors: number) => {
    const hsl = hexToHSL(color);
    const h = hsl[0];
    const s = hsl[1];
    const l = hsl[2];
    console.log(h, s, l);

    const palette = [];
    // To left side
    for (let i = 0; i < numColors / 2; i++) {
      const newH = h - tempShift * i;
      const newS = s - satShift * i;
      const newL = l - satShift * i;
      palette.push(`hsl(${newH}, ${newS}%, ${newL}%)`);
    }
    // To right side
    for (let i = 1; i < numColors / 2; i++) {
      const newH = h + tempShift * i;
      const newS = s - satShift * i;
      const newL = l + satShift * i;
      palette.push(`hsl(${newH}, ${newS}%, ${newL}%)`);
    }

    return palette;
  };

  const handleGenerate = (e: Event) => {
    e.preventDefault();
    console.log('Generate');

    const color = (document.getElementById('color') as HTMLInputElement).value;
    const type = (document.getElementById('type') as HTMLSelectElement).value;
    const tempShift = (document.getElementById('temp-shift') as HTMLInputElement).value;
    const satShift = (document.getElementById('sat-shift') as HTMLInputElement).value;
    const numColors = (document.getElementById('num-colors') as HTMLInputElement).value;

    console.log(color, type, tempShift, satShift, numColors);

    const palette = generateOneLinePalette(color, parseInt(tempShift), parseInt(satShift), parseInt(numColors));

    console.log(palette);

    results = palette;    
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
      <label for="type" class="block mb-2 text-left text-2xl">Color harmony</label>
      <select id="type" class="block mb-8">
        <option value="monochromatic">Monochromatic</option>
        <option value="analogous">Analogous</option>
        <option value="complementary">Complementary</option>
        <option value="split-complementary">Split complementary</option>
        <option value="triadic">Triadic</option>
        <option value="tetradic">Tetradic</option>
      </select>
      <label for="temp-shift" class="block mb-2 text-left text-2xl">Temperature (hue) shift</label>
      <input type="range" id="temp-shift" class="block mb-8" />
      <label for="sat-shift" class="block mb-2 text-left text-2xl">Saturation shift</label>
      <input type="range" id="sat-shift" class="block mb-8" />
      <label for="num-colors" class="block mb-2 text-left text-2xl">Number of colors</label>
      <input type="number" id="num-colors" class="block mb-8" value={7} />
      <button class="block">Generate</button>
    </form>
    <!-- RESULT -->
    <div>
      {#if results.length > 0}
        <h2 class="mb-4">Result</h2>
        <div class="flex">
          {#each results as result}
            <div class="w-16 h-16 rounded-full" style="background-color: {result}"></div>
          {/each}
        </div>
      {/if}
    </div>
  </section>
</main>

<style>

</style>
