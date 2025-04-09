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
  function validateRGB(value) {
    let num = parseInt(value) || 0;
    return Math.min(255, Math.max(0, num));
  }

  // Mantém CMYK entre 0-100
  function validateCMYK(value) {
    let num = parseInt(value) || 0;
    return Math.min(100, Math.max(0, num));
  }

  // Mantém Hue entre 0-360
  function validateHue(value) {
    let num = parseInt(value) || 0;
    return Math.min(360, Math.max(0, num));
  }

  // Mantém porcentagens entre 0-100
  function validatePercent(value) {
    let num = parseInt(value) || 0;
    return Math.min(100, Math.max(0, num));
  }
</script>

<main>

</main>

<style>

</style>
