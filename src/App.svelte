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

</main>

<style>

</style>
