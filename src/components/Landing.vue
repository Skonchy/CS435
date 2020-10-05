<template>
    <div class="Landing">
        <div id="map" ref="map"></div>
        <div class = "table">
            <table id="table" v-if="selected === true">
            <tr>
                <td>State</td>
                <td>County ID</td>
                <td>County Name</td>
                <td>Population</td>
                <td>Average Age</td>
                <td>Gender Ratio</td>
            </tr>
            <tr>
                <td>{{ selectedState }}</td>
                <td>{{ selectedID }}</td>
                <td>{{ selectedName }}</td>
                <td>{{ selectedPopulation }}</td>
                <td>{{ selectedAge }}</td>
                <td>{{ selectedGender }}</td>
            </tr>
            </table>
        </div>
    </div>
</template>
<script>
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
                selectedState:'',
                selectedID:'',
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
            this.map.data.addListener("click", (event)=>{
                //TODO backend call to get county-specific data
                console.log("Click Event",event.feature.j,event.feature.j.NAME);
                this.selected = !this.selected;
                this.selectedState = event.feature.j.STATE;
                this.selectedID = event.feature.j.COUNTY;
                this.selectedName = event.feature.j.NAME;
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