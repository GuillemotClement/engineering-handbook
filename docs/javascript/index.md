# JavaScript 

## DOM 

```html
<html>
  <body>
    <div id="app"></div> // élément viser par le js 
    
    // import du script js 
    <script type="text/javascript">
      const app = document.getElementById('app'); // recupération de la div
      const header = document.createElement('h1'); // creation d'un h1
      const text = 'Develop. Preview. Ship.'; // déclaration et creation du texte
      const headerContent = document.createTextNode(text);
      header.appendChild(headerContent); // ajout du text au h1
      app.appendChild(header); // ajout du nouvel élément du le div
    </script>
  </body>
</html>
```