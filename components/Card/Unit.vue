<template>
	<div class="card card-container">
		<div class="card-title">
			{{ unit._id }}
		</div>
		<h4>
			<b-badge variant="danger">
				{{ unit.condition }}
			</b-badge>
		</h4>
	</div>
</template>

<script>
export default {
	props: {
		unit: {},
	},
};
</script>

<style scoped>
	.card-container {
		display: flex;
		justify-content: flex-start;
		align-items: center;
	}

	.card {
		padding: 25px;
	}

    .card-image img {
        width: 200px;
        height: 200px;
    }
</style>
