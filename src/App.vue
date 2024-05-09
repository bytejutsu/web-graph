<script setup lang="ts">
import { reactive, ref } from "vue"
import {Nodes, Edges, VNetworkGraph} from "v-network-graph"
import data from "./data.ts"

const nodes: Nodes = reactive({ ...data.nodes });
const edges: Edges = reactive({ ...data.edges });
const nextNodeIndex = ref(Object.keys(nodes).length + 1);
const nextEdgeIndex = ref(Object.keys(edges).length + 1);

const selectedNodes = ref<string[]>([]);
const selectedEdges = ref<string[]>([]);

function addNode() {
    const nodeId = `node${nextNodeIndex.value}`;
    const name = `N${nextNodeIndex.value}`;
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
</script>

<template>
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
    <div style="height:100vh;width:100vw;">
        <v-network-graph
            v-model:selected-nodes="selectedNodes"
            v-model:selected-edges="selectedEdges"
            :nodes="nodes"
            :edges="edges"
            :layouts="data.layouts"
            :configs="data.configs"
        />
    </div>
    <footer class="flex justify-between w-full fixed bottom-0 bg-black/5">
      <!-- Author -->
      <div class="p-6 text-left flex-1">
        <span>Author:</span>
        <span class="font-semibold">Dhia Nabli</span>
      </div>
      <!-- Copyright section -->
      <div class="p-6 text-center flex-1">
        <span>© 2024 Copyright:</span>
        <a class="font-semibold" href="https://bytejutsu.com/">ByteJutsu</a>
      </div>
      <!-- Email -->
      <div class="p-6 text-right flex-1">
        <a class="font-semibold" href="mailto:bytejutsuproject@gmail.com">bytejutsuproject@gmail.com</a>
      </div>
    </footer>
</template>