<template>
  <header>
    <img load="'lazy'" src="@/assets/BiblioGamesLogo.webp" alt="Logo" />
    <div class="header-right">
        <nav>
        <ul>
            <li><router-link to="/">Home</router-link></li>
            <li><router-link to="/catalog">Catalog</router-link></li>
            <li><router-link to="/usedGames">Used Games</router-link></li>
            <li><router-link to="/about">About</router-link></li>
            <li><a @click="openCart">Cart</a></li>
        </ul>
        </nav>
        <ul id="profilinterface">
        <li v-if="token == null" id = "connexion"><a href="#" @click="this.$emit('openLogin')">Login</a></li>
        <li v-if="token == null" id = "inscription"><a href="#" @click="this.$emit('openRegister')">Sign Up</a></li>
        <li v-if="token != null" id = "moncompte"><router-link to="/myAccount">My Account</router-link></li>
        <li v-if="token != null" id = "deconnexion"><a href="#" @click="this.$emit('logout')">Logout</a></li>
        </ul>
    </div>
  </header>
</template>

<script>
export default {
    name: 'Header',
    props: {
        token: {
            type: String,
            required: false,
        },
    },
    data() {
        return {
            lastScrollTop: 0,
            currentTop: 0,
        };
    },
    mounted() {
        window.addEventListener('scroll', this.handleScroll);
    },
    beforeUnmount() {
        window.removeEventListener('scroll', this.handleScroll);
    },
    methods: {
        handleScroll() {
            const currentScroll = window.pageYOffset || document.documentElement.scrollTop;
            const delta = currentScroll - this.lastScrollTop;

            this.currentTop = Math.min(Math.max(this.currentTop - delta, -75), 0);

            const header = this.$el;
            header.style.top = `${this.currentTop}px`;

            this.lastScrollTop = currentScroll <= 0 ? 0 : currentScroll;
        },
        toggleConnection() {
            this.connected = !this.connected;
        },
        openCart() {
            this.$emit('open-cart');
        }
    }
}
</script>

<style>
header {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 1000;
    transition: top 0.2s ease;
    display: flex;
    align-items: center;
    justify-content: space-between;
    background-color: #242424;
    margin: 0;
    color: white;
    width: 100vw;
    border-bottom: 1px solid #444;
}

.header-right {
    display: flex;
    align-items: center;
    justify-content: flex-end;
    gap: 30px;
}

header img {
    height: 75px;
    margin-left: 1rem;
}

nav ul {
    display: flex;
    list-style: none;
    padding: 0;
    margin: 0;
}


nav ul li a {
    color: white;
    text-decoration: none;
    font-size: 16px;
    padding: 10px 15px;
    border-radius: 5px;
    width: 100%;
}

nav ul li a:hover {
    background-color: #333;
    cursor: pointer;
}

.header-right > ul {
    display: flex;
    list-style: none;
    padding: 0;
    margin: 0;
    gap: 10px;
    margin-right: 3vw ;
}

.header-right > ul li a {
    text-decoration: none;
    padding: 8px 15px;
    border-radius: 5px;
    font-size: 16px;
}

#connexion a , #moncompte a{
    border: 1px solid white;
    color: white;
    background: transparent;
}

#inscription a ,#deconnexion a{
    background-color: white;
    color: black;
}

#profilinterface li:hover {
    transform: scale(1.1);
    transition: transform 0.2s ease;
}
</style>