<script>
	module.exports = {
  API_LOCATION: 'http://localhost:8081',
  textoRules: [(v) => !!v || 'Campo vacío', v => (v && v.length <= 20) || 'Máximo de caracteres'],
	  numberRules: [(v) => v > 0 || 'No puede ser menor a 0', v => (v <= 10000000000000000000) || 'Máximo de caracteres'],
	  parrafoRules: [(v) => !!v || 'Campo vacío', v => (v && v.length <= 100) || 'Máximo de caracteres'],
	  emailRules: [v => !!v || 'Campo vacío', v => /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail debe ser valido']
}
</script>
