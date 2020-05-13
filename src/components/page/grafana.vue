<template>
    <div style="width: 100%;height: 100%">
        <iframe id="grafanaFrame" src="http://172.16.200.4:3000/d/taQlRuxik/k8s-cluster-summary" frameborder="0" width="100%" height="100%" ></iframe>
    </div>
</template>

<script>
    export default {
        name: "grafana"
    }
</script>

<style scoped>

</style>