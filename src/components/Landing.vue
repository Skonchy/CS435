<template>
    <div class="Landing">
        <div id="map" ref="map"></div>
        <div id="table" v-if="selected === true">
            <tr>
                <td>County Name</td>
                <td>Population</td>
                <td>Average Age</td>
                <td>Gender Ratio</td>
            </tr>
            <tr>
                <td>{{ selectedName }}</td>
                <td>{{ selectedPopulation }}</td>
                <td>{{ selectedAge }}</td>
                <td>{{ selectedGender }}</td>
            </tr>
        </div>
    </div>
</template>
<script>
    import gmapsInit from '@/utils/gmap';

    export default {
        name: "Landing",
        data() {
            return {
                map: null,
                countycoords: null,
                filter_array: [],
                filterclicked: false,
                countyclicked: false,
                selected:false,
                selectedName: '',
                selectedPopulation: '',
                selectedAge:'',
                selectedGender:'',
            }
        },
        mounted() {
            this.map = new window.google.maps.Map(this.$refs["map"],{
                center:{lat:38.678,lng:-90.199},
                zoom:7
            });
            this.countycoords = require('@/assets/countyzones.json');

            this.map.data.addGeoJson(this.countycoords);
            this.map.data.addListener("onclick", (event)=>{
                //TODO backend call to get county-specific data
                this.selected = !this.selected;
                this.selectedName = event.feature.getProperty("name");
                this.selectedPopulation = "Testing Population";
                this.selectedAge = "Testing Age";
                this.selectedGender = "Testing Gender";
            });
            this.map.data.addListener("mouseover", (event) => {
                this.map.data.revertStyle();
                this.map.data.overrideStyle(event.feature, { strokeWeight: 8 });
            });
            this.map.data.addListener("mouseout", (event) => {
                this.map.data.revertStyle();
            });
        },
        methods: {
            isJSON(data) {
                var ret = false;
                try {
                    JSON.parse(data);
                }catch(e) {
                    ret = true;
                }
                return ret;
            },
        },
    }
</script>
<style>
body {
    margin: 0;
    padding: 0;
}

#map {
    height:600px;
    margin:2rem;
    background:grey;
}

</style>