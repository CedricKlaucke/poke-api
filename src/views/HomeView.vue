<template>
<v-card dark tile class="pb-16 fill-width fill-height">
	<v-app-bar>
		<v-tooltip right color="primary" content-class="custom-tooltip">
			<template v-slot:activator="{ on, attrs }">
				<v-app-bar-nav-icon @click="drawer = true" v-bind="attrs" v-on="on" />
			</template>
			<span>Change Pokedex</span>
		</v-tooltip>

		<v-toolbar-title v-if="loadedMonData">{{ capitalize(getMonData.name).replace("-", " ").replace("Letsgo", "Let's Go") }} Pokedex</v-toolbar-title>
		
		<v-spacer/>

		<v-menu dark offset-y>
			<template v-slot:activator="{ on, attrs }">
				<v-btn
					color="primary"
					dark
					v-bind="attrs"
					v-on="on"
				>
					Pokemon Shown
				</v-btn>
			</template>
			<v-list>
				<v-list-item
					v-for="(item, index) in 4"
					:key="'menu ' + index"
					@click="numberShown = item * 5"
				>
					<v-list-item-title>{{ item * 5 }}</v-list-item-title>
				</v-list-item>

				<v-row class="align-center">
					<v-divider class="mx-2" />
					Custom: {{ numberShown }}
					<v-divider class="mx-2" />
				</v-row>

				<v-list-item class="mt-2">
					<v-slider hide-details min="1" max="25" v-model="numberShown" />
				</v-list-item>
			</v-list>
		</v-menu>
	</v-app-bar>

	<v-navigation-drawer
		v-model="drawer"
		absolute
		bottom
		temporary
	>
		<v-expansion-panels v-if="loadedDexData" tile accordion>
			<v-expansion-panel class="d-flex justify-center">
				<v-card-title>Pokedex version</v-card-title>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-btn class="text-none text-left pl-0 my-2" block text @click="pokedexId = Number(getDexData.results[0].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
					<v-card-text>National</v-card-text>
				</v-btn>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Kanto</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[1].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Kanto</v-card-text>
					</v-btn>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[24].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Let's Go Kanto</v-card-text>
					</v-btn>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Johto</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[2].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Original Johto</v-card-text>
					</v-btn>

					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[6].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Updated Johto</v-card-text>
					</v-btn>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Hoenn</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[3].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Original Hoenn</v-card-text>
					</v-btn>

					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[13].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Updated Hoenn</v-card-text>
					</v-btn>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Sinnoh</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[4].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Original Sinnoh</v-card-text>
					</v-btn>

					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[5].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Extended Sinnoh</v-card-text>
					</v-btn>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Unova</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[7].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Original Unova</v-card-text>
					</v-btn>

					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[8].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Updated Unova</v-card-text>
					</v-btn>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-btn class="text-none text-left pl-0 my-2" block text @click="pokedexId = Number(getDexData.results[9].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
					<v-card-text>Conquest Gallery</v-card-text>
				</v-btn>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Kalos</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[10].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Kalos Central</v-card-text>
					</v-btn>

					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[11].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Kalos Coastal</v-card-text>
					</v-btn>

					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[12].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Kalos Mountain</v-card-text>
					</v-btn>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Alola</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[14].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Original Alola</v-card-text>
					</v-btn>

					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[19].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Updated Alola</v-card-text>
					</v-btn>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Melemele</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[15].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Original Melemele</v-card-text>
					</v-btn>

					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[20].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Updated Melemele</v-card-text>
					</v-btn>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Akala</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[16].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Original Akala</v-card-text>
					</v-btn>

					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[21].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Updated Akala</v-card-text>
					</v-btn>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Ulaula</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[17].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Original Ulaula</v-card-text>
					</v-btn>

					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[22].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Updated Ulaula</v-card-text>
					</v-btn>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-expansion-panel-header>Poni</v-expansion-panel-header>
				<v-expansion-panel-content>
					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[18].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Original Poni</v-card-text>
					</v-btn>

					<v-btn class="text-none text-left pl-0" block text @click="pokedexId = Number(getDexData.results[23].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
						<v-card-text>Updated Poni</v-card-text>
					</v-btn>
				</v-expansion-panel-content>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-btn class="text-none text-left pl-0 my-2" block text @click="pokedexId = Number(getDexData.results[25].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
					<v-card-text>Galar</v-card-text>
				</v-btn>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-btn class="text-none text-left pl-0 my-2" block text @click="pokedexId = Number(getDexData.results[26].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
					<v-card-text>Isle of Armor</v-card-text>
				</v-btn>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-btn class="text-none text-left pl-0 my-2" block text @click="pokedexId = Number(getDexData.results[27].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
					<v-card-text>Crown Tundra</v-card-text>
				</v-btn>
			</v-expansion-panel>

			<v-expansion-panel>
				<v-btn class="text-none text-left pl-0 my-2" block text @click="pokedexId = Number(getDexData.results[28].url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer">
					<v-card-text>Hisui</v-card-text>
				</v-btn>
			</v-expansion-panel>
		</v-expansion-panels>
	</v-navigation-drawer>

	<v-row>
		<v-spacer/>
		<v-col v-if="loadedMonData">
			<PokemonContainer
				v-for="pokemon in getMonData.pokemon_entries.slice(startSlice, endSlice)"
				:key="pokemon.pokemon_species.name"
				:pokemonId="Number(pokemon.pokemon_species.url.slice(42, -1))"
				:pokemon="pokemon"
			/>
		</v-col>
		<v-spacer/>
	</v-row>

	<v-footer fixed>
		<v-divider/>
		<v-pagination
			v-model="page"
			:length="pageNumbers"
			:total-visible="7"
		/>
		<v-divider/>
	</v-footer>
</v-card>
</template>

<script>
import axios from "axios";
import PokemonContainer from "@/components/PokemonContainer.vue";

export default {
	name: "HomeView",

	data: () => ({
		drawer: false,
		page: 1,
		numberShown: 10,
		expand: false,
		pokemonImgBack: false,
		pokemonImgShiny: false,
		pokedexId: 1,
		
		// axios
		getDexData: null,
		getMonData: null,
		loadedDexData: false,
		loadedMonData: false,
	}),

	computed: {
		pageNumbers() {
			if (this.loadedMonData) {
				return Math.ceil(this.getMonData.pokemon_entries.length / this.numberShown);
			} else {
				return 0;
			}
		},

		startSlice() { return this.endSlice - this.numberShown; },

		endSlice() { return this.page * this.numberShown; },
	},

	beforeMount() {
		this.getPokemon();
		this.getPokedex();
	},

	methods: {
		capitalize(string) {
			return string.charAt(0).toUpperCase() + string.slice(1);
		},

		getPokedex() {
			axios
				.get(`https://pokeapi.co/api/v2/pokedex/?limit=30`)
				.then((response) => {
					this.getDexData = response.data;
					this.loadedDexData = true;
				})
				.catch((error) => {
					console.error(error);
				});
		},

		getPokemon() {
			axios
				.get(`https://pokeapi.co/api/v2/pokedex/${this.pokedexId}`)
				.then((response) => {
					this.getMonData = response.data;
					this.loadedMonData = true;
				})
				.catch((error) => {
					console.error(error);
				});
		},
	},

	watch: {
		page() {
			window.scrollTo({ top: 0 });
		},

		numberShown() {
			if (this.page > this.pageNumbers) {
				return this.page = this.pageNumbers
			}
		}
	},

	components: {
		PokemonContainer,
	}
}
</script>

<style>
.custom-tooltip {
	opacity: 1 !important;
}
</style>