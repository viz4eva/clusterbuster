<script>
  import { slide } from "svelte/transition";
  import VisuBlock from "./VisuBlock.svelte";
  import { last_cluster, last_input } from "../stores";
  import Spinner from "./Spinner.svelte";
  //import { PUBLIC_API_URL } from "$env/static/public";
  export let data;
  export let input;
  export let visu_id;
  export let expertMode;
  export let generate;
  export let currentCluster;

  let isLastCluster;
  last_cluster.subscribe((currValue) => {
    isLastCluster = currValue.cluster_id === currentCluster;
  });
  let isLastInput;
  last_input.subscribe((currValue) => {
    isLastInput = currValue === input;
  });

  //hier wird verwaltet, welche Navigationsfragen angezeigt werden
  let visuBlocks = []; //anfangs leer und wird dann basierend auf Nav aufgefüllt
  let navDisplay = [true, true, true, true];
  let currentFocus = "init";

  //Zusammenfassung kann nur erzeugt werden, wenn gerade keine Fakten erzeugt werden und schon mind. zwei Visualisierungen gezeigt wurde
  let analysisSummary = "";
  let factCreationRunning = 0;
  let summaryCreationRunning = false;
  const fragen = {
    0: {
      0: "Wie erfolgreich ist dieser Text?",
      1: "Wie verteilt sich das Interesse über den Themenbereich?",
      2: "Wie ist die Leserschaft dieses Textes aufgebaut?",
      3: "Wann bestand am meisten Interesse an diesem Text?",
    },
    1: {
      0: "Wie gut schlägt sich der Text im globalen Vergleich?",
      1: "Sticht der Text aus seinem Themenbereich heraus?",
      2: "Welche Leser*innen wurden mit diesem Text erreicht?",
      3: "Wie haben sich diese Visits und Abos über die Zeit angesammelt?",
    },
    2: {
      0: "Ist der Text damit vergleichsweise erfolgreich?",
      1: "Welche anderen Texte behandeln Themen für diese Zielgruppe?",
      2: "Welche Leser*innen interessieren sich für diesen Text?",
      3: "War das Interesse dieser Zielgruppe konstant?",
    },
    3: {
      0: "War der Text damit insgesamt erfolgreich?",
      1: "Wurden in diesem Zeitraum viele ähnliche Texte veröffentlicht?",
      2: "Welche Zielgruppe stecken hinter dem Traffic?",
      3: "Wann war dieser Text für User*innen interessant?",
    },
  };
  let fragenVerlauf = [];

  function addVisuBlock(visList) {
    currentFocus = visList[0];
    const previous =
      visuBlocks.length == 0 ? currentFocus : visuBlocks[visuBlocks.length - 1];
    visuBlocks = visuBlocks.concat(visList);

    fragenVerlauf.push(fragen[previous][currentFocus]);

    visList.forEach((element) => {
      navDisplay[element] = false;
    });
  }

  function deleteVisuBlock(event) {
    if (visuBlocks.length > 1) {
      currentFocus = visuBlocks[visuBlocks.length - 1];
    } else {
      currentFocus = "init";
    }
    const visuIndex = visuBlocks.indexOf(event.detail.type);
    visuBlocks.splice(visuIndex, 1);
    visuBlocks = visuBlocks;
    // const navIndex = visuBlocks.indexOf(event.detail.type);
    // console.log(navIndex);
    navDisplay[event.detail.type] = true;
    navDisplay = navDisplay;
  }

  function summarize() {
    summaryCreationRunning = true;
    setTimeout(() => {
      if (
        input ===
        "https://www.zeit.de/sinn/2024-05/falschparker-melden-privatperson-abschleppwagen-ordnungsamt"
      ) {
        analysisSummary =
          "In den verschiedenen Analysen zeigte der ausgewählte Artikel eine überdurchschnittliche Anzahl an Besuchen im Vergleich zum Gesamtdurchschnitt, jedoch unterdurchschnittlich bei den Abos und ebenso eine niedrigere Besucherzahl im Vergleich zu ähnlichen Artikeln, jedoch mit überdurchschnittlichen Abos. Der thematische Themenkomplex des Artikels war im Verhältnis innerhalb seiner Nutzergruppen anders verteilt als der Artikel selbst, mit einem überraschenden Mangel an 'Brand Lovers' Engagement in beiden Fällen; zudem zeigte der ausgewählte Artikel bereits seine Spitzen bei Besuchen und Abonnements vor denen im gesamten Themenkomplex.";
      } else {
        analysisSummary =
          "Der ausgewählte Text erzielte im Vergleich zu allen Artikeln eine hohe Anzahl an Besuchen, lag jedoch bei den Abonnements darunter und erreichte sowohl bei Besuchen als auch bei Abonnements innerhalb des Clusters ähnlicher Texte unterdurchschnittliche Werte. Weitere Analysen zeigten, dass die Nutzergruppen-Verteilung der Besuche und Abonnements zwischen dem einzelnen Text und dem gesamten Themenkomplex variierte, wobeis die Brand Lovers viele Besuche, aber keine Abos vorwiesen und die Besuchs-Peaks des ausgewählten Textes zeitlich nicht mit denen des gesamten Themenkomplexes übereinstimmten.";
      }

      summaryCreationRunning = false;
    }, 3000);

    //dynamic creation is omitted for demo
    // fetch(`${PUBLIC_API_URL}/analysisSummary`)
    //   .then((res) => res.json())
    //   .then((json) => {
    //     analysisSummary = json.data[0].content[0].text.value;
    //     summaryCreationRunning = false;
    //   });
  }
