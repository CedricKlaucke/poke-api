<template>
<v-card outlined shaped width="1000" min-height="334" class="overflow-hidden ma-4">
	<v-row no-gutters>
		<v-col cols="8" min-height="334">
			<v-row no-gutters height="64">
				<v-card-title>{{ pokemon.entry_number }} - {{ capitalize(pokemon.pokemon_species.name).replace("Nidoran-f", "Nidoran ♀").replace("Nidoran-m", "Nidoran ♂").replace("Tapu-", "Tapu ") }}</v-card-title>

				<v-spacer/>

				<div v-if="loadedMonData" class="mr-1 align-center d-flex">
					<div v-for="item in getMonData.types" :key="item.type.name[pokemon.pokemon_species.name]">
						<v-card class="justify-center ma-2 pa-2 d-flex" min-width="75" :color="item.type.name">
							{{ capitalize(item.type.name) }}
					</v-card>
					</div>
				</div>
			</v-row>

			<v-divider/>

			<v-row class="ma-2" v-if="loadedMonData">
				<v-col cols="6" v-for="stats in getMonData.stats" :key="stats.stat.name[pokemon.pokemon_species.name]">
					{{ capitalize(stats.stat.name).replace("-", " ") }}: {{ stats.base_stat }}
				</v-col>
				<v-col cols="6">
					Height: {{ getMonData.height / 10 }}m
				</v-col>
				<v-col cols="6">
					Weight: {{ getMonData.weight / 10 }}kg
				</v-col>
			</v-row>
			<v-btn @click="expand = !expand" tile class="pokemonShowBtn">Show {{ expand ? "less" : "more" }} info</v-btn>
		</v-col>

		<v-divider vertical/>

		<v-col cols="4" v-if="loadedMonData">
			<v-card tile class="pokemonImg">
				<v-img v-if="!pokemonImgBack && !pokemonImgShiny" :src="getMonData.sprites.front_default"/>
				<v-img v-if="pokemonImgBack && !pokemonImgShiny" :src="getMonData.sprites.back_default"/>
				<v-img v-if="!pokemonImgBack && pokemonImgShiny" :src="getMonData.sprites.front_shiny"/>
				<v-img v-if="pokemonImgBack && pokemonImgShiny" :src="getMonData.sprites.back_shiny"/>
				<v-row no-gutters class="pokemonImgBtn">
					<v-col cols="2">
						<v-tooltip right>
							<template v-slot:activator="{ on, attrs }">
								<v-btn tile block class="rounded-br-xl"
									@click="pokemonImgBack = !pokemonImgBack"
									v-bind="attrs"
									v-on="on"
								>
									<v-icon>mdi-arrow-u-right-bottom</v-icon>
								</v-btn>
							</template>
							<span>Turn around</span>
						</v-tooltip>
					</v-col>

					<v-spacer/>

					<v-col cols="2">
						<v-tooltip left>
							<template v-slot:activator="{ on, attrs }">
								<v-btn tile block class="rounded-bl-xl"
									@click="pokemonImgShiny = !pokemonImgShiny"
									v-bind="attrs"
									v-on="on"
								>
									<v-icon>mdi-white-balance-sunny</v-icon>
								</v-btn>
							</template>
							<span>Toggle shiny</span>
						</v-tooltip>
					</v-col>
				</v-row>
			</v-card>
		</v-col>
	</v-row>

	<v-expand-transition>
		<v-expansion-panels accordion v-show="expand">
			<v-expansion-panel/>

			<v-expansion-panel>
				<v-expansion-panel-header>Abilities</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-row v-if="loadedMonData">
						<v-col cols="6" v-for="abilities in getMonData.abilities" :key="abilities.ability.name[pokemon.entry_number]">
							<AbilityContainer
								:abilityId="Number(abilities.ability.url.slice(34, -1))"
							/>
						</v-col>
					</v-row>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Available moves</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-row v-if="loadedMonData">
						<v-col cols="4" v-for="moves in getMonData.moves" :key="moves[pokemon.entry_number]">
							<MoveContainer
								:moveId="Number(moves.move.url.slice(31, -1))"
							/>
						</v-col>
					</v-row>
				</v-expansion-panel-content>
			</v-expansion-panel>
		</v-expansion-panels>
	</v-expand-transition>
</v-card>
</template>

<script>
import axios from "axios"
import MoveContainer from "@/components/MoveContainer.vue";
import AbilityContainer from "@/components/AbilityContainer.vue";

export default {
	name: "PokemonContainer",

	data: () => ({
		expand: false,
		pokemonImgBack: false,
		pokemonImgShiny: false,

		// axios
		getMonData: null,
		loadedMonData: false,
	}),

	props: {
		pokemonId: Number,
		pokemon: Object,
	},

	beforeMount() {
		this.getPokemon();
	},

	methods: {
		capitalize(string) {
			return string.charAt(0).toUpperCase() + string.slice(1);
		},

		getPokemon() {
			axios
				.get(`https://pokeapi.co/api/v2/pokemon/${this.pokemonId}/`)
				.then((response) => {
					this.getMonData = response.data;
					this.loadedMonData = true;
				})
				.catch((error) => {
					console.error(error);
				});
		}
	},
	
	components: {
		MoveContainer,
		AbilityContainer,
	},
}
</script>

<style>
.pokemonImg {
	margin-left: 1px;
}

.pokemonImgBtn {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
}

.pokemonShowBtn {
	position: absolute;
	top: 280px;
}
</style>