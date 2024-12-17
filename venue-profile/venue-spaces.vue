<template>
  <Card class="venue-spaces-card">
    <template #title>
      <h2>Venue Spaces</h2>
    </template>
    <template #content>
      <OrganizationChart :value="spaces" class="organization-chart" />
      <div class="add-space-section">
        <input v-model="newSpaceLabel" placeholder="Add New Space" class="space-input" />
        <button @click="addSpace('Main Venue', newSpaceLabel)" class="add-space-button">Add Space</button>
      </div>
    </template>
  </Card>
</template>

<script setup lang="ts">
import { ref } from "vue";
import Card from "primevue/card";
import OrganizationChart from "primevue/organizationchart";

interface Node {
  label: string;
  children?: Node[];
}

const spaces = ref<Node[]>([
  {
    label: "Main Venue",
    children: [
      {
        label: "Hall A",
        children: [{ label: "Section A1" }, { label: "Section A2" }],
      },
      {
        label: "Hall B",
        children: [{ label: "Section B1" }, { label: "Section B2" }],
      },
      {
        label: "Outdoor Area",
      },
    ],
  },
  {
    label: "Conference Rooms",
    children: [
      {
        label: "Room 101",
      },
      {
        label: "Room 102",
        children: [{ label: "Section 102A" }, { label: "Section 102B" }],
      },
    ],
  },
]);

const newSpaceLabel = ref("");

function addSpace(parentLabel: string, spaceLabel: string) {
  if (parentLabel === "Main Venue") {
    let parent = findNode(spaces.value, parentLabel);
    if (!parent) {
      parent = { label: parentLabel, children: [] };
      spaces.value.push(parent);
    }

    if (!parent.children) {
      parent.children = [];
    }
    parent.children.push({ label: spaceLabel });
    newSpaceLabel.value = ""; // Clear the input field
  }
}

function removeSpace(spaceLabel: string) {
  const parent = findParentNode(spaces.value, spaceLabel);
  if (parent) {
    parent.children = parent.children.filter((child) => child.label !== spaceLabel);
  }
}

function findNode(nodes: Node[], label: string): Node | undefined {
  for (const node of nodes) {
    if (node.label === label) {
      return node;
    }
    if (node.children) {
      const childNode = findNode(node.children, label);
      if (childNode) {
        return childNode;
      }
    }
  }
  return undefined;
}

function findParentNode(nodes: Node[], label: string): Node | undefined {
  for (const node of nodes) {
    if (node.children) {
      const index = node.children.findIndex((child) => child.label === label);
      if (index !== -1) {
        return node;
      }
      const childParent = findParentNode(node.children, label);
      if (childParent) {
        return childParent;
      }
    }
  }
  return undefined;
}
</script>

<style scoped>
.venue-spaces-card {
  width: 100%;
}

.organization-chart {
  margin-top: 1rem;
}

.add-space-section {
  display: flex;
  align-items: center;
  margin-top: 1rem;
}

.space-input {
  flex-grow: 1;
  padding: 0.5rem;
  margin-right: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.add-space-button {
  padding: 0.5rem 1rem;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.add-space-button:hover {
  background-color: #0056b3;
}
</style>