</script>

<div class="textpath-wrapper">
  <!--Visualisierungsblöcke-->
  {#each visuBlocks as visuBlock, i (visuBlock)}
    <div transition:slide>
      <!--Input-Frage soll sich eigentlich nicht ändern-->
      <div class="input-doc">
        {fragenVerlauf[i]}
      </div>
      <VisuBlock
        type={visuBlock}
        {visu_id}
        {data}
        on:deleteMe={deleteVisuBlock}
        {input}
        path={"text"}
        on:running={() => (factCreationRunning += 1)}
        on:listening={() => (factCreationRunning -= 1)}
        {expertMode}
        {generate}
      />
    </div>
  {/each}

  {#if factCreationRunning == 0 && visuBlocks.length == 4 && !summaryCreationRunning && analysisSummary === "" && generate}
    <button on:click={summarize} disabled={!(isLastCluster && isLastInput)}
      >Fasse die bisherigen Analyse-Ergebnisse zusammen.</button
    >
  {/if}
  {#if summaryCreationRunning}
    <Spinner message={"Zusammenfassung wird generiert"} />
  {/if}
  <p>{analysisSummary}</p>

  <!--Navigation-->
  {#if navDisplay.includes(true)}
    <p>Was möchtest du wissen?</p>
    {#if navDisplay[0]}
      <button
        on:click={() => addVisuBlock([0])}
        disabled={!(isLastCluster && isLastInput)}
        >{fragen[currentFocus !== "init" ? currentFocus : 0][0]}</button
      >
    {/if}
    {#if navDisplay[1]}
      <button
        on:click={() => addVisuBlock([1])}
        disabled={!(isLastCluster && isLastInput)}
        >{fragen[currentFocus !== "init" ? currentFocus : 1][1]}</button
      >
    {/if}
    {#if navDisplay[2]}
      <button
        on:click={() => addVisuBlock([2])}
        disabled={!(isLastCluster && isLastInput)}
        >{fragen[currentFocus !== "init" ? currentFocus : 2][2]}</button
      >
    {/if}
    {#if navDisplay[3]}
      <button
        on:click={() => addVisuBlock([3])}
        disabled={!(isLastCluster && isLastInput)}
        >{fragen[currentFocus !== "init" ? currentFocus : 3][3]}</button
      >
    {/if}
  {:else}
    <p>Du hast alles zu diesem Thema gelesen.</p>
  {/if}
</div>

<style>
  .textpath-wrapper {
    padding: 20px;
    border-radius: 10px;
  }

  button {
    margin-bottom: 10px;
  }
</style>
