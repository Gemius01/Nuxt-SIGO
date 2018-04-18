<script>
	module.exports = {
  API_LOCATION: 'http://localhost:8081aa',
  rules: [(v) => !!v || 'Campo vacío', v => (v && v.length <= 20) || 'Máximo de caracteres']
}
</script>
