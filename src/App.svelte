<script lang="ts">
  import { onMount } from "svelte";

  let results: string[][] = [];

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

  const generateOneLinePalette = (color: number[], hShift: number, sShift: number, lShift: number, scale: number, numColors: number) => {
    // color: [h, s, l]
    const h = color[0];
    const s = color[1];
    const l = color[2];
    console.log(h, s, l);

    console.log("scale", 1/scale);

    const palette = [];
    // To left side
    for (let i = numColors / 2; i >= 0; i--) {
      const newH = (h - hShift * i + 360) % 360;
      const newS = s - sShift * i;
      const newL = l - lShift * i * (1/scale);
      palette.push(`hsl(${newH}, ${newS}%, ${newL}%)`);
    }
    // To right side
    for (let i = 1; i < numColors / 2; i++) {
      const newH = (h + hShift * i) % 360;
      const newS = s - sShift * i;
      const newL = l + lShift * i * (1/scale);
      palette.push(`hsl(${newH}, ${newS}%, ${newL}%)`);
    }

    return palette;
  };

  const handleGenerate = (e: Event) => {
    e.preventDefault();
    console.log('Generate');

    const color = (document.getElementById('color') as HTMLInputElement).value;
    const type = (document.getElementById('type') as HTMLSelectElement).value;
    const hShift = (document.getElementById('h-shift') as HTMLInputElement).value;
    const sShift = (document.getElementById('s-shift') as HTMLInputElement).value;
    const lShift = (document.getElementById('l-shift') as HTMLInputElement).value;
    const lScale = (document.getElementById('l-scale') as HTMLSelectElement).value;
    const numColors = (document.getElementById('num-colors') as HTMLInputElement).value;

    console.log(color, type, hShift, sShift, lShift, lScale, numColors);

    const hsl = hexToHSL(color);

    let baseColors: number[][] = [];

    if (type === 'monochromatic') {
      baseColors = [hsl];
    } else if (type === 'analogous') {
      // calculate analogous colors based on color
      const left = [(hsl[0] - 30 + 360) % 360, hsl[1], hsl[2]];
      const right = [(hsl[0] + 30) % 360, hsl[1], hsl[2]];
      baseColors = [left, hsl, right];
    } else if (type === 'complementary') {
      // calculate complementary colors based on color
      const complementary = [(hsl[0] + 180) % 360, hsl[1], hsl[2]];
      baseColors = [hsl, complementary];
    } else if (type === 'split-complementary') {
      // calculate split complementary colors based on color
      const left = [(hsl[0] + 180 - 30 + 360) % 360, hsl[1], hsl[2]];
      const right = [(hsl[0] + 180 + 30) % 360, hsl[1], hsl[2]];
      baseColors = [hsl, left, right];
    } else if (type === 'triadic') {
      // calculate triadic colors based on color
      const left = [(hsl[0] - 120 + 360) % 360, hsl[1], hsl[2]];
      const right = [(hsl[0] + 120) % 360, hsl[1], hsl[2]];
      baseColors = [hsl, right, left];
    } else if (type === 'tetradic') {
      // calculate tetradic colors based on color
      const opposite = [(hsl[0] + 180) % 360, hsl[1], hsl[2]];
      const left = [(hsl[0] - 90 + 360) % 360, hsl[1], hsl[2]];
      const right = [(hsl[0] + 90) % 360, hsl[1], hsl[2]];
      baseColors = [hsl, right, opposite, left];
    }

    console.log(baseColors);

    let tempResults: string[][] = [];
    for (const baseColor of baseColors) {
      const palette = generateOneLinePalette(baseColor, parseInt(hShift), parseInt(sShift), parseInt(lShift), parseFloat(lScale), parseInt(numColors));
      tempResults.push(palette);
    }

    results = tempResults;
  };

  onMount(() => {
    handleGenerate(new Event('submit'));
  });
</script>

<main>
  <!-- TITLE -->
  <h1 class="mb-4">Copykitten's Color Palette Generator</h1>
  <!-- DESCRIPTION -->
  <p class="mb-12">Use this website to generate color palette for your illustrations. The calculation is based on <a href="https://www.clipstudio.net/how-to-draw/archives/156922" target="_blank" rel="noopener noreferrer">Ann Maulina&apos;s color palette guide</a>, with modification to fit my own style.</p>
  <!-- MAIN CONTENT -->
  <section class="flex gap-12">
    <!-- COLOR INPUT -->
    <form on:submit={handleGenerate} class="w-1/2">
      <label for="color" class="block mb-2 text-left text-2xl">Base color</label>
      <input type="color" id="color" class="block mb-8" value="#ff0000" on:change={handleGenerate} />
      <label for="type" class="block mb-2 text-left text-2xl">Color harmony</label>
      <select id="type" class="block mb-8" on:change={handleGenerate}>
        <option value="monochromatic" selected>Monochromatic</option>
        <option value="analogous">Analogous</option>
        <option value="complementary">Complementary</option>
        <option value="split-complementary">Split complementary</option>
        <option value="triadic">Triadic</option>
        <option value="tetradic">Tetradic</option>
      </select>
      <label for="h-shift" class="block mb-1 text-left text-2xl">Temperature/hue shift (H)</label>
      <p class="mb-2 opacity-70 text-left">This would depict how harsh the lighting is.</p>
      <input type="range" id="h-shift" class="block mb-8" max="20" value="10" on:change={handleGenerate} />
      <label for="s-shift" class="block mb-1 text-left text-2xl">Saturation shift (S)</label>
      <p class="mb-2 opacity-70 text-left">This would dictate how vibrant your illustration becomes (also depending on base color's saturation).</p>
      <input type="range" id="s-shift" class="block mb-8" max="20" value="10" on:change={handleGenerate} />
      <label for="l-shift" class="block mb-1 text-left text-2xl">Brightness shift (L)</label>
      <p class="mb-2 opacity-70 text-left">This indicates how far apart the colors are from each other.</p>
      <input type="range" id="l-shift" class="block mb-8" max="60" value="20" on:change={handleGenerate} />
      <label for="l-scale" class="block mb-2 text-left text-2xl">Brightness shift scale</label>
      <select id="l-scale" class="block mb-8" on:change={handleGenerate}>
        <option value="1.067">1.067 - Minor Second</option>
        <option value="1.125">1.125 - Major Second</option>
        <option value="1.200">1.200 - Minor Third</option>
        <option value="1.250">1.250 - Major Third</option>
        <option value="1.333">1.333 - Perfect Fourth</option>
        <option value="1.414">1.414 - Augmented Fifth</option>
        <option value="1.500">1.500 - Perfect Fifth</option>
        <option value="1.618" selected>1.618 - Golden Ratio</option>
      </select>
      <label for="num-colors" class="block mb-2 text-left text-2xl">Number of colors</label>
      <input type="number" id="num-colors" class="block mb-8" value={7} on:change={handleGenerate} />
    </form>
    <!-- RESULT -->
    <!-- TODO: make this sticky -->
    <div class="sticky top-0 right-0 w-1/2 flex flex-col items-end">
      {#if results.length > 0}
        <h2 class="mb-2 text-left text-2xl">Result</h2>
        <div class="flex flex-col gap-2">
          {#each results as result}
            <div class="flex">
              {#each result as subresult}
                <div class="w-16 h-16 rounded-full border border-black/20" style="background-color: {subresult}"></div>
              {/each}
            </div>
          {/each}
        </div>
      {/if}
    </div>
  </section>
</main>
