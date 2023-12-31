<template>
    <main class="mb-3">
        <div class="container mx-auto">

            <section class="filters-categories row justify-content-center my-2">

                <button v-for="category in categories" :class="selectedCategories.includes(category) ? 'active' : ''"
                    class="animated-button col-auto">
                    <span v-on:click="addFilter(category)" class="button-text">{{ category }}</span>
                </button>

            </section>

            <section class="restaurants-list" v-if="store.restaurants.length > 0">

                <h2 class="my-h2">Ristoranti</h2>

                <div @click="fetchRestaurantBySlug(restaurant.slug)" class="card my-col d-flex flex-column"
                    v-for="(restaurant, i) in store.restaurants" :key="i">
                    <img class="thumb" :src="getImageUrl(restaurant.img)" alt="restaurant img">

                    <div class="info-text">
                        <h4 class="restaurant-title my-text text-center mb-0">{{ restaurant.name }}</h4>
                    </div>

                    <div class="info-categories row align-items center justify-content-center">
                        <span class="category-name col-auto" v-for="category in store.restaurants[i].categories">{{
                            category.name
                        }}</span>
                    </div>
                </div>
            </section>


            <div class="error-message" v-if="store.restaurants.length == 0">
                <h2>Nessun ristorante corrisponde ai parametri di ricerca, per favore prova a ricercare un'altra categoria.
                </h2>
            </div>

        </div>

        <div class="container-auto pt-5">
            <img class="wave-middle" src="/imgs/waves/desc-wave-desktop.svg" alt="">
        </div>

        <section class="d-flex flex-column gap-5 pb-5 px-4 bg-descpription">

            <h1 class="text-center py-4">Il nostro mondo</h1>

            <div class="container mx-auto d-flex justify-content-between flex-wrap row-gap-5">
                <div class="col-12 col-md-5 d-flex flex-column">
                    <h3 class="mb-2">Ben Arrivato cliente!</h3>
                    <p>Benvenuti nel nostro sito internet, il luogo ideale per soddisfare le vostre voglie culinarie senza
                        uscire di casa! Siamo un servizio di consegna di cibo online che collabora con una vasta selezione
                        di ristoranti locali, offrendovi una varietà di delizie gastronomiche da gustare nel comfort del
                        vostro salotto.</p>
                </div>
                <div class="col-12 col-md-5 d-flex flex-column">
                    <h3 class="mb-2">Il nostro Obiettivo</h3>
                    <p>Il nostro obiettivo è offrirvi una piattaforma facile da usare, dove potrete sfogliare i menu dei
                        migliori ristoranti della zona e ordinare i vostri piatti preferiti con pochi clic. Che siate alla
                        ricerca di una pizza saporita, una pasta al dente, un sushi fresco o un'insalata croccante, avrete a
                        disposizione un'ampia scelta di cucine internazionali per accontentare ogni palato.</p>
                </div>
            </div>

            <div class="container mx-auto d-flex justify-content-between flex-wrap row-gap-5">
                <div class="col-12 col-md-5 d-flex flex-column">
                    <h3 class="mb-2">&eacute; Facile!</h3>
                    <p>La nostra interfaccia intuitiva vi permetterà di filtrare le opzioni in base alle vostre preferenze,
                        consentendovi di cercare ristoranti di diverse nazionalit&aacute; tra le quali: cinese, giapponese,
                        argentino, indiano e molto altro </p>
                </div>
                <div class="col-12 col-md-5 d-flex flex-column">
                    <h3 class="mb-2">Il nostro Impegno</h3>
                    <p>Ci preoccupiamo della qualità del cibo che offriamo e collaboriamo solo con ristoranti che rispettano
                        i più alti standard igienici e di sicurezza. I nostri corrieri dedicati garantiranno che il vostro
                        ordine arrivi caldo e fresco direttamente a casa vostra, in tempi rapidi e puntuali.</p>
                </div>
            </div>
        </section>

        <div class="container-auto bg-wave pt-5">
            <img class="wave-bottom" src="/imgs/waves/footer-wave-desktop.svg" alt="">
        </div>
    </main>
</template>

<script>
import store from '../../store';
import axios from 'axios';

