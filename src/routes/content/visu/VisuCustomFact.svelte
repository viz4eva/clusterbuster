<script>
  import { onMount, createEventDispatcher } from "svelte";
  import Spinner from "../Spinner.svelte";
  //import { PUBLIC_API_URL } from '$env/static/public';

  export let type;
  export let input;
  let gptFact = "";
  const dispatch = createEventDispatcher();
  let loading = true;

  const staticFacts = {
    text: {
      0: {
        kinder:
          "Der ausgewählte Text weist sowohl im Vergleich mit allen anderen Texten als auch mit ähnlichen Texten zwar mehr Visits, aber weniger Abos auf. Der Trend ist in beiden Fällen ähnlich: Die Visits des Textes liegen über dem Durchschnitt, die Abos jedoch etwas darunter. Somit kann der Text als besuchsrelevant, aber in Hinsicht auf Abos als leicht unterdurchschnittlich betrachtet werden.",
        denunziant:
          "Der ausgewählte Text erhielt deutlich mehr Besuche als der Durchschnitt aller Texte, jedoch weniger Abonnements. Im Vergleich zu ähnlichen Texten liegt er bei den Besuchen unter dem Durchschnitt, aber bei den Abonnements darüber. Die Ergebnisse gegenüber allen Texten und den ähnlichen Texten fallen unterschiedlich aus, wobei der Text hinsichtlich der Besucherzahlen positiver gegenüber dem Gesamtportfolio abschneidet.",
      },
      1: {
        kinder:
          "Die Werte sind vielfältig, und während die meisten Artikel eine ähnliche Anzahl an Visits und Abos erreicht haben, gibt es dennoch Ausnahmen. Die zwei Artikel, die am meisten Besuche von der Norm abweichen, sind Kindererziehung Entwicklung Eltern Verunsicherung und Erziehung Spielen Kinder Eltern Diskussion. Bei den Abonnements sind Bindungsorientierte Erziehung Eltern Karin Klaus Grossmann und der erste der beiden genannten Artikel zu Besuchen die größten Ausreißer. Der aktuell ausgewählte Text gehört jedoch nicht zu diesen Ausreißern und war somit weniger erfolgreich.",
        denunziant:
          "Die zwei Artikel, die bei den Besuchen am meisten vom Durchschnitt abweichen sind Deutschlandticket Erfahrungen Finanzierung und Fahrradkauf Richtige Finden Fahrradtypen Hollandrad Rennrad. Bei den Abonnements zeigen Fahrradkauf Richtige Finden Fahrradtypen Hollandrad Rennrad und Radverkehr Berlin Verkehrspolitik Autofahrer die größte Abweichung. Der gerade betrachtete Artikel war hinsichtlich beider Kriterien kein Ausreißer und hat im Vergleich eine durchschnittliche Performance gezeigt.",
      },
      2: {
        kinder:
          "Die Besuche verteilen sich über verschiedene Nutzergruppen mit besonderer Präsenz der Fast Mover, erkennbar an dem ausgedehnten eingefärbten Anteil in der Grafik. Die Abonnements sind stark auf die Flybys und Casual Reader verteilt, wobei die Flybys einen sehr großen Anteil einnehmen. Brand Lovers machen etwa ein Viertel der Besuche aus, doch überraschenderweise gibt es keine Abonnements von ihnen – ein Umstand, der in der Visualisierung durch das Fehlen des entsprechenden Anteils deutlich wird.",
        denunziant:
          "Die Besuchszahlen verteilen sich ungleichmäßig über die verschiedenen Nutzergruppen, wobei die 'Fast Mover' mit einem deutlich breit eingefärbten Anteil die meisten Besuche auf sich vereinen. 'Brand Lovers' stellen trotz ihrer starken Bindung zur Marke nur einen mittleren Anteil der Besuche dar und schließen keine Abonnements ab. Bei den Abonnements sind die Unterschiede nicht so markant, da keine Gruppe signifikant mehr Abos generiert als die anderen, was in der grafischen Darstellung an fast gleich großen eingefärbten Segmenten zu erkennen ist.",
      },
      3: {
        kinder:
          "Es gab zwei bemerkenswerte Peaks bei den Besuchs- und Abozahlen des Artikels am 23. und am 26. Februar 2024. Diese beiden Peaks traten sowohl bei den Besuchen als auch bei den Abos auf und fielen auf dieselben Tage.",
        denunziant:
          "Es gab einen markanten Höhepunkt bei den Besuchen am 20. Mai 2024, einem zweiten, aber deutlich niedrigeren am 19. Mai 2024. Die meisten Abonnements wurden ebenfalls am 20. Mai 2024 abgeschlossen, gefolgt von einem weiteren, geringeren Peak am 18. Mai 2024. Der Höchstwert bei den Besuchen ging somit mit dem Höchstwert bei den Abonnements einher.",
      },
    },
    cluster: {
      4: {
        kinder:
          "Das Thema des ausgewählten Themenkomplexes wird, wie in der Visualisierung ersichtlich, weniger besucht und weist weniger Abos im Vergleich zum Durchschnitt aller Themenkomplexe auf. Die Visits und Abos des Themenkomplexes liegen somit beidesmal unter dem Durchschnitt. Da der ausgewählte Einzeltext überdurchschnittlich viele Besuche, aber leicht unterdurchschnittliche Abozahlen aufweist, deutet dies darauf hin, dass der Einzeltext im Vergleich zum allgemeinen Trend seines Themenbereiches erfolgreicher ist. Der Erfolg des Einzeltextes bildet in diesem Fall nicht den allgemeinen Trend der thematisch ähnlichen Texte ab.",
        denunziant:
          "Das Thema des ausgewählten Textes ist insgesamt beliebter als der Durchschnitt des gesamten Themenkomplexes, da sowohl die Besuche als auch die Abonnements über den Linien liegen, die die Durchschnittswerte aller Themen abbilden. Im Vergleich zeigt sich jedoch, dass die ähnlichen Texte weniger Besuche, aber mehr Abonnements erhalten als der ausgewählte Text, dessen Besuchszahlen ihm einen Vorsprung verschaffen, jedoch mit geringeren Abonnementzahlen einhergehen. Daher kann nicht unbedingt vom Erfolg des Einzeltextes auf den Erfolg thematisch ähnlicher Texte geschlossen werden",
      },
      5: {
        kinder:
          "Die Verteilung der Besuchs- und Abozahlen auf die unterschiedlichen Nutzersegmente differiert sowohl für den einzelnen ausgewählten Text als auch für den gesamten Themenkomplex. Für den Einzeltext sind vor allem Fast Mover und Flybys präsent, während im gesamten Themenkomplex eine Dominanz der Flybys gegenüber anderen Gruppen zu erkennen ist. Abonnementzahlen sind zudem bei Flybys des Einzeltextes ausgesprochen höher im Vergleich zu anderen Gruppen und zu denen des Themenkomplexes. Brand Lovers haben beim Einzeltext einen beachtlichen Anteil an den Besuchen, schließen jedoch keine Abonnements ab. Insgesamt lässt der Einzeltext keine zuverlässigen Schlüsse auf das Nutzerverhalten des gesamten Themenkomplexes zu.",
        denunziant:
          "Bei dem einzelnen Artikel sind die 'Fast Mover' am auffälligsten vertreten, gefolgt von den 'Brand Lovers', während bei dem gesamten Themenkomplex die 'Flybys' dominieren, sowohl bei Visits als auch bei Abonnements. Diese Verschiebung zeigt, dass die Nutzerverteilung für den Einzeltext nicht direkt auf den Themenkomplex übertragbar ist. Die 'Brand Lovers' sind sowohl für den einzelnen Artikel als auch für den Themenkomplex nicht die vorrangige Besuchergruppe, was in der Visualisierung an dem nicht annähernd dominantesten eingefärbten Segment ablesbar ist.",
      },
      6: {
        kinder:
          "Die Peaks bei Besuchszahlen für den ausgewählten Text waren am 23. und 26. Februar 2024, was auch für die Abos zutrifft. Im gesamten Themenkomplex traten jedoch die Besuchs-Peaks zu anderen Zeiten auf, nämlich am 8. Mai und 6. April 2024, und auch Abonnement-Peaks waren zu anderen Daten, mit Peaks am 8. Mai 2024 und 11. Dezember 2023. Es gibt keine Übereinstimmungen zwischen den Peak-Daten für den einzelnen Text und denen des Themenkomplexes, was darauf schließen lässt, dass die Peaks des ausgewählten Artikels nicht für die besonders hohen Visit-Zahlen im gesamten Themenkomplex verantwortlich sind.",
        denunziant:
          "Für den einzelnen Artikel gab es zwei markante Besucherhöhepunkte am 19. und 20. Mai 2024 sowie zwei maximale Werte für Abonnements am 18. und 20. Mai 2024. Innerhalb des gesamten Themenkomplexes traten Spitzenwerte bei den Visits am 2. und 3. Mai 2024 auf, wohingegen die Abonnements am 27. und 30. März 2024 ihre Höhepunkte hatten. Die Peaks im Visits-Verlauf zwischen dem ausgewählten Text und dem gesamten Themenkomplex stimmen zeitlich nicht überein, was darauf hindeutet, dass die hohen Besuchszahlen des Einzeltextes nicht maßgeblich für die Peaks im Gesamtkomplex waren.",
      },
    },
  };

  onMount(() => {
    getGPTFact();
  });

  async function getGPTFact() {
    //get describing fact about data
    dispatch("running");

    //demo uses static pre-computed caption
    let exampleArticle = "kinder";
    if (
      input ===
      "https://www.zeit.de/sinn/2024-05/falschparker-melden-privatperson-abschleppwagen-ordnungsamt"
    ) {
      exampleArticle = "denunziant";
    }

    setTimeout(() => {
      gptFact =
        type < 4
          ? staticFacts.text[type][exampleArticle]
          : staticFacts.cluster[type][exampleArticle];
      dispatch("listening");
      loading = false;
    }, 1000);

    //dynamic caption generation omitted in demo version
    // fetch(`${PUBLIC_API_URL}/customFact?type=${type}`).then(
    //   async (response) => {
    //     loading = false;
    //     const reader = response.body.getReader();
    //     for await (const chunk of readChunks(reader)) {
    //       //console.log(`received  ${chunk}`);
    //       gptFact += Utf8ArrayToStr(chunk);
    //       if (type === 1) {
    //         gptFact = gptFact.replace(
    //           /\[(.*?)\]\((.*?)\)/g,
    //           '<a href="$2" target="_blank">$1</a>'
    //         );
    //       }
    //       gptFact = gptFact.replace(/\n/g, "<br>");
    //     }
    //     dispatch("listening");
    //   }
    // );

    // function readChunks(reader) {
    //   return {
    //     async *[Symbol.asyncIterator]() {
    //       let readResult = await reader.read();
    //       while (!readResult.done) {
    //         yield readResult.value;
    //         readResult = await reader.read();
    //       }
    //     },
    //   };
    // }

    // function Utf8ArrayToStr(array) {
    //   var out, i, len, c;
    //   var char2, char3;

    //   out = "";
    //   len = array.length;
    //   i = 0;
    //   while (i < len) {
    //     c = array[i++];
    //     switch (c >> 4) {
    //       case 0:
    //       case 1:
    //       case 2:
    //       case 3:
    //       case 4:
    //       case 5:
    //       case 6:
    //       case 7:
    //         // 0xxxxxxx
    //         out += String.fromCharCode(c);
    //         break;
    //       case 12:
    //       case 13:
    //         // 110x xxxx   10xx xxxx
    //         char2 = array[i++];
    //         out += String.fromCharCode(((c & 0x1f) << 6) | (char2 & 0x3f));
    //         break;
    //       case 14:
    //         // 1110 xxxx  10xx xxxx  10xx xxxx
    //         char2 = array[i++];
    //         char3 = array[i++];
    //         out += String.fromCharCode(
    //           ((c & 0x0f) << 12) | ((char2 & 0x3f) << 6) | ((char3 & 0x3f) << 0)
    //         );
    //         break;
    //     }
    //   }

    //   return out;
    // }
  }
</script>

{#if loading}
  <Spinner message={"Erklärung wird generiert..."} />
{:else}
  <p>{@html gptFact}</p>
{/if}
