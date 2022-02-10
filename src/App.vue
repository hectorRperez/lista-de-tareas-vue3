<template>
	<TaskList />
</template>

<script>
import TaskList from './components/TaskList.vue';

export default {
	name: 'App',
	components: {
		TaskList,
	},
};
</script>

<style lang="scss">
#app {
	-moz-osx-font-smoothing: grayscale;
	-webkit-font-smoothing: antialiased;
	color: #2c3e50;
	font-family: 'Roboto', sans-serif;
	margin-top: 60px;
	text-align: center;
}
</style>
