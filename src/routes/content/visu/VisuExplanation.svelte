<script>
  export let type;
  export let expertMode;
</script>

<div class="explanation-wrapper">
  <details open={!expertMode}>
    <summary>?</summary>

    {#if type == 0}
      <p>
        <span style="font-weight:800">How to read:</span><br />
        Die Visualisierungen haben je zwei Skalen, links eine für Visits und rechts
        eine für Abos. <br />
        Die <span class="gradient-text">farbigen Markierungen</span>, verbunden
        durch die
        <span style="font-weight:800">schwarze Linie</span>, zeigen wie viele
        Abos bzw. Visits der gewählte Text erreicht hat.
        <br />
        Um vergleichen zu können, ob der Text damit erfolgreich war, zeigen dir die
        <span style="color:grey">grauen Markierungen</span>, wo sich jeweils der
        Durchschnitt aller Texte bzw. ähnlicher Texte befindet. Sie sind durch
        eine
        <span style="color:grey; text-decoration: underline dashed grey"
          >grau gestrichelte Durchschnitts-Linie
        </span>
        verbunden.
        <br /><br />
        Das Dreieck markiert zusätzlich das Ausmaß der Abweichung des Textes vom
        Durchschnitt. <br />
        &#9650; Zeigt es nach oben, macht ein Text vergleichweise viele Bestellungen
        für vergleichweise wenig Visits. <br />
        &#9660; Zeigt es nach unten, gibt es wenige Abos für vergleichsweise mehr
        Visits. <br /> Die Farbe der Markierungen und des Dreiecks hängt von
        ihrer Position auf der Skala ab. <br />
        Falls du kein Dreieck, sondern ein flaches Rechteck siehst, liegen die Werte in der Grafik zu nahe beieinander, um ein interpretierbares
        Dreieck zu bilden.
        <br /><br />
        <span style="font-weight:800">Interaktion:</span><br />
        Wenn du über die Markierungen auf den Skalen hoverst, kannst du die genauen
        Werte ablesen.
      </p>
    {:else if type == 1}
      <p>
        <span style="font-weight:800">How to read:</span><br />
        Grundlage dieser Visualisierung ist eine Sammlung von Texten, die dem gewählten
        thematisch ähneln. Du kannst also die Performance des Textes seinem Themenbereich
        vergleichen. Zu Beginn ist der von dir gewählte Text mit einem
        <span style="background-color:#f2efaa; border: 1px solid #f7eb02"
          >gelben Rechteck</span
        >
        markiert. Alle Texte sind nach Erscheinungsdatum von links nach rechts angeordnet.

        <br /><br />
        Es handelt sich um einen sogenannten Lollipop-Chart. Zu jedem Text gehört
        also eine Struktur, die einem Lollipop ähnelt. Je länger der "Stiel", desto
        mehr Visits oder Abos hat ein Text gesammelt.
        <br /><br />
        <span style="color:blue">Visits &#9632; </span>: Je weiter der
        <span style="color:blue">blaue, quadratische</span>
        Lollipop über der Null-Linie liegt, desto mehr Visits hat ein Text.<br
        />
        <span style="color:hotpink">Abos &#9679;</span>: Je weiter der
        <span style="color:hotpink">pinke, runde</span>
        Lollipop nach unten reicht, desto mehr Abos hat ein Text gemacht. Bleibt
        der Kreis grau, gab es keine Abos.

        <br /><br />
        <span style="font-weight:800">Interaktion:</span><br />
        Du kannst auch andere Texte genauer untersuchen, indem du den
        <span
          style="background-color:#f2efaa; border: 1px solid #f7eb02; padding:2px"
          >Fokus</span
        >
        verschiebst oder auf einen Lollipop klickst, der einen interessanten Text
        visualisiert. Unterhalb kannst du dann exakten Werte ablesen sowie eine Vorschau
        des gewählten Artikels sehen.
        <br /><br />
        Wenn es sehr viele ähnliche Texte gibt, können immer nur ein paar davon gezeigt
        werden und es gibt unter der Grafik eine Übersichtsleiste, die dir den aktuellen
        Ausschnitt zeigt. Du kannst ihn verschieben, um andere Texte zu sehen.
      </p>
    {:else if type == 2}
      <span style="font-weight:800">How to read:</span><br />
      Du siehst eine Grafik für Visits und gegebenenfalls eine für Abos. <br />
      Das gesamte Rechteck repräsentiert alle Visits bzw. Abos. Es ist in farbige
      Segmente unterteilt, die jeweils den Anteil einer User-Gruppe am Traffic visualisieren.<br
      />
      Je breiter also ein Segment, desto stärker war eine User-Gruppe vertreten.
      <br /><br />

      <span class="flyby">Flybys:</span> Lesen beim Besuch auf der Seite nur
      höchstens einen Artikel.<br />
      <span class="casual_reader">Casual Reader:</span> Kommen nur selten auf
      die Seiten und lesen dann nur wenige Texte<br />
      <span class="brand_lover">Brand Lover:</span> Besuchen die Seite oft und
      lesen dann auch viele Texte. Sie sind häufig treue Leser*innen.<br />
      <span class="deep_diver">Deep Diver:</span> Kommen selten auf die Seite,
      lesen dann aber viel auf einmal.<br />
      <span class="fast_mover">Fast Mover:</span> Sind häufig auf der Seite,
      lesen dann aber nur wenige Texte. <br />

      <br /><br />
      <span style="font-weight:800">Interaktion:</span><br />
      Wenn du über die Flächen hoverst, kannst Du die genauen Werte ablesen.
      <br /><br />
      Über die Checkboxen unten kannst Du auch einzelne Leser*innen-Gruppen aus-
      und wieder einblenden. Das kann z.B. für die
      <span class="flyby">Flybys</span> interessant sein, da sie einen großen Teil
      der Besuche auf ZON ausmachen, aber selten nachhaltige Abos eingehen.
    {:else if type == 3}
      <p>
        <span style="font-weight:800">How to read:</span><br />
        Du siehst zwei Grafiken, die jeweils den zeitlichen Verlauf des Traffics
        auf dem ausgewählten Text seit Anfang 2023 visualisieren:<br />
        Eine mit <span style="color:blue">blauem Graph für Visits </span> und
        gegebenenfalls eine mit
        <span style="color:hotpink">pinkem für Abos.</span> Der rote Punkt zeigt
        dir das Veröffentlichungsdatum des von dir gewählten Textes (sofern das
        nicht vor 2023 war).

        <br /><br /> Die Timeline beginnt beim ersten Tag mit registrierten
        Visits auf dem gewählten Text, frühestens aber im Januar 2023.

        <br /><br />
        <span style="font-weight:800">Interaktion:</span><br />
        Wenn du bestimmte Zeiträume genauer untersuchen willst, kannst du in die
        Grafiken hineinzoomen.
        <br /><br />
        Falls dich ein Aspekt nicht interessiert, kannst du ihn mit Klick auf die
        Überschrift ausblenden.
      </p>
    {/if}
  </details>
</div>

<style>
  .explanation-wrapper {
    background-color: #eee;
    border-radius: 10px;
    padding: 10px;
  }

  .gradient-text {
    background: linear-gradient(90deg, #bfb825, rgb(241, 39, 134), #4d1b98);
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
  }
</style>
