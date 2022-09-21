<template>
<v-card dark tile class="pb-16 fill-width fill-height">
	<v-app-bar>
		<v-app-bar-nav-icon @click="drawer = true"/>

		<v-toolbar-title v-if="loadedMonData">{{ capitalize(getMonData.name) }} Pokedex</v-toolbar-title>
		
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
          :key="index"
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
		<v-list dense rounded>
			<v-list-item-content>
				<v-list-item-title>Pokedex</v-list-item-title>
			</v-list-item-content>

			<v-divider/>

			<v-list-item v-for="item in getDexData" :key="item.name">
				{{ item.name }}test
			</v-list-item>
		</v-list>
	</v-navigation-drawer>

	<v-row>
		<v-spacer/>
		<v-col>
			<div v-if="loadedMonData">
				<v-card outlined shaped width="1000" class="overflow-hidden ma-2"
					v-for="pokemon in getMonData.pokemon_entries.slice(startSlice, endSlice)"
					:key="pokemon.pokemon_species.name"
				>
					<v-row no-gutters>
						<v-col cols="8" class="fill-height">
							<v-row no-gutters>
								<v-card-title>{{ pokemon.entry_number }} - {{ capitalize(pokemon.pokemon_species.name).replace("-f", " ♀").replace("-m", " ♂") }}</v-card-title>

								<v-spacer/>

								<div>test</div>
							</v-row>

							<v-divider/>

							<v-row class="ma-2" v-if="loadedMonDataExtra">
								<v-col cols="6" v-for="stats in getMonDataExtra.stats" :key="stats.stat.name[pokemon.pokemon_species.name]">
									{{ capitalize(stats.stat.name).replace("-", " ") }}: {{ stats.base_stat }}
								</v-col>
							</v-row>
							<v-btn @click="expand = !expand" tile class="pokemonShowBtn">Show {{ expand ? "less" : "more" }} info</v-btn>
						</v-col>

						<v-divider vertical/>

						<v-col cols="4" v-if="loadedMonDataExtra">
							<v-card tile class="pokemonImg">
								<v-img v-if="!pokemonImgBack && !pokemonImgShiny" :src="getMonDataExtra.sprites.front_default"/>
								<v-img v-if="pokemonImgBack && !pokemonImgShiny" :src="getMonDataExtra.sprites.back_default"/>
								<v-img v-if="!pokemonImgBack && pokemonImgShiny" :src="getMonDataExtra.sprites.front_shiny"/>
								<v-img v-if="pokemonImgBack && pokemonImgShiny" :src="getMonDataExtra.sprites.back_shiny"/>
								<v-row no-gutters class="pokemonImgBtn">
									<v-col cols="6">
										<v-tooltip top>
											<template v-slot:activator="{ on, attrs }">
												<v-btn tile block
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

									<v-col cols="6">
										<v-tooltip top>
											<template v-slot:activator="{ on, attrs }">
												<v-btn tile block
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
								<v-expansion-panel-header>Moves</v-expansion-panel-header>
								<v-expansion-panel-content>
									<v-row v-if="loadedMonDataExtra">
										<v-col cols="4" v-for="moves in getMonDataExtra.moves" :key="moves[pokemon.entry_number]">
											<v-card outlined class="justify-center pa-2 d-flex">{{ moves.move.name }}</v-card>
										</v-col>
									</v-row>
								</v-expansion-panel-content>
							</v-expansion-panel>
						</v-expansion-panels>
					</v-expand-transition>
				</v-card>
			</div>
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
		pokemonId: 1,
		
		// axios
		getDexData: null,
		getMonData: null,
		getMonDataExtra: null,
		loadedDexData: false,
		loadedMonData: false,
		loadedMonDataExtra: false,
	}),

	computed: {
		pageNumbers() {
			if (this.loadedMonData) {
				return Math.ceil(this.getMonData.pokemon_entries.length / this.numberShown)
			} else {
				return 0
			}
		},

		startSlice() { return this.endSlice - this.numberShown },
		
		endSlice() { return this.page * this.numberShown },
	},

	beforeMount() {
		this.getPokemon();
		this.getPokemonData();
	},

	methods: {
		capitalize(string) {
			return string.charAt(0).toUpperCase() + string.slice(1)
		},

		getPokedex() {
			axios
				.get(`https://pokeapi.co/api/v2/pokedex/?offset=0&limit=30`)
				.then((response) => {
					this.getDexData = response.data
					this.loadedDexData = true
					console.log(this.getDexData)
				})
				.catch((error) => {
					console.error(error)
				})
		},
		
		getPokemon() {
			axios
				.get(`https://pokeapi.co/api/v2/pokedex/${ this.pokedexId }/`)
				.then((response) => {
					this.getMonData = response.data
					this.loadedMonData = true
				})
				.catch((error) => {
					console.error(error)
				})
		},

		getPokemonData() {
			axios
				.get(`https://pokeapi.co/api/v2/pokemon/${ this.pokemonId }/`)
				.then((response) => {
					this.getMonDataExtra = response.data
					this.loadedMonDataExtra = true
				})
				.catch((error) => {
					console.error(error)
				})
		}
	},

	watch: {
		page() {
			window.scrollTo({ top: 0 });
		}
	}
}
</script>

<style>
.pokemonImg {
	margin-left: 1px;
}

.pokemonImgBtn {
	position: absolute;
	bottom: 0;
	left: 0;
	width: 100%;
}

.pokemonShowBtn {
	position: absolute;
	left: 0;
}
</style>