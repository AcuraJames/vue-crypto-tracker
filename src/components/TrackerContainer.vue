<template>
    <div class="tracker-container">
        <h1>VueCoinMarketCap</h1>
        <table>
            <thead>
                <tr>
                    <th class="index">#</th>
                    <th class="name">Name</th>
                    <th>Ticker</th>
                    <th @click="sortByPrice('price')">Price &nbsp; &#9660;</th>
                    <th @click="sortByPrice('mktCap')">Market Cap &nbsp; &#9650;</th>
                    <th>Volume (24h)</th>
                    <th>Supply</th>
                </tr>
            </thead>
            <tbody>
                <tr :class="{ 'up-bg': up && uidx.has(index), 'down-bg': down && didx.has(index) }"
                    v-for="(coin, index) in sorted" :key="index" >
                    <td class="index">{{ index + 1 }}</td>
                    <td>
                        <img :src="baseUrl + coin.CoinInfo.ImageUrl"> 
                        <strong>{{ coin.CoinInfo.FullName }}</strong>                        
                    </td>
                    <td>{{ coin.CoinInfo.Name }}</td>
                    <td :class="{ up: up && uidx.has(index), down: down && didx.has(index) }">
                        {{ coin.DISPLAY.USD.PRICE }} 
                    </td>
                    <td>{{ coin.DISPLAY.USD.MKTCAP }}</td>
                    <td>{{ coin.RAW.USD.VOLUME24HOUR }}</td>
                    <td>{{ coin.RAW.USD.SUPPLY }}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
import axios from 'axios'
import { setInterval } from 'timers';

export default {
    name: 'tracker-container',
    data: () => {
        return {
            coins: [],
            uidx: new Set(),
            didx: new Set(),
            up: false,
            down: false,
            asc: false,
            desc: false,
            mktCap: true,
            baseUrl: 'https://cryptocompare.com'
        }
    },
    computed: {
        sorted() {
            return this.coins.sort((a, b) => {
                if(this.asc) return a.RAW.USD.PRICE - b.RAW.USD.PRICE
                if(this.desc) return b.RAW.USD.PRICE - a.RAW.USD.PRICE
                else return b.RAW.USD.MKTCAP - a.RAW.USD.MKTCAP
            })
        }
    },
    watch: {
        coins(newPrice, oldPrice) {
            newPrice.forEach((np, idx) => {
                if(np.CoinInfo.Name == oldPrice[idx].CoinInfo.Name && np.RAW.USD.PRICE > oldPrice[idx].RAW.USD.PRICE) {
                    this.uidx.add(idx)
                    this.up = true
                }
                    
                if(np.CoinInfo.Name == oldPrice[idx].CoinInfo.Name && np.RAW.USD.PRICE < oldPrice[idx].RAW.USD.PRICE) {
                    this.didx.add(idx)
                    this.down = true
                }  
            })                
        }
    },
    methods: {
        getMarketCap() {
            axios.get('https://min-api.cryptocompare.com/data/top/mktcapfull?limit=50&tsym=USD')
            .then(response => {
                this.coins = response.data.Data
            })
            .catch(e => {
                console.log(e)
            })
        },
        sortByPrice(name) {
            if(name == 'price') {
                this.desc = !this.desc
                this.asc = false
                this.mktCap = false
            }

            if(name == 'price' && !this.desc && !this.mktCap) {
                this.asc = true
            }

            if(name == 'mktCap') {
                this.asc = false
                this.desc = false
                this.mktCap = true
            }
        }
    },
    created() {
        this.getMarketCap()
        let req = setInterval(() => this.getMarketCap(), 2000) 
        // I couldn't use websockets :(   
    },
    beforeDestroy() {
        clearInterval(this.req)
    } 
}
</script>

<style scoped lang="scss">
    @import url('https://fonts.googleapis.com/css?family=Lato');
    
    * {
        font-family: 'Lato', monospace;
    }

    .tracker-container {
        margin: 60px;
        font-family: Arial, Helvetica, sans-serif;

        table, th, td {
            border: 1px solid #ddd;
            border-left: none;
            border-right: none;
            border-collapse: collapse;
        }

        table {
            width: 100%; 
            margin: auto;
            margin-top: 40px;

            th {
                height: 30px;
                text-align: left;
                cursor: pointer;
            }
            
            .name {
                text-align: center;
            }

            td {
                height: 50px;
                /* padding: 0 50px 0 10px; */
                text-align: left;
            }

            .index {
                padding: 0 20px;
                text-align: center;
            }

            img {
                width: 20px;
                margin-right: 10px;
                vertical-align: middle;
            }

            .up {
                color: green;
            }

            .down {
                color: red;
            }

            .up-bg {
                animation: change-up 1s;
            }

            .down-bg {
                animation: change-down 1s;
            }

            @keyframes change-up {
                0% {background-color: rgba(0, 255, 0, 0)}
                50% {background-color: rgba(0, 255, 0, 0.1)}
                100% {background-color: rgba(0, 255, 0, 0)}
            }
            @keyframes change-down {
                0% {background-color: rgba(255, 0, 0, 0)}
                50% {background-color: rgba(255, 0, 0, 0.1)}
                100% {background-color: rgba(255, 0, 0, 0)}
            }
        }
    }
</style>
