<template>
<v-card dark tile class="pb-16 fill-width fill-height">
	<v-app-bar>
		<v-app-bar-nav-icon @click="drawer = true" />

		<v-toolbar-title v-if="loadedMonData">{{ capitalize(getMonData.name).replace("-", " ").replace("Letsgo", "Let's Go") }} Pokedex</v-toolbar-title>
		
		<v-spacer/>

		<v-menu dark offset-y>
			<template #activator="{ on, attrs }">
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
					<v-slider hide-details
						v-model="numberShown"
						min="1"
						max="25"
					/>
				</v-list-item>
			</v-list>
		</v-menu>
	</v-app-bar>

	<v-navigation-drawer
		v-model="drawer"
		absolute
		temporary
	>
		<v-list shaped v-if="loadedDexData">
			<div class="d-flex justify-center"><h2>Pokedex</h2></div>

			<v-divider class="ma-2 mr-1" />

			<div v-for="(pokedex, index) in pokedexMenu" :key="'menu ' + pokedex.name">
				<v-list-item
					v-if="pokedex.path"
					@click="
						pokedexId = pokedex.path;		// sets the ID for the pokedex
						getPokemon();								// update axios
						drawer = !drawer;						// closes the drawer
						page = 1;
					"
				>
					<v-card tile flat width="3px" class="ml-1 mr-5"
						:color="pokedexMenuColor(pokedex.path)"
						:class="menuList[index - 1] ? 'pokedexMenuBorderStart' : pokedexMenuBorder(index)"
					/>
					<v-list-item-title>{{ pokedex.name }}</v-list-item-title>
				</v-list-item>
				<v-list-group v-if="pokedex.child" v-model="menuList[index]">
					<template #activator>
						<v-card tile flat width="3px" class="ml-1 mr-5"
							:color="pokedexMenuGroupColor(pokedex.child) ? 'primary' : 'grey'"
							:class="menuList[index - 1] ? 'pokedexMenuBorderStart' : (menuList[index] ? 'pokedexMenuBorderEnd' : pokedexMenuBorder(index))"
						/>
						<v-list-item-content>
							<v-list-item-title>{{ pokedex.name }}</v-list-item-title>
						</v-list-item-content>
					</template>
					<v-list-item
						v-for="(child, indexChild) in pokedex.child" :key="'menu ' + child.name"
						@click="
							pokedexId = child.path;		// sets the ID for the pokedex
							getPokemon();							// update axios
							drawer = !drawer;					// closes the drawer
							page = 1;
						"
					>
						<v-card tile flat class="ml-2 mr-6" width="3px"
							:color="pokedexMenuColor(child.path)"
							:class="pokedexMenuBorder(index, indexChild)"
						/>
						<v-list-item-title>{{ child.name }}</v-list-item-title>
					</v-list-item>
				</v-list-group>
			</div>
		</v-list>
	</v-navigation-drawer>

	<v-row>
		<v-spacer/>
		<v-col cols="12" sm="8" v-if="loadedMonData">
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

		// pokedex menu data
		menuList: [],
		pokedexMenu: [
			{ name: "National", path: 1 },
			{ name: "Kanto", child: [
				{ name: "Kanto", path: 2 },
				{ name: "Let's Go Kanto", path: 26 },
			]},
			{ name: "Johto", child: [
				{ name: "Original Johto", path: 3 },
				{ name: "Updated Johto", path: 7 },
			]},
			{ name: "Hoenn", child: [
				{ name: "Original Hoenn", path: 4 },
				{ name: "Updated Hoenn", path: 15 },
			]},
			{ name: "Sinnoh", child: [
				{ name: "Original Sinnoh", path: 5 },
				{ name: "Extended Sinnoh", path: 6 },
			]},
			{ name: "Unova", child: [
				{ name: "Original Unova", path: 8 },
				{ name: "Updated Unova", path: 9 },
			]},
			{ name: "Kalos", child: [
				{ name: "Kalos Central", path: 12 },
				{ name: "Kalos Coastal", path: 13 },
				{ name: "Kalos Mountain", path: 14 },
			]},
			{ name: "Alola", child: [
				{ name: "Original Alola", path: 16 },
				{ name: "Updated Alola", path: 21 },
			]},
			{ name: "Melemele", child: [
				{ name: "Original Melemele", path: 17 },
				{ name: "Updated Melemele", path: 22 },
			]},
			{ name: "Akala", child: [
				{ name: "Original Akala", path: 18 },
				{ name: "Updated Akala", path: 23 },
			]},
			{ name: "Ulaula", child: [
				{ name: "Original Ulaula", path: 19 },
				{ name: "Updated Ulaula", path: 24 },
			]},
			{ name: "Poni", child: [
				{ name: "Original Poni", path: 20 },
				{ name: "Updated Poni", path: 25 },
			]},
			{ name: "Galar", path: 27 },
			{ name: "Hisui", path: 30 },
			{ name: "Conquest Gallery", path: 11 },
			{ name: "Isle of Armor", path: 28 },
			{ name: "Crown Tundra", path: 29 },
		],
		
		// axios placeholders
		getDexData: null,
		loadedDexData: false,
		getMonData: null,
		loadedMonData: false,
	}),

	computed: {
		// gets the page numbers
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
		// get data on page load
		this.getPokemon();
		this.getPokedex();
	},

	methods: {
		// sets the side menu borders based on first/last item
		pokedexMenuBorder(index, indexChild) {
			if (index !== undefined) {
				if ((indexChild !== undefined) ? (this.pokedexMenu[index].child.length === 1) : (this.pokedexMenu.length === 1)) {
					return "pokedexMenuBorderSolo";
				} else if ((indexChild !== undefined) ? (indexChild === 0) : (index === 0)) {
					return "pokedexMenuBorderStart";
				} else if ((indexChild !== undefined) ? (indexChild === this.pokedexMenu[index].child.length - 1) : (index === this.pokedexMenu.length - 1)) {
					return "pokedexMenuBorderEnd";
				} else {
					return "pokedexMenuBorderMiddle";
				}
			} else {
				return ""
			}
		},

		// sets the side menu border active color
		pokedexMenuColor(value) {
			var color = "";
			this.pokedexMenu.forEach(function() {
				if (value === this.pokedexId) {
					color = "primary"
				} else {
					color = "grey"
				}
			}, this)
			return color
		},

		// sets the side menu group border active color
		pokedexMenuGroupColor(child) {
			var color = false
			child.forEach(function(item) {
				if (item.path === this.pokedexId) {
					color = true
				}
			}, this)
			return color
		},

		// capitalizes the first letter of a word
		capitalize(string) {
			return string.charAt(0).toUpperCase() + string.slice(1);
		},

		// axios
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
		// scrolls to the top of the page if the page number changes
		page() {
			window.scrollTo({ top: 0 });
		},

		// checks if the current page is larger then the avalible pages
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

<style scoped>
/* pokedex menu borders */
.pokedexMenuBorderSolo {
	height: 32px;
}

.pokedexMenuBorderStart {
	height: 40px;
	border-bottom: 8px solid #9E9E9E !important;
	display: flex;
	align-self: flex-end;
	-moz-transition: height 0.3s ease;
  -webkit-transition: height 0.3s ease;
  -o-transition: height 0.3s ease;
  transition: height 0.3s ease;
}

.pokedexMenuBorderMiddle {
	height: 48px;
	border-bottom: 8px solid #9E9E9E !important;
	border-top: 8px solid #9E9E9E !important;
	display: flex;
	align-self: flex-start;
	-moz-transition: height 0.3s ease;
  -webkit-transition: height 0.3s ease;
  -o-transition: height 0.3s ease;
  transition: height 0.3s ease;
}

.pokedexMenuBorderEnd {
	height: 40px;
	border-top: 8px solid #9E9E9E !important;
	display: flex;
	align-self: flex-start;
	-moz-transition: height 0.3s ease;
  -webkit-transition: height 0.3s ease;
  -o-transition: height 0.3s ease;
  transition: height 0.3s ease;
}
</style>