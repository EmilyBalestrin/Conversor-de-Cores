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

  // Mantém HSV entre 0-360
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
  function updateCMYK() {
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

  // Converte RGB para HSV
  function updateHSV() {
      const rNorm = r / 255;
      const gNorm = g / 255;
      const bNorm = b / 255;

      const max = Math.max(rNorm, gNorm, bNorm);
      const min = Math.min(rNorm, gNorm, bNorm);
      const delta = max - min;

      v = Math.round(max * 100);

      if (delta < 0.00001) {
          h = 0;
          s = 0;
      } else {
          s = Math.round((max === 0 ? 0 : delta / max) * 100);

          if (max === rNorm) {
              h = ((gNorm - bNorm) / delta) % 6;
          } else if (max === gNorm) {
              h = (bNorm - rNorm) / delta + 2;
          } else {
              h = (rNorm - gNorm) / delta + 4;
          }

          h = Math.round(h * 60);
          if (h < 0) h += 360;
      }
  }

  // Converte CMYK para RGB
  function updateRGBFromCMYK() {
      const cNorm = c / 100;
      const mNorm = m / 100;
      const yNorm = y / 100;
      const kNorm = k / 100;

      r = Math.round(255 * (1 - cNorm) * (1 - kNorm));
      g = Math.round(255 * (1 - mNorm) * (1 - kNorm));
      b = Math.round(255 * (1 - yNorm) * (1 - kNorm));
  }

  // Converte HSV para RGB
  function updateRGBFromHSV() {
      const hNorm = h / 360;
      const sNorm = s / 100;
      const vNorm = v / 100;

      const chroma = vNorm * sNorm;
      const x = chroma * (1 - Math.abs((hNorm * 6) % 2 - 1));
      const m = vNorm - chroma;

      let rNorm, gNorm, bNorm;

      if (hNorm < 1/6) {
          [rNorm, gNorm, bNorm] = [chroma, x, 0];
      } else if (hNorm < 2/6) {
          [rNorm, gNorm, bNorm] = [x, chroma, 0];
      } else if (hNorm < 3/6) {
          [rNorm, gNorm, bNorm] = [0, chroma, x];
      } else if (hNorm < 4/6) {
          [rNorm, gNorm, bNorm] = [0, x, chroma];
      } else if (hNorm < 5/6) {
          [rNorm, gNorm, bNorm] = [x, 0, chroma];
      } else {
          [rNorm, gNorm, bNorm] = [chroma, 0, x];
      }

      r = Math.round((rNorm + m) * 255);
      g = Math.round((gNorm + m) * 255);
      b = Math.round((bNorm + m) * 255);
  }

  // Atualiza tudo quando a aba RGB muda
  $: if (abaAtual === 'rgb') {
      rgbNormalizado = [
          parseFloat((r / 255).toFixed(2)),
          parseFloat((g / 255).toFixed(2)),
          parseFloat((b / 255).toFixed(2))
      ];
      escalaCinza = Math.round(0.299 * r + 0.587 * g + 0.114 * b);
      updateCMYK();
      updateHSV();
  }

  // Atualiza quando a aba CMYK muda
  $: if (abaAtual === 'cmyk') {
      updateRGBFromCMYK();
      rgbNormalizado = [
          parseFloat((r / 255).toFixed(2)),
          parseFloat((g / 255).toFixed(2)),
          parseFloat((b / 255).toFixed(2))
      ];
      escalaCinza = Math.round(0.299 * r + 0.587 * g + 0.114 * b);
      updateHSV();
  }

  // Atualiza quando a aba HSV muda
  $: if (abaAtual === 'hsv') {
      updateRGBFromHSV();
      rgbNormalizado = [
          parseFloat((r / 255).toFixed(2)),
          parseFloat((g / 255).toFixed(2)),
          parseFloat((b / 255).toFixed(2))
      ];
      escalaCinza = Math.round(0.299 * r + 0.587 * g + 0.114 * b);
      updateCMYK();
  }

  // Observadores extras para garantir que tudo funcione corretamente
  $: if (abaAtual === 'cmyk') {
      const _c = c, _m = m, _y = y, _k = k;
      updateRGBFromCMYK();
  }

  $: if (abaAtual === 'hsv') {
      const _h = h, _s = s, _v = v;
      updateRGBFromHSV();
  }

</script>

<main>
    <h1>Conversor de Cores</h1>

    <div class="tabs">
        <button class={abaAtual === 'rgb' ? 'active' : ''} on:click={() => abaAtual = 'rgb'}>RGB → All</button>
        <button class={abaAtual === 'cmyk' ? 'active' : ''} on:click={() => abaAtual = 'cmyk'}>CMYK → RGB</button>
        <button class={abaAtual === 'hsv' ? 'active' : ''} on:click={() => abaAtual = 'hsv'}>HSV → RGB</button>
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

    {:else if abaAtual === 'cmyk'}
        <!-- Tela CMYK para RGB -->
        <div class="input-group">
            <h2>CMYK</h2>
            <div class="input-row">
                <label>C:</label>
                <input type="number" min="0" max="100" bind:value={c} on:change={() => c = validaCMYK(c)}>
                <input type="range" min="0" max="100" bind:value={c}>
            </div>
            <div class="input-row">
                <label>M:</label>
                <input type="number" min="0" max="100" bind:value={m} on:change={() => m = validaCMYK(m)}>
                <input type="range" min="0" max="100" bind:value={m}>
            </div>
            <div class="input-row">
                <label>Y:</label>
                <input type="number" min="0" max="100" bind:value={y} on:change={() => y = validaCMYK(y)}>
                <input type="range" min="0" max="100" bind:value={y}>
            </div>
            <div class="input-row">
                <label>K:</label>
                <input type="number" min="0" max="100" bind:value={k} on:change={() => k = validaCMYK(k)}>
                <input type="range" min="0" max="100" bind:value={k}>
            </div>
        </div>

        <div class="results">
            <div class="result-group">
                <h3>RGB Resultante:</h3>
                <p>R: {r}</p>
                <p>G: {g}</p>
                <p>B: {b}</p>
            </div>
            <div class="result-group">
                <h3>HSV:</h3>
                <p>H: {h}°</p>
                <p>S: {s}%</p>
                <p>V: {v}%</p>
            </div>
        </div>
    {:else}
        <!-- Tela HSV para RGB -->
        <div class="input-group">
            <h2>HSV</h2>
            <div class="input-row">
                <label>H (0-360°):</label>
                <input type="number" min="0" max="360" bind:value={h} on:change={() => h = validaHSV(h)}>
                <input type="range" min="0" max="360" bind:value={h}>
            </div>
            <div class="input-row">
                <label>S (0-100%):</label>
                <input type="number" min="0" max="100" bind:value={s} on:change={() => s = validaPorcentagem(s)}>
                <input type="range" min="0" max="100" bind:value={s}>
            </div>
            <div class="input-row">
                <label>V (0-100%):</label>
                <input type="number" min="0" max="100" bind:value={v} on:change={() => v = validaPorcentagem(v)}>
                <input type="range" min="0" max="100" bind:value={v}>
            </div>
        </div>

        <div class="results">
            <div class="result-group">
                <h3>RGB Resultante:</h3>
                <p>R: {r}</p>
                <p>G: {g}</p>
                <p>B: {b}</p>
            </div>
            <div class="result-group">
                <h3>CMYK:</h3>
                <p>C: {c}%</p>
                <p>M: {m}%</p>
                <p>Y: {y}%</p>
                <p>K: {k}%</p>
            </div>
        </div>
    {/if}
</main>

<style>
    /* Configurações gerais da página */
    :root {
        font-family: Arial, sans-serif; /* Fonte padrão */
        color: #333; /* Cor do texto principal */
    }

    /* Container principal */
    main {
        max-width: 800px; /* Largura máxima */
        margin: 0 auto; /* Centraliza na página */
        padding: 20px; /* Espaçamento interno */
    }

    /* Estilo do título principal */
    h1 {
        text-align: center; /* Centraliza o texto */
        color: #f9f9f9; /* Cor do texto */
        margin-bottom: 20px; /* Espaço abaixo */
    }

    /* Container das abas */
    .tabs {
        display: flex; /* Layout flexível */
        justify-content: center; /* Centraliza os botões */
        margin-bottom: 20px; /* Espaço abaixo */
        gap: 10px; /* Espaço entre botões */
    }

    /* Estilo dos botões das abas */
    .tabs button {
        padding: 10px 20px; /* Espaçamento interno */
        border: none; /* Remove borda padrão */
        background: #ffffff; /* Cor de fundo */
        color: #000000; /* Cor do texto */
        cursor: pointer; /* Cursor de ponteiro */
        border-radius: 5px; /* Bordas arredondadas */
        transition: all 0.3s ease; /* Transição suave */
        border: 1px solid #ddd; /* Borda fina */
        font-weight: normal; /* Peso da fonte */
    }

    /* Estilo do botão ativo */
    .tabs button.active {
        background: #4CAF50; /* Cor verde */
        color: white; /* Texto branco */
        font-weight: bold; /* Negrito */
        border: 1px solid #4CAF50; /* Borda verde */
    }

    /* Efeito hover nos botões */
    .tabs button:hover {
        background: #f0f0f0; /* Cor de fundo ao passar mouse */
    }

    /* Área de visualização da cor */
    .color-display {
        width: 100%; /* Largura total */
        height: 100px; /* Altura fixa */
        margin: 20px 0; /* Margem vertical */
        border-radius: 8px; /* Bordas arredondadas */
        box-shadow: 0 2px 5px rgba(0,0,0,0.2); /* Sombra */
        border: 1px solid #ddd; /* Borda fina */
    }

    /* Grupos de inputs */
    .input-group {
        background: #f5f5f5; /* Cor de fundo */
        padding: 20px; /* Espaçamento interno */
        border-radius: 8px; /* Bordas arredondadas */
        margin-bottom: 20px; /* Espaço abaixo */
        box-shadow: 0 1px 3px rgba(0,0,0,0.1); /* Sombra leve */
    }

    /* Título dos grupos */
    .input-group h2 {
        margin-top: 0; /* Remove margem superior */
        color: #333; /* Cor do texto */
        border-bottom: 1px solid #ddd; /* Linha abaixo */
        padding-bottom: 10px; /* Espaço abaixo */
    }

    /* Linha de input */
    .input-row {
        display: flex; /* Layout flexível */
        align-items: center; /* Alinha verticalmente */
        margin-bottom: 15px; /* Espaço abaixo */
    }

    /* Estilo dos labels */
    .input-row label {
        width: 100px; /* Largura fixa */
        font-weight: bold; /* Texto em negrito */
        color: #555; /* Cor do texto */
    }

    /* Inputs numéricos */
    .input-row input[type="number"] {
        width: 70px; /* Largura fixa */
        padding: 8px; /* Espaçamento interno */
        margin-right: 15px; /* Espaço à direita */
        border: 1px solid #ddd; /* Borda fina */
        border-radius: 4px; /* Bordas arredondadas */
    }

    /* Sliders (inputs de range) */
    .input-row input[type="range"] {
        flex-grow: 1; /* Ocupa espaço restante */
        height: 8px; /* Altura */
        border-radius: 4px; /* Bordas arredondadas */
        background: #ddd; /* Cor de fundo */
        outline: none; /* Remove outline */
    }

    /* Estilo do "ponteiro" do slider */
    .input-row input[type="range"]::-webkit-slider-thumb {
        -webkit-appearance: none; /* Remove estilo padrão */
        width: 18px; /* Largura */
        height: 18px; /* Altura */
        border-radius: 50%; /* Forma circular */
        background: #4CAF50; /* Cor verde */
        cursor: pointer; /* Cursor de ponteiro */
    }

    /* Container dos resultados */
    .results {
        display: grid; /* Layout em grid */
        grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); /* Colunas responsivas */
        gap: 20px; /* Espaço entre itens */
    }

    /* Grupos de resultados */
    .result-group {
        background: #f5f5f5; /* Cor de fundo */
        padding: 15px; /* Espaçamento interno */
        border-radius: 8px; /* Bordas arredondadas */
        box-shadow: 0 1px 3px rgba(0,0,0,0.1); /* Sombra leve */
    }

    /* Título dos resultados */
    .result-group h3 {
        margin-top: 0; /* Remove margem superior */
        color: #333; /* Cor do texto */
        border-bottom: 1px solid #ddd; /* Linha abaixo */
        padding-bottom: 8px; /* Espaço abaixo */
    }

    /* Texto dos resultados */
    .result-group p {
        margin: 8px 0; /* Margem vertical */
        font-family: 'Courier New', monospace; /* Fonte monoespaçada */
        color: #222; /* Cor do texto */
    }
</style>
