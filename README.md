<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>PokéMatrimonio</title>
  
  <!-- Google Fonts -->
  <link href="https://fonts.googleapis.com/css2?family=Oleo+Script&family=Open+Sans:ital,wght@1,400&display=swap" rel="stylesheet">
  
  <!-- jsPDF CDN -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  
  <style>
    /* Importar las fuentes para Open Sans Italic y Oleo Script */
    @import url('https://fonts.googleapis.com/css2?family=Oleo+Script&family=Open+Sans:ital,wght@1,400&display=swap');

    /* Estilos Generales */
    body {
      font-family: Arial, sans-serif;
      background-color: #f0f8ff;
      margin: 0;
      padding: 20px;
    }

    .container {
      max-width: 800px;
      margin: auto;
      background: white;
      padding: 30px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }

    /* Logo Centrado */
    .logo-container {
      text-align: center;
      margin-bottom: 20px;
    }

    .logo-container img {
      max-width: 200px;
      height: auto;
    }

    /* Formulario de Selección */
    .form-container {
      display: flex;
      justify-content: space-between;
      align-items: flex-end;
      margin-bottom: 30px;
      flex-wrap: wrap;
    }

    .dropdown-container, .input-container {
      display: flex;
      flex-direction: column;
      width: 48%;
      margin-bottom: 15px;
    }

    label {
      margin-bottom: 5px;
      color: #555;
    }

    select, input {
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 5px;
      font-family: Arial, sans-serif; /* Fuente normal */
      font-size: 16px;
    }

    /* Acta de Matrimonio */
    .acta {
      font-family: 'Open Sans', sans-serif;
      font-style: italic;
      background-color: #e8f5e9;
      border: 2px solid #4CAF50;
      border-radius: 10px;
      padding: 20px;
      display: none; /* Oculto inicialmente */
      margin-bottom: 20px;
    }

    .acta p {
      margin: 10px 0;
    }

    .acta .nombre-humano {
      font-family: 'Oleo Script', cursive;
      font-size: 1.2em;
    }

    /* Botones */
    .buttons-container {
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
    }

    .generate-btn {
      padding: 15px 30px;
      background-color: red;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      font-size: 16px;
      transition: background-color 0.3s ease, transform 0.3s ease;
      margin-bottom: 10px;
    }

    .generate-btn:hover {
      background-color: darkred;
      transform: scale(1.05);
    }

    .donate-btn {
      width: 100px;
      cursor: pointer;
      transition: transform 0.3s ease;
      margin-bottom: 10px;
    }

    .donate-btn:hover {
      transform: scale(1.1);
    }

    /* Responsividad */
    @media (max-width: 600px) {
      .dropdown-container, .input-container {
        width: 100%;
      }

      .buttons-container {
        flex-direction: column;
        align-items: stretch;
      }

      .generate-btn, .donate-btn {
        width: 100%;
        text-align: center;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <!-- Logo Centrado -->
    <div class="logo-container">
      <img src="https://i.postimg.cc/rFVdp9cq/Picsart-24-10-14-13-33-08-885.jpg" alt="Logo" id="logo">
    </div>
    
    <!-- Formulario de Selección -->
    <div class="form-container">
      <div class="dropdown-container">
        <label for="pokemon">Selecciona un Pokémon:</label>
        <select id="pokemon" name="pokemon" required>
          <option value="">-- Selecciona un Pokémon --</option>
          <option value="arceus">Arceus</option>
          <option value="slaking">Slaking</option>
          <option value="rapidash">Rapidash</option>
          <option value="octillery">Octillery</option>
          <option value="ursaring">Ursaring</option>
          <option value="typhlosion">Typhlosion</option>
          <option value="vaporeon">Vaporeon</option>
          <option value="gardevoir">Gardevoir</option>
          <option value="hypno">Hypno</option>
          <option value="mew">Mew</option>
        </select>
      </div>
      
      <div class="input-container">
        <label for="humanName">Nombre del Humano:</label>
        <input type="text" id="humanName" name="humanName" required>
      </div>
    </div>
    
    <!-- Acta de Matrimonio -->
    <div id="acta" class="acta">
      <!-- El contenido del acta se generará aquí -->
    </div>
    
    <!-- Botones -->
    <div class="buttons-container">
      <button type="button" id="generateBtn" class="generate-btn">Generar Acta</button>
      <a href="https://paypal.me/Astarot777?country.x=NI&locale.x=es_XC" target="_blank">
        <img src="https://i.postimg.cc/ZRs4M9ph/PayPal.png" alt="Donar con PayPal" class="donate-btn">
      </a>
    </div>
  </div>
  
  <script>
    // Importa la biblioteca jsPDF
    const { jsPDF } = window.jspdf;
    
    // Elementos del DOM
    const generateBtn = document.getElementById('generateBtn');
    const actaDiv = document.getElementById('acta');
    
    // Event Listener para el botón de generar acta
    generateBtn.addEventListener('click', generarActa);
    
    async function generarActa() {
      const nombreHumano = document.getElementById('humanName').value.trim();
      const pokemonSeleccionado = document.getElementById('pokemon').value;
    
      if (!nombreHumano || !pokemonSeleccionado) {
        alert('Por favor, completa todos los campos.');
        return;
      }
    
      // Obtener la fecha actual
      const fecha = new Date();
      const dia = fecha.getDate();
      const mes = fecha.getMonth() + 1; // Los meses empiezan en 0
      const año = fecha.getFullYear();
    
      // Obtener la región basada en la geolocalización
      const region = await obtenerRegion();
    
      // Asignar número de PokéDex de forma aleatoria (ejemplo: entre 1 y 900)
      const numeroPokedex = Math.floor(Math.random() * 900) + 1;
    
      // Nombre del profesor con apellido Astaroth
      const nombreProfesor = `Profesor Astaroth`;
    
      // Nombre oficial del Pokémon (puedes personalizar esto si lo deseas)
      const nombreOficialPokemon = capitalizarPrimeraLetra(pokemonSeleccionado);
    
      // Crear el acta en HTML
      const actaHTML = `
        <h2>ACTA DE MATRIMONIO</h2>
        <p><strong>En la Liga Pokémon de la región ${region}, a los ${dia} días del mes de ${obtenerNombreMes(mes)}, del año ${año}, se declara formalmente el matrimonio entre:</p>
    
        <p><strong class="nombre-humano">${nombreHumano}</strong><br>
        Humano/a perteneciente a la región de ${region}.</p>
    
        <p><strong>${nombreOficialPokemon}</strong><br>
        Especie: ${capitalizarPrimeraLetra(pokemonSeleccionado)}<br>
        Número en la Pokédex: #${numeroPokedex}</p>
    
        <p>Bajo la supervisión del Alto Mando de la región de ${region}, así como del ${nombreProfesor}, ambos individuos han decidido unirse en matrimonio bajo las leyes y regulaciones de la Liga Pokémon.</p>
    
        <p>Se declara que, de ahora en adelante, ambas partes se comprometen a respetar y protegerse mutuamente en salud, en batalla, y en cualquier aventura o desafío que enfrenten en el futuro.</p>
    
        <p><strong>Certificado por:</strong><br>
        ${nombreOficialPokemon} de la región de ${region}.</p>
    
        <p><strong>Fecha de expedición:</strong> ${dia}/${mes}/${año}</p>
      `;
    
      actaDiv.innerHTML = actaHTML;
      actaDiv.style.display = 'block';
    
      // Generar y descargar el PDF
      generarPDF(nombreHumano, nombreOficialPokemon);
    }
    
    // Función para capitalizar la primera letra
    function capitalizarPrimeraLetra(string) {
      return string.charAt(0).toUpperCase() + string.slice(1);
    }
    
    // Función para obtener el nombre del mes
    function obtenerNombreMes(numeroMes) {
      const meses = [
        'Enero', 'Febrero', 'Marzo', 'Abril', 'Mayo', 'Junio',
        'Julio', 'Agosto', 'Septiembre', 'Octubre', 'Noviembre', 'Diciembre'
      ];
      return meses[numeroMes - 1];
    }
    
    // Función para obtener la región basada en la geolocalización
    async function obtenerRegion() {
      try {
        const response = await fetch('https://ipapi.co/json/');
        const data = await response.json();
        const pais = data.country_code;
    
        switch (pais) {
          case 'JP': // Japón
            return 'Kanto';
          case 'FR': // Francia
            return 'Kalos';
          case 'US': // Estados Unidos
            return 'Unova';
          case 'GB': // Reino Unido
            return 'Galar';
          case 'ES': // España
            return 'Paldea';
          // Agrega más casos según tus necesidades
          default:
            return 'Kanto'; // Región por defecto
        }
      } catch (error) {
        console.error('Error al obtener la región:', error);
        return 'Kanto'; // Región por defecto en caso de error
      }
    }
    
    // Función para generar y descargar el PDF
    function generarPDF(nombreHumano, nombreOficialPokemon) {
      const doc = new jsPDF();
      
      // Configurar fuentes predeterminadas
      doc.setFont('Open Sans', 'italic');
      
      // Obtener el contenido del acta
      const actaContent = document.getElementById('acta').innerText;
      
      // Dividir el contenido en líneas que caben en el PDF
      const lines = doc.splitTextToSize(actaContent, 180);
      let y = 20; // Posición vertical inicial
    
      // Añadir el texto al PDF
      lines.forEach(line => {
        // Detectar si la línea contiene el nombre del humano para cambiar la fuente
        if (line.includes(nombreHumano)) {
          const parts = line.split(nombreHumano);
          if (parts.length > 1) {
            doc.setFont('Open Sans', 'italic');
            doc.text(parts[0], 10, y);
            doc.setFont('Oleo Script', 'normal');
            doc.text(nombreHumano, 10 + doc.getTextWidth(parts[0] + ' '), y);
            doc.setFont('Open Sans', 'italic');
            doc.text(parts[1], 10 + doc.getTextWidth(parts[0] + ' ' + nombreHumano + ' '), y);
          } else {
            doc.text(line, 10, y); de 
          }
        } else {
          doc.text(line, 10, y);
        }
        y += 10;
      });
    
      // Descargar el PDF
      doc.save(`${nombreHumano}_${nombreOficialPokemon}_Acta.pdf`);
    }
  </script>
</body>
</html>
