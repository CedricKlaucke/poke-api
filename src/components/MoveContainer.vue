<template>
<div v-if="loadedMoveData">
	<v-card :color="getMoveData.type.name" outlined class="justify-center pa-2 d-flex">
		{{ capitalize(getMoveData.name).replace("-", " ") }}
	</v-card>
</div>
</template>

<script>
import axios from "axios";

export default {
	name: "MoveContainer",

	data: () => ({

		// axios
		getMoveData: null,
		loadedMoveData: false,
	}),

	props: {
		moveId: Number,
	},

	beforeMount() {
		this.getMoves();
	},

	methods: {
		capitalize(string) {
			return string.charAt(0).toUpperCase() + string.slice(1);
		},

		getMoves() {
			axios
				.get(`https://pokeapi.co/api/v2/move/${this.moveId}/`)
				.then((response) => {
					this.getMoveData = response.data;
					this.loadedMoveData = true;
				})
				.catch((error) => {
					console.error(error);
				});
		}
	}
}
</script>