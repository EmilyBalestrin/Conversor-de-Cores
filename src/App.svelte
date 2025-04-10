<script>
  let abaAtual = 'rgb';

  // Valores iniciais RGB
  let r = 0;
  let g = 0;
  let b = 0;

  // Valores CMYK (serão calculados)
  let c = 0;
  let m = 0;
  let y = 0;
  let k = 0;

  // Valores HSV (serão calculados)
  let h = 0;
  let s = 0;
  let v = 0;

  // Valores calculados automaticamente
  let rgbNormalizado = [0.00, 0.00, 0.00]; // RGB entre 0 e 1
  let escalaCinza = 0; // Valor em tons de cinza

  // Funções que validam os valores para que fiquem dentro dos limites
  // Mantém RGB entre 0-255
  function validaRGB(value) {
    let num = parseInt(value) || 0;
    return Math.min(255, Math.max(0, num));
  }

  // Mantém CMYK entre 0-100
  function validaCMYK(value) {
    let num = parseInt(value) || 0;
    return Math.min(100, Math.max(0, num));
  }

  // Mantém Hue entre 0-360
  function validaHSV(value) {
    let num = parseInt(value) || 0;
    return Math.min(360, Math.max(0, num));
  }

  // Mantém porcentagens entre 0-100
  function validaPorcentagem(value) {
    let num = parseInt(value) || 0;
    return Math.min(100, Math.max(0, num));
  }

  // Converte RGB para CMYK
  function atualizaCMYK() {
    const rNorm = r / 255;
    const gNorm = g / 255;
    const bNorm = b / 255;

    k = 1 - Math.max(rNorm, gNorm, bNorm);
    c = k === 1 ? 0 : (1 - rNorm - k) / (1 - k);
    m = k === 1 ? 0 : (1 - gNorm - k) / (1 - k);
    y = k === 1 ? 0 : (1 - bNorm - k) / (1 - k);

    // Converte para porcentagem
    c = Math.round(c * 100);
    m = Math.round(m * 100);
    y = Math.round(y * 100);
    k = Math.round(k * 100);
  }
</script>

<main>
    <h1>Conversor de Cores</h1>

    <div class="tabs">
        <button class={abaAtual === 'rgb' ? 'active' : ''} on:click={() => abaAtual = 'rgb'}>RGB → All</button>
    </div>

    <div class="color-display" style="background-color: rgb({r}, {g}, {b})"></div>

    {#if abaAtual === 'rgb'}
        <!-- Tela RGB para todas as conversões -->
        <div class="input-group">
            <h2>RGB</h2>
            <div class="input-row">
                <label>R:</label>
                <input type="number" min="0" max="255" bind:value={r} on:change={() => r = validaRGB(r)}>
                <input type="range" min="0" max="255" bind:value={r}>
            </div>
            <div class="input-row">
                <label>G:</label>
                <input type="number" min="0" max="255" bind:value={g} on:change={() => g = validaRGB(g)}>
                <input type="range" min="0" max="255" bind:value={g}>
            </div>
            <div class="input-row">
                <label>B:</label>
                <input type="number" min="0" max="255" bind:value={b} on:change={() => b = validaRGB(b)}>
                <input type="range" min="0" max="255" bind:value={b}>
            </div>
        </div>

        <div class="results">
            <div class="result-group">
                <h3>CMYK:</h3>
                <p>C: {c}%</p>
                <p>M: {m}%</p>
                <p>Y: {y}%</p>
                <p>K: {k}%</p>
            </div>

            <div class="result-group">
                <h3>HSV:</h3>
                <p>H: {h}°</p>
                <p>S: {s}%</p>
                <p>V: {v}%</p>
            </div>

            <div class="result-group">
                <h3>Escala de Cinza:</h3>
                <p>{escalaCinza}</p>
            </div>

            <div class="result-group">
                <h3>RGB Normalizado:</h3>
                <p>[{rgbNormalizado[0].toFixed(2)}, {rgbNormalizado[1].toFixed(2)}, {rgbNormalizado[2].toFixed(2)}]</p>
            </div>
        </div>
    {/if}
</main>

<style>

</style>
