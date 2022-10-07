<template>
<div v-if="loadedMoveData">
	<v-card tile outlined
		@click="expand = !expand"
		:color="getMoveData.type.name"
		class="justify-center pa-2 d-flex rounded"
	>
		<v-row>
			<v-col cols="8" class="d-flex justify-center">
				{{ capitalize(getMoveData.name).replace("-", " ") }}
			</v-col>
			<v-col cols="4" class="d-flex justify-center">
				{{ capitalize(getMoveData.damage_class.name) }}
			</v-col>
		</v-row>
	</v-card>

	<v-expand-transition>
		<div v-show="expand">
			<v-card tile outlined
				class="pa-2 rounded-b"
			>
				<v-row>
					<v-col cols="12" class="d-flex justify-center mt-2">{{ getMoveData.effect_entries[0].effect }}</v-col>

					<v-col cols="12" sm="6"
						v-for="item in moveData" :key="item.name[getMoveData.name]"
					>
						<v-card color="primary" class="pa-2 rounded">{{ capitalize(item.name) }}: {{ (getMoveData[item.name] === null) ? "-" : getMoveData[item.name] }}</v-card>
					</v-col>
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
		moveData: [
			{ name: "accuracy" },
			{ name: "power" },
			{ name: "pp" },
			{ name: "priority" },
		],

		// axios placeholders
		getMoveData: null,
		loadedMoveData: false,
	}),

	beforeMount() {
		this.getMoves();
	},

	methods: {
		// capitalizes the first letter of a word
		capitalize(string) {
			if (string === "pp") {
				return string.toUpperCase()
			} else {
				return string.charAt(0).toUpperCase() + string.slice(1);
			}
		},

		// axios
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
	},

	props: {
		moveId: Number,
	},
}
</script>