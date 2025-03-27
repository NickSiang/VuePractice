<script>
export default {
    data() {
        return {
            dataName: '今明36小時天氣預報',
            cityWeatherData: [],
            time: null,
            selectedCity: 'all',
            regionAll: [
                '基隆市', '新北市', '臺北市', '桃園市', '新竹市', '新竹縣', '苗栗縣', '臺中市', '南投縣', '彰化縣', '雲林縣', '嘉義市', '嘉義縣', '臺南市', '高雄市', '屏東縣',
                '宜蘭縣', '花蓮縣', '臺東縣', '澎湖縣', '金門縣', '連江縣'
            ],
        }
    },
    mounted() {
        fetch('https://opendata.cwa.gov.tw/api/v1/rest/datastore/F-C0032-001?Authorization=CWA-98DE9976-8906-41AD-A1E7-2599F553F4A3')
            .then(response => response.json())
            .then(data => {
                console.log(data);

                this.cityWeatherData = data.records.location.sort((a, b) => {
                    return this.regionAll.indexOf(a.locationName) - this.regionAll.indexOf(b.locationName);
                });

                console.log(this.cityWeatherData);

                // this.cityWeatherData = data.records.location;
            })

            .catch(error => {
                console.error('資料獲取錯誤', error)
                alert('請稍後再試');
            });

        const updateTime = () => {
            this.time = new Intl.DateTimeFormat("zh-TW", {
                year: "numeric",
                month: "2-digit",
                day: "2-digit",
                hour: "2-digit",
                minute: "2-digit",
                second: "2-digit",
                hour12: false,
            }).format(new Date());
        };
        updateTime();
        setInterval(updateTime, 1000);

    },
    methods: {
        picture(number) {
            if (number < 10) {
                return '0' + number;
            }
            return number;

        },
        onScreenData() {
            if (this.selectedCity === 'all') {
                return this.cityWeatherData;
            }

            return [this.cityWeatherData[Number(this.selectedCity)]];
        }

    },

}

</script>

<template>
    <div id="app">
        <div class="container">

            <div class="text-block">
                <h1>{{ dataName }}</h1>
                <h3>現在時間: {{ time }}</h3>
            </div>

            <div class="input-block">
                <select name="" id="" v-model="selectedCity">
                    <option value="all" selected>全部</option>
                    <option v-for="(item, index) in cityWeatherData" :key="index" :value="index">
                        {{ item.locationName }}
                    </option>
                </select>
            </div>

            <div class="card-region">
                <div class="card" v-for="(item, index) in onScreenData()" :key="index">
                    <h3>{{ item.locationName }}</h3>
                    <img
                        :src="`https://www.cwa.gov.tw/V8/assets/img/weather_icons/weathers/svg_icon/day/${picture(item.weatherElement[0].time[0].parameter.parameterValue)}.svg`">
                    <p>
                        {{ ((Number(item.weatherElement[2].time[0].parameter.parameterName) +
                            Number(item.weatherElement[4].time[0].parameter.parameterName)) / 2 ).toFixed(0)}}º
                    </p>
                    <span>{{ item.weatherElement[0].time[0].parameter.parameterName }}</span>
                    <br /><br />
                    <p><i class="fa-solid fa-droplet"></i> {{ item.weatherElement[1].time[0].parameter.parameterName }}%
                    </p>
                    <br />
                    <p><i class="fa-solid fa-temperature-low"></i> {{
                        item.weatherElement[2].time[0].parameter.parameterName }}° /
                        {{ item.weatherElement[4].time[0].parameter.parameterName }}°</p>
                </div>
            </div>

        </div>
    </div>

</template>

<style scoped>
#app {
    background-color: rgb(50, 146, 255);
    font-weight: 600;
    color: rgba(211, 233, 252, 0.832);
}

i {
    color: rgba(211, 233, 252, 0.655);
    margin-right: 4px;
}

p {
    margin: 0;
    text-align: center;
    font-size: 18px;
}

h3 {
    font-size: 24px;
    margin: 0 0 12px;
}

.container {
    width: 100%;
    min-height: 100vh;
    padding: 60px 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
    background: linear-gradient(rgba(83, 102, 240, 0.768), rgba(62, 188, 246, 0.774), rgba(240, 196, 75, 0.536));
}

.text-block {
    margin: 40px 0;
    text-align: center;
}

.input-block {
    max-width: 600px;
    width: 60%;
    padding: 2px;
    background-color: rgba(131, 131, 131, 0.193);
    border-radius: 10px;
}

.input-block select {
    width: 100%;
    height: 36px;
    border: none;
    outline: none;
    border-radius: 8px;
    padding: 0 10px;
    font-size: 16px;
    box-shadow: 0 1px 6px 0 #20212447;
    position: relative;
}

.card-region {
    max-width: 1280px;
    padding: 100px 12%;
    display: flex;
    flex-wrap: wrap;
    gap: 40px;
}

.card {
    min-width: 270px;
    width: calc((100% - 80px)/3);
    background: rgba(0, 0, 0, 0.136);
    backdrop-filter: blur(5px);
    border-radius: 20px;
    padding: 30px;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
}

.card img {
    width: 100%;
    height: 80px;
    padding: 20px 0 0;
    border-top: 2px solid rgba(255, 255, 255, 0.216);

}

.card img+p {
    font-size: 38px;
    /* background-color: aqua; */
    padding: 10px;
}

@media (max-width: 1226px) {
    .card-region {
        justify-content: center;
        align-items: center;
    }

}

@media (max-width: 570px) {
    h1 {
        font-size: 26px;
    }

    h3 {
        font-size: 20px;
    }
}
</style>