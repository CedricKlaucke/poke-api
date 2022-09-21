<template>
<v-card dark tile class="pb-16 fill-width fill-height">
	<v-app-bar>
		<v-app-bar-nav-icon @click="drawer = true"/>

		<v-toolbar-title v-if="loadedMonData">{{ capitalize(getMonData.name).replace("-", " ") }} Pokedex</v-toolbar-title>
		
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
					v-for="(item, index) in 5"
					:key="'menu' + index"
					@click="numberShown = item * 5"
				>
					<v-list-item-title>{{ item * 5 }}</v-list-item-title>
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
		<v-list v-if="loadedDexData" dense>
			<v-list-item-content>
				<v-list-item-title class="justify-center d-flex">Pokedex</v-list-item-title>
			</v-list-item-content>

			<v-divider/>

			<v-expansion-panels tile accordion>
				<v-expansion-panel v-for="results in getDexData.results" :key="results.name">
					<v-list-item link
						@click="pokedexId = Number(results.url.slice(34, -1)); getPokemon(); page = 1; drawer = !drawer"
					>
						<v-list-item-title>{{ capitalize(results.name) }}</v-list-item-title>
					</v-list-item>
				</v-expansion-panel>
			</v-expansion-panels>
		</v-list>
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
				.get(`https://pokeapi.co/api/v2/pokedex/?offset=0&limit=30`)
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