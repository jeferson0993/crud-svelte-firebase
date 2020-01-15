<script>
	import Tasks from "./Tasks/Task.svelte"
	const projectName = "To Do App"
	const src="https://avatars2.githubusercontent.com/u/23617963?s=40&v=4"
</script>

<style>
nav {
	padding-top: 1ch;
	margin-bottom: 1.5ch
}
.subtitle {
	text-align: center
}
.title {
	color: aliceblue
}
</style>

<nav class="navbar is-link" role="navigation" aria-label="main navigation">
  <div class="navbar-brand">
    <figure class="image is-42x42">
		<img {src} alt="Svelte3"/>
	</figure>
    <div class="navbar-start">
      <h1 class="title">{projectName.toUpperCase()}</h1>
    </div>
  </div>
</nav>

<section>
	<div class="container">
		<p class="subtitle">Minha lista de tarefas feita com 
		<strong>Svelte!</strong></p>
	</div>
	<Tasks/>
</section>

<footer class="footer">
  <div class="content has-text-centered">
    <p>
      <strong>Lista de tarefas com svelte</strong> por <a href="http://">Jeferson Ferreira</a>. O codigo fonte está sob a licença
      <a href="http://">MIT</a>. O repositorio do projeto pode ser encontrado <a href="http://">aqui</a>.
    </p>
  </div>
</footer>
