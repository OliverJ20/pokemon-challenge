<template>
  <div>
    <h1>{{ title }}</h1>
    <Modal v-if="triggerModal && selectedPokemon" @ok="handleClose" v-model="triggerModal" :title="selectedPokemon.name">
      <img :src="selectedPokemon.sprites.front_default" alt="pokemon" width="150" height="200">
      <h3>Types</h3>
      <hr>
      <div style="justify-content: space-evenly; display: flex">
        <div v-for="type in selectedPokemon.types" :key="type">
          <p>{{type}}</p>
        </div>
      </div>
      <div v-if="selectedPokemon.heldItems.length > 0">
        <h3>{{selectedPokemon.name}} Holds the following items</h3>
        <hr>
        <div>
          <div  v-for="item in selectedPokemon.heldItems" :key="item">
            <ul>{{item.item.name}}</ul>
          </div>
        </div>
      </div>
      <div v-else>
        <h3>{{selectedPokemon.name}} holds no items</h3>
        <hr>
      </div>
      <h3>Originating from the species</h3>
      <hr>
        <div>
          <p>{{selectedPokemon.species}}</p>
        </div>
    </Modal>
    <Table :columns="columns" :data-source="pokemonArray">
      <a slot="name" slot-scope="text"  @click="pokemonModalOpen(text)">{{ text }}</a>
      <span slot="customTitle"><a-icon type="smile-o" /> Name</span>
      <span slot="types" slot-scope="types">
      <Tag
              v-for="tag in types"
              :key="tag"
              :style="`background-color: ${typeColor(tag)}`"
      >
        {{ tag.toUpperCase() }}
      </Tag>
    </span>
    </Table>
  </div>
</template>

<script>
import axios from 'axios'
import 'ant-design-vue/dist/antd.css'
import { Table, Tag, Modal } from 'ant-design-vue'
export default {
  name: 'PokemonTable',
  components: {
    Table,
    Tag,
    Modal
  },
  props: {
    title: String
  },

  data() {
    return {
      triggerModal: false,
      dataSource: null,
      pokemonArray: [],
      pokemonArrayDefault: [],
      selectedPokemon: {},
      columns: [
        {
          dataIndex: 'name',
          key: 'name',
          title: this.inputSetup,
          scopedSlots: { customRender: 'name' },
          defaultSortOrder: 'ascend',
          sorter: (a, b) => a.name.localeCompare(b.name),
        },
        {
          title: 'Types',
          key: 'types',
          dataIndex: 'types',
          scopedSlots: { customRender: 'types' },
          filters: [
            {
              text: 'Normal',
              value: 'normal',
            },
            {
              text: 'Fighting',
              value: 'fighting',
            },
            {
              text: 'Flying',
              value: 'flying',
            },
            {
              text: 'Poison',
              value: 'poison',
            },
            {
              text: 'Ground',
              value: 'ground',
            },
            {
              text: 'Rock',
              value: 'rock',
            },
            {
              text: 'Bug',
              value: 'bug',
            },
            {
              text: 'Ghost',
              value: 'ghost',
            },
            {
              text: 'Steel',
              value: 'steel',
            },
            {
              text: 'Fire',
              value: 'fire',
            },
            {
              text: 'Water',
              value: 'water',
            },
            {
              text: 'Grass',
              value: 'grass',
            },
            {
              text: 'Electric',
              value: 'electric',
            },
            {
              text: 'Psychic',
              value: 'psychic',
            },
            {
              text: 'Ice',
              value: 'ice',
            },
            {
              text: 'Dragon',
              value: 'dragon',
            },
            {
              text: 'Fairy',
              value: 'fairy',
            },
            {
              text: 'Dark',
              value: 'dark',
            },
            {
              text: 'Unknown',
              value: 'unknown',
            },
          ],
          filterMultiple: false,
          onFilter: (value, record) => record.types.indexOf(value) === 0,
          sorter: (a, b) => a.types.localeCompare(b.types),
          sortDirections: ['descend', 'ascend'],
        },
      ],
    };
  },

  created() {
    this.getPokemonData()
  },

  methods: {
    /**
     * Sets up the search bar for the name column
     * A messy implementation but produces the desired affect
     * @returns {boolean}
     */
    inputSetup() {
      return (
              <div style={{ textAlign: 'center', display: 'flex' }}>
    <div style="margin-top: auto; margin-bottom: auto; margin-right: 10px;">Name</div>
              <Input placeholder="Search Name" onChange={this.filterTable} />
      </div>
    );
    },

    filterTable(value) {
      this.pokemonArray = this.pokemonArrayDefault.filter(item => item.name.includes(value.target.value))
    },

    /**
     * Set the colour of the pokemon types to match the colour used on online sites to make them look distinct
     * @param value
     * @returns {string}
     */
    typeColor(value) {
      console.log('value', value)
      switch (value) {
        case 'normal':
          return '#a8a978'
        case 'fighting':
          return '#c03128'
        case 'flying':
          return '#a890f0'
        case 'poison':
          return '#a041a0'
        case 'ground':
          return '#e0c068'
        case 'rock':
          return '#b8a038'
        case 'bug':
          return '#a8b821'
        case 'ghost':
          return '#705898'
        case 'steel':
          return '#b8b8d0'
        case 'fire':
          return '#f08031'
        case 'water':
          return '#6890f0'
        case 'grass':
          return '#78c850'
        case 'electric':
          return '#f7d031'
        case 'psychic':
          return '#f45888'
        case 'ice':
          return '#98d8d8'
        case 'dragon':
          return '#7038f8'
        case 'dark':
          return '#7038f8'
        case 'fairy':
          return '#7038f8'
        default:
          return '#7038f8'
      }
    },

    /**
     * Access the pokemon api and retrieves a list of the first 300 pokemon listed
     * It then makes seperate calls within to grab individual details of the pokemon to build out a profile,
     * adding it into an array of objects to display the information across the table.
     */
    getPokemonData() {
      axios.get('https://pokeapi.co/api/v2/pokemon?limit=300',).then((response) => {
        this.$data.dataSource = response.data.results;

        this.dataSource.forEach((item) => {
          axios.get(item.url).then((res) => {
            const types = res.data.types.map((i) => {
              return i.type.name
            })
             this.$data.pokemonArray.push({
               name: res.data.name,
               types: types,
               sprites: res.data.sprites,
               heldItems: res.data.held_items,
               species: res.data.species.name,
             })
            // create a clean copy which wont be altered
            this.pokemonArrayDefault = this.$data.pokemonArray;
          })
        })
      });
    },
    pokemonModalOpen(value) {
      this.selectedPokemon = this.$data.pokemonArray.filter((item) => item.name === value)[0]
      this.triggerModal = true;
    },
    handleClose() {
      this.selectedPokemon = {};
      this.triggerModal = false;
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
