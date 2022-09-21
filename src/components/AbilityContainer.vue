<template>
<div v-if="loadedAbilityData">
	<v-card color="primary" outlined tile class="justify-center pa-2 d-flex rounded-t">
		{{ capitalize(getAbilityData.name).replace("-", " ") }}
	</v-card>
	
	<div v-for="effect_entries in getAbilityData.effect_entries" :key="effect_entries.effect">
		<v-card v-if="effect_entries.language.name.includes('en')" tile class="pa-2 rounded-b">
			{{ effect_entries.effect }}
		</v-card>
	</div>
</div>
</template>

<script>
import axios from "axios";

export default {
	name: "AbilityContainer",

	data: () => ({

		// axios
		getAbilityData: null,
		loadedAbilityData: false,
	}),

	props: {
		abilityId: Number,
	},

	beforeMount() {
		this.getAbilities();
	},

	methods: {
		capitalize(string) {
			return string.charAt(0).toUpperCase() + string.slice(1);
		},

		getAbilities() {
			axios
				.get(`https://pokeapi.co/api/v2/ability/${this.abilityId}/`)
				.then((response) => {
					this.getAbilityData = response.data;
					this.loadedAbilityData = true;
				})
				.catch((error) => {
					console.error(error);
				});
		}
	}
}
</script>