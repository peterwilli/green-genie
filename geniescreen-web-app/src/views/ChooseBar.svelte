<style>
  #nav {
    text-align: center;
    margin-top: 15px;
    margin-bottom: 15px;
    border-top: 1px #fff solid;
    border-bottom: 1px #fff solid;
    height: 50px;
    background: #383838;
  }
  #nav li {
    display: inline-block;
    height: 100%;
  }
  #nav a:hover {
    background-color: rgb(107, 107, 107);
  }
  #nav a:active {
    background-color: rgb(39, 39, 39);
  }
  #nav a {
    display: inline-block;
    line-height: 50px;
    height: 100%;
    text-decoration: none;
    padding: 0 30px;
    color: #539c1e;
    font-weight: bold;
  }
</style>

<script>
    export let state = 0;
</script>

<main>
  <ul id="nav">
    <li on:click="{() => state = 1}"><a href="#">Webcam</a></li>
    <li on:click="{() => state = 2}"><a href="#">Video Footage (Coming soon)</a></li>
  </ul>
</main>
