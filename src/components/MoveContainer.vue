<template>
<div v-if="loadedMoveData">
	<v-card tile outlined
		@click="expand = !expand"
		:color="getMoveData.type.name"
		class="justify-center pa-2 d-flex rounded"
	>
		{{ capitalize(getMoveData.name).replace("-", " ") }}
	</v-card>

	<v-expand-transition>
		<div v-show="expand">
			<v-card tile outlined
				:color="getMoveData.type.name"
				class="pa-2 rounded-b"
			>
				<v-row>
					<v-col cols="12">Class: {{ getMoveData.damage_class.name }}</v-col>

					<v-col cols="6"><v-card class="pa-1 rounded">Accuracy: {{ getMoveData.accuracy }}</v-card></v-col>

					<v-col cols="6"><v-card class="pa-1 rounded">Power: {{ getMoveData.power }}</v-card></v-col>
					
					<v-col cols="6"><v-card class="pa-1 rounded">PP: {{ getMoveData.pp }}</v-card></v-col>
					
					<v-col cols="6"><v-card class="pa-1 rounded">Priority: {{ getMoveData.priority }}</v-card></v-col>
				</v-row>
			</v-card>
		</div>
	</v-expand-transition>
</div>
</template>

<script>
import axios from "axios";

export default {
	name: "MoveContainer",

	data: () => ({
		expand: false,

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