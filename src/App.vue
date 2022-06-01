<template>
    <div>
        <div class="mainframe">
            <div v-if="isError">
                <div class="error__block">
                    <div @click="handleReturn" class="error__arrow header__arrow">
                        <img src="./assets/errorPage/left-arrow.png" alt="" />
                    </div>
                    <h1 class="error__block-title">City not found. Try again!</h1>
                    <div class="error__icon"><img src="./assets/errorPage/error.png" alt="" /></div>
                </div>
            </div>
            <div v-else>
                <div class="mainframe-header">
                    <div class="header__body">
                        <div class="header__icon">
                            <img :src="getImgUrl(data?.weather?.[0]?.description)" alt="" />
                            <div class="header__city">{{ data?.name }}</div>
                        </div>
                        <div class="header__info">
                            <div class="info__temp">{{ data?.main?.temp }} F</div>
                            <div class="info__feelslike">feelslike {{ data?.main?.feels_like }} f</div>
                            <div class="info__title">{{ data?.weather?.[0]?.description }}</div>
                        </div>
                    </div>
                </div>
                <div class="mainframe-details">
                    <div class="details details__visibility">
                        <div class="item__icon"><img src="./assets/detailsIcons/fog.png" alt="" /></div>
                        <div class="item__value">{{ data?.visibility }}</div>
                    </div>
                    <div class="details details__pressure">
                        <div class="item__icon"><img src="./assets/detailsIcons/atmospheric.png" alt="" /></div>
                        <div class="item__value">{{ data?.main?.pressure }} atm</div>
                    </div>
                    <div class="details details__windspeed">
                        <div class="item__icon"><img src="./assets/detailsIcons/wind.png" alt="" /></div>
                        <div class="item__value">{{ data?.wind?.speed }} mph</div>
                    </div>
                    <div class="details details__humidity">
                        <div class="item__icon"><img src="./assets/detailsIcons/humidity.png" alt="" /></div>
                        <div class="item__value">{{ data?.main?.humidity }} %</div>
                    </div>
                </div>
                <div class="mainframe__choosecity">
                    <form class="choosecity-form__input">
                        <input type="text" v-model.trim="selectedCity" class="choosecity__input" />
                        <input type="submit" @click.prevent="handleSubmit" class="choosecity__button" value="Search!" />
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from 'vue';
export default defineComponent({
  name: 'App',
  components: {
  },
   setup() {
        let data = ref([]);
        const selectedCity = ref('');
        const isError = ref(false);
        const url = ref('');
        const getImgUrl = (description:string) => {
            switch (description) {
                case "mist":
                    return require('./assets/forecastIcons/' + "foggy.png");
                case "few clouds":
                    return require('./assets/forecastIcons/' + "few-clouds.png");
                case "broken clouds":
                    return require('./assets/forecastIcons/' + "few-clouds.png");
                case "overcast clouds":
                    return require('./assets/forecastIcons/' + "cloudy.png");
                case "scattered clouds":
                    return require('./assets/forecastIcons/' + "cloudy.png");
                case "light intensity shower rain":
                    return require('./assets/forecastIcons/' + "drizzle.png");
                case "light rain":
                    return require('./assets/forecastIcons/' + "drizzle.png");
                default:
                    return require('./assets/forecastIcons/' + "sunny.png");
            }
        }
        const handleReturn = () => {
            isError.value = false;
            selectedCity.value = '';
            fetchData();
        }
        async function fetchData() {
            const options = {
                method: 'GET',
                headers: {
                    'X-RapidAPI-Host': 'community-open-weather-map.p.rapidapi.com',
                    'X-RapidAPI-Key': 'b03c85640cmsh9b0fe0ac15c5e5ap141742jsn83cd7832a770'
                }
            };
            fetch(
                `https://community-open-weather-map.p.rapidapi.com/weather?q=${selectedCity.value}&%2Cuk&lat=0&lon=0&id=2172797&lang=null&units=imperial`,
                options
            )
                .then((response) => response.json())
                .then((response) => { data.value = response; if (response.cod === "404") { throw new Error("error"); } })
                .catch(e => isError.value = e)

        }
        function handleSubmit() {
            fetchData();
            selectedCity.value = '';
            console.log(data.value);
        }
        onMounted(() => {
            fetchData();
        });
        return {
            fetchData,
            handleSubmit,
            getImgUrl,
            handleReturn,
            data,
            selectedCity,
            url,
            isError,
        }
    },
});
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Lato:ital,wght@0,300;0,400;0,700;1,300&display=swap');

*,
*::before,
*::after {
    box-sizing: border-box;
}

body {
    background-color: #71739A;
    display: flex;
    height: 100%;
    align-items: center;
    justify-content: center;
    font-family: "Lato";
}

.mainframe {
    margin: 50% 0 0 0;
    min-width: 420px;
    min-height: 350px;
    background-color: #bdd7fecb;
    border-radius: 5%;
    color: rgb(255, 255, 255);
}

.mainframe-header {
    background-color: #80abec;
    padding: 30px;
}

.header__body {
    display: flex;
    justify-content: space-between;
}

.header__icon {
    display: flex;
    flex-direction: column;
    /*align-items: center;*/
}

.header__icon img {
    width: 70px;
}

.header__city {
    margin-top: 10px;
    font-size: 24px;
    font-weight: bold;
    letter-spacing: 2px;
}

.header__info {
    text-align: right;
}

.info__temp {
    font-size: 45px;
}

.info__feelslike {
    font-style: italic;
    margin-bottom: 10px;
}

.info__title {
    font-size: 18px;
}

.mainframe-details {
    padding: 30px 20px 20px 20px;
    display: flex;
    align-items: flex-start;
    justify-content: space-around;
    color: #ffffff;
}

.details__pressure {}

.details__windspeed {}

.details__humidity {}

.details__visibility {}

.item__icon img {
    width: 50px;
}

.item__value {
    margin-top: 10px;
    font-size: 18px;
}

.details {
    text-align: center;
}


.mainframe__choosecity {
    padding: 10px 0 0 0;
}

.choosecity-form__input {
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.choosecity__button {
    margin: 20px;
    width: 100px;
    border: none;
    border-radius: 5%;
    height: 30px;
    font-family: "Lato";
    font-size: 18px;
}

.choosecity__input {
    border: none;
    border-bottom: 1px solid white;
    background-color: transparent;
    color: inherit;
    outline: none;
    font-size: 20px;
}

.mainframe__choosecity {
    text-align: center;
}

.error__block {
    padding: 60px 40px 40px 40px;
}

.error__block-title {
    margin-top: 30px;
    font-size: 26px;
    text-align: center;
}

.error__icon {
    margin-top: 40px;
    text-align: center;
}

.header__arrow {
    margin: 0px 0px 10px 0;
}

.header__arrow img {
    width: 20px;
}

.error__icon img {
    width: 90px;
}
</style>
