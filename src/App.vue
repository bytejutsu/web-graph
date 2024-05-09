<script setup lang="ts">
import { ref, watchEffect, reactive } from "vue"
import * as vNG from "v-network-graph"
import {Nodes, Edges, VNetworkGraph} from "v-network-graph"
import data from "./data"
import { Graph }  from "./Graph"

const currentYear = ref(new Date().getFullYear());

const nodes: Nodes = reactive({ ...data.nodes });
const edges: Edges = reactive({ ...data.edges });

const nextNodeIndex = ref(Object.keys(nodes).length + 1);
const nextEdgeIndex = ref(Object.keys(edges).length + 1);

const selectedNodes = ref<string[]>([]);
const selectedEdges = ref<string[]>([]);

function addNode() {
  const nodeId = `node${nextNodeIndex.value}`;
  //const name = `N${nextNodeIndex.value}`;
  const name = ``;
  nodes[nodeId] = { name };
  nextNodeIndex.value++;
}

function removeNode() {
  for (const nodeId of selectedNodes.value) {
    delete nodes[nodeId]
  }
}

function addEdge() {
  if (selectedNodes.value.length !== 2) return
  const [source, target] = selectedNodes.value;
  const edgeId = `edge${nextEdgeIndex.value}`;
  edges[edgeId] = { source, target };
  nextEdgeIndex.value++;
}

function removeEdge() {
  for (const edgeId of selectedEdges.value) {
    delete edges[edgeId]
  }
}

const graphKey = ref(0);

const paths = ref<vNG.Paths>({});

const targetNode = ref("node12");

watchEffect(() => {

  const graph = new Graph(data.edges);
  const routeOfNodes = graph.findShortestPath(["node1", targetNode.value]);

  if (routeOfNodes) {
    const routeOfEdges = graph.convertNodesToEdges(routeOfNodes);
    paths.value = { shortestPath: { edges: routeOfEdges } };
  }

  console.log(paths.value);

  graphKey.value += 1;


});

const eventHandlers: vNG.EventHandlers = {
  "node:pointerover": ({ node }) => {

    if (node === "node1") return

    targetNode.value = node;
  },
}

</script>

<template>
  <body class="flex flex-col h-screen">
    <nav class="relative flex w-full flex-wrap items-center justify-between bg-blue-500 shadow-dark-mild mb-4">
      <div class="flex w-full flex-wrap items-center justify-between">
        <img src="../images/Flag_of_Tunisia.svg" alt="Flag of Tunisia" width="50" height="20"/>
        <div class="flex">
          <div>
            <label>Node:</label>
            <el-button @click="addNode">add</el-button>
            <el-button :disabled="selectedNodes.length == 0" @click="removeNode">remove</el-button>
          </div>
          <div>
            <label>Edge:</label>
            <el-button :disabled="selectedNodes.length != 2" @click="addEdge">add</el-button>
            <el-button :disabled="selectedEdges.length == 0" @click="removeEdge">remove</el-button>
          </div>
        </div>
        <span class="text-white mr-2 font-bold">Web Graph</span>
      </div>
    </nav>
    <div class="flex-1" style="width:100vw;">
      <v-network-graph
          :nodes="nodes"
          :edges="edges"
          :paths="paths"
          :layouts="data.layouts"
          :configs="data.configs"
          :event-handlers="eventHandlers"
          :key="graphKey"
      >
        <template #edge-label="{ edge, ...slotProps }">
          <v-edge-label
              :text="`${edge.cost}`"
              align="center"
              vertical-align="below"
              v-bind="slotProps"
          />
        </template>
      </v-network-graph>
    </div>
    <footer class="flex flex-col sm:flex-row justify-between w-full bg-black/5">
      <!-- Author -->
      <div class="p-6 text-center sm:text-left">
        <span>Author:</span>
        <span class="font-semibold">Dhia Nabli</span>
      </div>
      <!-- Copyright section -->
      <div class="p-6 text-center sm:text-center">
        <span>© {{ currentYear }} Copyright:</span>
        <a class="font-semibold" href="https://bytejutsu.com/">ByteJutsu</a>
      </div>
      <!-- Email -->
      <div class="p-6 text-center sm:text-right">
        <a class="font-semibold" href="mailto:bytejutsuproject@gmail.com">bytejutsuproject@gmail.com</a>
      </div>
    </footer>
  </body>
</template>