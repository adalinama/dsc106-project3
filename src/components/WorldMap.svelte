<script>
    import App from './App.svelte';
  </script>
  
  <main>
    <WorldMap dataUrl="https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/world.geojson"/>
  </main>
  
  <style>
    body, html {
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100%;
      overflow: hidden;
    }
  </style>
  