<script>
  export let expertMode;
</script>

<div class="intro-wrapper">
  <details open={!expertMode}>
    <summary>Intro</summary>
    <p>
      Hier kannst du mehr über die Nutzungsdaten einzelner ZON-Texte herausfinden. <br>
      Wie erfolgreich sind sie? Wann und bei wem besonders?
      Und lässt sich daraus ein genereller Trend für einen gesamten Themenbereich ableiten? <br>
      Du wirst Datenvisualisierungen sowie automatisch generierte Erklärungen dazu sehen. 
      <br /><br />
      Suche über die Zeile nach einem Text, der dich interessiert.
      <br /><br />
      Die zugrundeliegenden Daten stammen aus BigQuery und umfassen Traffic seit Anfang 2023.
    </p>
  </details>
</div>

<style>
  .intro-wrapper {
    text-align: left;
  }

  details {
    margin: 10px;
    margin-bottom: 20px;
    width: 40vw;
  }
</style>
