<script setup>
import { ref } from "vue";
import { createClient } from "@prismicio/client";

const docs = ref([]);

const client = createClient("route-n-link-resolver", {
  routes: [
    {
      type: "page",
      path: "/:uid"
    },
    {
      type: "service",
      path: "/services/:uid"
    },
    {
      type: "post",
      path: "/post/:taxonomy/:uid",
      resolvers: {
        taxonomy: "taxonomy"
      }
    }
  ]
});

(async () => {
  const raw = await
    client.dangerouslyGetAll({ lang: "*" });

  // Filter taxonomy, sort by lang, then by type
  docs.value = raw
    .filter(doc => doc.type !== "taxonomy")
    .sort((a, b) => a.type > b.type ? -1 : 1)
    .sort((a, b) => a.lang > b.lang ? 1 : -1)
})();
</script>

<template>
  <h1>Route Resolver Demo</h1>
  <table>
    <thead>
      <tr>
        <th>type</th>
        <th>lang</th>
        <th>uid</th>
        <th>url</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="doc in docs" :key="doc.id">
        <td>{{ doc.type }}</td>
        <td>{{ doc.lang }}</td>
        <td>{{ doc.uid }}</td>
        <td>{{ doc.url }}</td>
      </tr>
    </tbody>
  </table>
</template>

<style>
body {
  font-family: sans-serif;
}

td, th {
  border: 1px solid #000000;
  font-size: 1.25rem;
}

table {
  border-collapse: collapse;
}
</style>