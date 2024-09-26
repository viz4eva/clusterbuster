<script>
  import { onMount, createEventDispatcher } from "svelte";
  import * as d3 from "d3";
  import { last_cluster } from "../stores";
  import { get } from "svelte/store";
  import Spinner from "./Spinner.svelte";
  //import { PUBLIC_API_URL } from "$env/static/public";

  export let expertMode;
  let cluster;
  let similarGraph;
  const width = 300;
  const height = 300;
  let initial = true;
  let loading = false;
  let size;
  let articles = [];
  let data;
  let selectedCluster;
  let label;

  //ähnliche Cluster fetchen
  onMount(() => {
    loading = true;
    //get last loaded cluster for similar search
    const prevClusterData = get(last_cluster);
    cluster = parseInt(prevClusterData.cluster_id);

    //static data fetching for demo
    let exampleArticle = "kinder";
    if (cluster === 4005) {
      exampleArticle = "denunziant";
    }

    fetch(`./example_data/${exampleArticle}/similarity_data.json`)
      .then((res) => res.json())
      .then((graphData) => {
        console.log("data", graphData);
        data = graphData;
        articles = graphData.nodes[0].articles;
        size = graphData.nodes[0].size;
        selectedCluster = graphData.nodes[0].cluster_id;
        label = graphData.nodes[0].label[0].label;

        const sizeScale = d3.scaleSqrt(
          [
            d3.min(graphData.nodes.map((elem) => elem.size)),
            d3.max(graphData.nodes.map((elem) => elem.size)),
          ],
          [3, 30]
        );

        const svg = d3.select(similarGraph);
        const node = svg
          .append("g")
          .selectAll()
          .data(graphData.nodes)
          .join("circle")
          .classed(`similar_clusters_${cluster}`, true)
          .attr("r", (d) => sizeScale(d.size))
          .attr("fill", (d) => {
            if (d.cluster_id == cluster) {
              return "orange";
            }
            return "grey";
          })
          .attr("opacity", 0.7)
          .on("click", (e, d) => switchSelection(e, d));

        //build similarity graph
        const simulation = d3
          .forceSimulation(graphData.nodes)
          .force(
            "link",
            d3
              .forceLink(graphData.edges)
              .id((d) => d.cluster_id)
              .strength(0.5)
              .distance(width / 5)
          )
          .force("charge", d3.forceManyBody().strength(-250))
          .force("center", d3.forceCenter(width / 2, height / 2))
          .on("tick", ticked);

        function ticked() {
          node.attr("cx", (d) => d.x).attr("cy", (d) => d.y);
        }
        loading = false;
      });
  });

  function switchSelection(e, d) {
    size = d.size;
    articles = d.articles;
    if (d.label[0]) {
      label = d.label[0].label;
    } else {
      label = "noch kein Themenlabel vorhanden";
    }
    initial = false;
    selectedCluster = d.cluster_id;

    d3.selectAll(`.similar_clusters_${cluster}`).attr("fill", (d) => {
      if (d.cluster_id == cluster) {
        return "orange";
      }
      return "grey";
    });
    d3.select(e.currentTarget).attr("fill", "tomato");
  }

  let focusText;
  function setFocusText(event) {
    focusText = event.currentTarget.value;
  }

  const dispatch = createEventDispatcher();
  function dispatchClusterInput() {
    if (focusText) {
      dispatch("clusterInput", {
        focusText: focusText,
        cluster: selectedCluster,
      });
    } else {
      alert("Wähle einen Text aus");
    }
  }
</script>

<div class="similar-wrapper">
  <svg bind:this={similarGraph} id="bubble-vis" {width} {height}></svg>
  <div id="similar-tooltip">
    {#if loading}
      <Spinner message={"Suche nach ähnlichen Texten..."} />
    {:else}
      <div class="explanation-wrapper">
        <details open={!expertMode}>
          <summary>?</summary>
          <p>
            Der gerade analysierte Text befindet sich im <span
              style="color:orange">orange</span
            >
            markierten Themenbereich, der hier durch einen Kreis repräsentiert wird.
            Diese Texte sind dem gewählten Text am ähnlichsten. <br /> Zusätzlich
            siehst du weitere Themenbereiche, sogenannte Cluster, die ähnlich zu
            diesem sind. Klicke auf einen anderen Cluster, um die enthaltenen Texte
            zu sehen.
          </p>

          <details>
            <summary>Wie werden Cluster bestimmt?</summary>
            <p>
              Alle ZON-Texte werden regelmäßig einem Clustering unterzogen. Das
              bedeutet, dass Texte mit ähnlichen Themen, in einer Gruppe, einem
              sogenannten Cluster, zusammengefasst werden. <br /> Diese Cluster
              können ziemlich groß werden (mehrere Tausend Texte) oder auch sehr
              klein bleiben. Bei speziellen Themen oder sehr neuen Texten ist es
              manchmal nur ein einzelner Text. <br /><br /> Der Cluster, dem der
              ausgewählte Text zugeordnet wurde, ist die Grundlage für die Daten,
              die dir angezeigt werden wenn du in diesem Tool "ähnliche Texte" oder
              "Texte zu diesem Thema" analysierst.
            </p>
          </details>
        </details>
      </div>

      Der gewählte Themenbereich enthält {size} ähnliche Texte zum Thema:
      <span style="font-weight:700">{label}</span>
      <div class="article-list">
        {#each articles as article}
          <div class="articlelist-elem">
            <input
              type="radio"
              id={article.page}
              name="focus-text"
              value={article.page}
              class="radio-{cluster}"
              on:change={setFocusText}
            />
            <label for={article.page}
              ><a href={article.page} target="_blank"
                >{article.page.split("www.")[1]}</a
              ></label
            >
          </div>
        {/each}
      </div>
      <!--This button has been disabled for the demo prototype-->
      <button on:click={dispatchClusterInput} disabled>Diesen Text ansehen</button>
      <p>In dieser Demo-Version können keine neuen Texte geladen werden.</p>
    {/if}
  </div>
</div>

<style>
  .similar-wrapper {
    margin: 1rem;
    display: grid;
    grid-template-columns: 40% 60%;
    background-color: #fff;
    border-radius: 10px;
  }

  #similar-tooltip {
    border: 1px #eee solid;
    border-radius: 10px;
    margin: 10px;
    padding: 20px;
  }

  .article-list {
    height: 20vh;
    overflow-y: scroll;
    border: solid #eee 1px;
    border-radius: 10px;
    padding: 2px;
    margin-top: 10px;
    margin-bottom: 10px;
  }

  .articlelist-elem {
    padding-bottom: 5px;
    display: grid;
    grid-template-columns: 10% 90%;
  }

  input[type="radio"] {
    margin: auto;
  }

  .explanation-wrapper {
    background-color: #eee;
    border-radius: 10px;
    padding: 10px;
    margin-bottom: 20px;
  }
</style>