export default {
    data() {
        return {
            store,
            categories: [],
        }
    },
    methods: {
        fetchCategories() {
            axios.get('http://127.0.0.1:8000/api/restaurants', {
            })
                .then(res => {
                    const results = res.data.results

                    for (let i = 0; i < results.length; i++) {

                        const singleCategories = results[i].categories

                        for (let j = 0; j < singleCategories.length; j++) {

                            const category = singleCategories[j]

                            const categoryLC = category.name.toLowerCase()

                            if (!this.categories.includes(categoryLC)) {

                                this.categories.push(categoryLC)
                            }
                        }
                    }
                }).catch((err) => {
                    this.$router.push('/404')
                })
        },
        addFilter(category) {

            const basePath = 'http://127.0.0.1:8000/api/restaurants'

            const categories = this.selectedCategories

            // controllo: se la categoria selezionata è presente nell'array allora elimino quella categoria
            if (this.selectedCategories.includes(category)) {
                category.toLowerCase()
                this.selectedCategories.splice(this.selectedCategories.indexOf(category), 1)
            }
            // altrimenti pusho la categoria nell'array dello store
            else {
                category.toLowerCase()
                this.selectedCategories.push(category)
            }

            // chiamata con l'array di parametri inseriti nello store
            axios.get(basePath, {
                params: {
                    categories,
                }
            })
                .then((res) => {
                    this.store.restaurants = res.data.results
                })
                .catch((err) => {
                    this.$router.push('/404')
                })
        },
        fetchRestaurantBySlug(slug) {

            axios.get(`http://127.0.0.1:8000/api/restaurants/${slug}`)
                .then(res => {

                    this.restaurant = res.data.results

                    this.dishes = this.restaurant.dishes

                    this.$router.push(`/restaurant/${slug}`)
                }).catch((err) => {
                    this.$router.push('/404')
                })
        },
        getImageUrl(imagePath) {
            if (imagePath) {
                return `http://127.0.0.1:8000/storage/${imagePath}`
            }
        },
    },
    computed: {
        selectedCategories() {
            return this.store.selectedCategories
        }
    },
    mounted() {
        if (this.selectedCategories == false) {
            // come mai non gli va bene l'array vuoto ma lo zero/false si
            const basePath = 'http://127.0.0.1:8000/api/restaurants'

            axios.get(basePath)

                .then((res) => {
                    this.store.restaurants = res.data.results
                })
                .catch((err) => {
                    this.$router.push('/404')
                })
        } else {

            const basePath = 'http://127.0.0.1:8000/api/restaurants'

            const categories = this.selectedCategories

            // chiamata con l'array di parametri inseriti nello store
            axios.get(basePath, {
                params: {
                    categories,
                }
            })
                .then((res) => {
                    this.store.restaurants = res.data.results
                })
                .catch((err) => {
                    this.$router.push('/404')
                })

            window.scrollTo({
                top: 0,
                behavior: 'smooth'
            })
        }
        this.fetchCategories()
    },
}

</script>

<style lang="scss" scoped>
@use '../../styles/main.scss' as *;
@use '../../styles/partials/resets.scss' as *;
@use '../../styles/partials/colors.scss' as *;

main {

    .container {
        background-color: none;

        .my-col {
            flex-basis: calc(100%/4 - 16px*3/4);
        }

        .my-h2 {
            color: black;
            flex-basis: 100%;
            margin-bottom: 0;
        }

        .restaurants-list {
            display: flex;
            flex-wrap: wrap;
            gap: 16px;

            .card {
                border-radius: 1rem;
                position: relative;
                box-shadow: rgba(0, 0, 0, 0.16) 0px 10px 36px 0px, rgba(0, 0, 0, 0.06) 0px 0px 0px 1px;
                cursor: pointer;
                overflow: hidden;
                background-color: white;

                .thumb {
                    border-radius: 1rem 1rem 0 0;
                    width: 100%;
                    aspect-ratio: 16/9;
                    transition: transform .3s;
                    transform: scale(1);
                }

                .info-text {
                    position: absolute;
                    height: 100%;
                    width: 100%;

                    .restaurant-title {
                        position: absolute;
                        color: white;
                        top: 50%;
                        left: 50%;
                        transform: translate(-50%, -50%);
                        filter: drop-shadow(0 0 5px black);
                        font-weight: bold;
                    }
                }

                .info-categories {
                    border-top: 2px solid $purple-3;
                    background-color: $orange-3;
                    z-index: 2;
                    font-size: 0.75rem;
                    padding: 10px;
                    gap: 4px;
                    height: 100%;

                    .category-name {
                        background-color: $purple-5;
                        padding: 0 3px;
                        border-radius: 10px;
                        color: white;
                        align-self: flex-start;
                    }
                }
            }
        }

        .filters-categories {

            .animated-button {
                background-color: #b87ee7;
                opacity: 1;
                border: none;
                color: white;
                padding: 3px 7px;
                text-align: center;
                text-decoration: none;
                display: inline-block;
                font-size: 0.75rem;
                margin: 4px 2px;
                cursor: pointer;
                font-family: 'Montserrat', sans-serif;
                border-radius: 10px;
                box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
                overflow: hidden;
                position: relative;
                transition: all 0.5s ease;
                z-index: 1;

                .button-text {
                    z-index: 999;
                }
            }

            .animated-button:after {
                content: "";
                position: absolute;
                top: 50%;
                left: 50%;
                width: 0;
                height: 0;
                background-color: $purple-4;
                border-radius: 50%;
                opacity: 1;
                transform: translate(-50%, -50%);
                transition: all 0.5s ease;
                z-index: -1;
            }

            .animated-button:hover:after {
                width: 100px;
                height: 100px;
                opacity: 1;
                z-index: -1;
            }

            .animated-button:focus {
                outline: none;
            }

            .animated-button:active {
                transform: translateY(2px);
                transition: 0.1s;
                box-shadow: none;
            }

            .active {
                background-color: $purple-4;
            }
        }
    }
}

.bg-descpription {
    background-color: #EBEBEB;
}

.bg-wave {
    background-color: #EBEBEB;
}

.wave-middle {
    width: 100%;
}

@media (max-width: 991px) {
    .my-col {
        flex-basis: calc(100%/3 - 16px*2/3) !important;
    }
}

@media (max-width: 575px) {
    .my-col {
        flex-basis: calc(100%/2 - 16px/2) !important;
    }

    .restaurants-list {
        margin: 15px;
    }
}

@media (max-width: 414px) {
    .my-col {
        flex-basis: 100% !important;
    }

    .restaurants-list {
        margin: 30px;
    }
}

.wave-bottom {
    min-width: 100%;
    position: relative;
    bottom: -16px;
    background-color: #EBEBEB;
}

@media (max-width: 376px) {
    .wave-bottom {
        bottom: -18px;
    }
}

@media (max-width: 422px) {
    .wave-bottom {
        bottom: -18px;
    }
}</style>