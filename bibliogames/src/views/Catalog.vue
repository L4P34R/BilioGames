<template>
    <div class="catalog">
        <section class="catalog-header text">
        <h1>Catalog</h1>
        <p>Explore our collection of games</p>
        </section>

        <section class="game-list">
            <gamecard 
                v-for="game in games.find(g => g.Page === Page)?.data" 
                :key="game.id" 
                :game="game" 
                @add-to-cart="addToCart" 
            />
        </section>
        <div class="nav-buttons">
            <button class="nav-button" v-if="Page > 1" @click="Page = 1; getXGames()">First Page</button>
            <button class="nav-button" @click="prevPage()" :disabled="Page <= 1">Previous</button>
            <span class="page-indicator">{{ Page }} / {{ maxPage }}</span>
            <button class="nav-button" @click="nextPage()" :disabled="Page >= maxPage">Next</button>
            <button class="nav-button" v-if="Page < maxPage" @click="Page = maxPage; getXGames()">Last Page</button>
        </div>
        <div class="page-input-container">
            <input 
                type="number" 
                class="page-input" 
                :min="1" 
                :max="maxPage" 
                v-model.number="goToPage" 
                placeholder="Go to page" 
                @keyup.enter="goToSpecificPage" 
            />
            <button 
                class="nav-button" 
                @click="goToSpecificPage" 
                :disabled="!isValidPage(goToPage)"
            >
                Go
            </button>
        </div>
    </div>
</template>

<script>
import Gamecard from '../components/Gamecard.vue';
import axios from 'axios';


export default {
  name: 'Catalog',
    components: {
        Gamecard,
    },
  data() {
    return {
        games: [],
        numberOfGames: 20,
        Page: 1,
        totalGames: 0,
        sort: 'average',
        order: 'ASC',
        maxPage: 1,
        goToPage: null, // Page saisie par l'utilisateur
    };
  },
    methods: {
    async getXGames() {
        if (!this.games.find(g => g.Page === this.Page)) {
            try {
                const response = await axios.get('http://localhost:5001/gamesLimited', {
                    params: {
                        x: this.numberOfGames,
                        page: this.Page,
                        sort: this.sort,
                        order: this.order,
                    },
                });
                const fetchedGames = {
                    Page: this.Page,
                    data: response.data,
                };
                this.games.push(fetchedGames);
                console.log(`Page ${this.Page} fetched successfully.`);
                this.storeGames();
            } catch (error) {
                console.error(`Error fetching page ${this.Page}:`, error);
            }
        } else {
            console.log(`Page ${this.Page} already fetched.`);
        }
    },
    async getNbGames() {
        try {
            const response = await axios.get('http://localhost:5001/gamesCount');
            this.totalGames = parseInt(response.data, 10); // Utilisation correcte de parseInt avec la base 10
            this.maxPage = Math.ceil(this.totalGames / this.numberOfGames); // Calcul du nombre total de pages
            console.log(`Total games: ${this.totalGames}, Max pages: ${this.maxPage}`);
        } catch (error) {
            console.error('Error fetching total number of games:', error);
        }
    },
    async storeGames(){
        localStorage.setItem('games', JSON.stringify(this.games));
        console.log('Games stored in localStorage:', this.games);
    },
    nextPage() {
        this.Page++;
        this.getXGames();
    },
    prevPage() {
        this.Page--;
        this.getXGames();
    },
    addToCart(game) {
            this.$emit('add-to-cart', game);
        },
    goToSpecificPage() {
        if (this.isValidPage(this.goToPage)) {
            this.Page = this.goToPage;
            this.getXGames();
        }
    },
    isValidPage(page) {
        return page >= 1 && page <= this.maxPage;
    },
    },
    created() {
        this.getNbGames();
        this.games = JSON.parse(localStorage.getItem('games')) || [];
        this.getXGames();
        console.log(`http://${window.baseURL.hostname}:5001`);
    },
};
</script>

<style scoped>
.catalog {
  padding: 2rem;
  background-color: #1e1e1e;
  color: white;
}

.catalog-header {
  text-align: center;
  margin-bottom: 2rem;
}

.catalog-header h1 {
  font-size: 2.5rem;
  margin-bottom: 1rem;
}

.catalog-header p {
  font-size: 1.2rem;
  opacity: 0.7;
}

.game-list {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(18rem, 1fr));
    gap: 1.5rem;
}

.nav-buttons {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  margin-top: 2rem;
}

.nav-button {
  background-color: #303030;
  color: white;
  border: none;
  padding: 0.6rem 1.2rem;
  border-radius: 5px;
  border: 0.5px solid #949494;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.nav-button:hover {
  background-color: white;
  color: black;
  opacity: 0.7;
  transform: translateY(-2px);
}

.nav-button:disabled {
  background-color: #555;
  cursor: not-allowed;
}

.page-indicator {
  font-size: 1.2rem;
  font-weight: bold;
  color: white;
  opacity: 0.8;
}

.page-input-container {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 0.5rem;
  margin-top: 1rem;
}

.page-input {
  width: 6rem;
  padding: 0.4rem;
  border: 1px solid #949494;
  border-radius: 5px;
  background-color: #303030;
  color: white;
  text-align: center;
  font-size: 1rem;
}

.page-input::placeholder {
  color: #aaa;
}

.page-input:focus {
  outline: none;
  border-color: white;
}
</style>