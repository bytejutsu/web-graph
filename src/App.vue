<script setup lang="ts">
import { reactive, ref } from "vue"
import {Nodes, Edges, VNetworkGraph} from "v-network-graph"
import data from "./data.ts"

const nodes: Nodes = reactive({ ...data.nodes })
const edges: Edges = reactive({ ...data.edges })
const nextNodeIndex = ref(Object.keys(nodes).length + 1)
const nextEdgeIndex = ref(Object.keys(edges).length + 1)

const selectedNodes = ref<string[]>([])
const selectedEdges = ref<string[]>([])

function addNode() {
    const nodeId = `node${nextNodeIndex.value}`
    const name = `N${nextNodeIndex.value}`
    nodes[nodeId] = { name }
    nextNodeIndex.value++
}

function removeNode() {
    for (const nodeId of selectedNodes.value) {
        delete nodes[nodeId]
    }
}

function addEdge() {
    if (selectedNodes.value.length !== 2) return
    const [source, target] = selectedNodes.value
    const edgeId = `edge${nextEdgeIndex.value}`
    edges[edgeId] = { source, target }
    nextEdgeIndex.value++
}

function removeEdge() {
    for (const edgeId of selectedEdges.value) {
        delete edges[edgeId]
    }
}
</script>

<template>
    <div class="demo-control-panel bg-green-400">
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
</template>