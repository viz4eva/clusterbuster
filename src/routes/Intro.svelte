<script>
  export let expertMode;
</script>

<div class="intro-wrapper">
  <details open={!expertMode}>
    <summary>Intro</summary>
    <p>
      Diese Anwendung wurde am Beispiel von Nutzungsdaten ZEIT Online-Artikeln erstellt. <br> Die hier vorliegend Demo-Version zeigt abgeänderte Daten für zwei ausgewählte Artikel,
      die nicht der Realität entsprechen. Einige Funktionen sind in dieser Version nicht verfügbar. <br><br>
      Hier kannst du mehr über die Nutzungsdaten einzelner Texte herausfinden. <br>
      Wie erfolgreich sind sie? Wann und bei wem besonders?
      Und lässt sich daraus ein genereller Trend für einen gesamten Themenbereich ableiten? <br>
      Du wirst Datenvisualisierungen sowie automatisch generierte Erklärungen dazu sehen. 
      <br /><br />
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
    width: 45vw;
  }
</style>
