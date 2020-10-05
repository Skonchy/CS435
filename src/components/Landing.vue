<template>
    <div class="Landing">
        <div id="map" ref="map"></div>
        <div class = "table" v-if="selected === true">
            <table class="table">
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
            //generate a google map centered on St. Louis
            this.map = new window.google.maps.Map(this.$refs["map"],{
                center:{lat:38.678,lng:-90.199},
                zoom:7
            });
            //import xounty coord data and apply it to the map above
            this.countycoords = require('@/assets/countyzones.json');
            this.map.data.addGeoJson(this.countycoords);

            //geoJSON map area events
            this.map.data.addListener("click", (event)=>{
                //TODO backend call to get county-specific data

                //debug prints
                console.log("Click Event",event.feature.j.STATE,this.gidToName(event.feature.j.STATE));
                //if the same county is selected, hide data
                this.selected = ((this.selectedName === event.feature.j.NAME) ? this.selected = false : this.selected = true);
                //convert 2 digit code to state name using census data and assign rest of data for table
                this.selectedState = this.gidToName(event.feature.j.STATE);
                this.selectedID = event.feature.j.COUNTY;
                this.selectedName = event.feature.j.NAME;
                this.selectedPopulation = "Testing Population";
                this.selectedAge = "Testing Age";
                this.selectedGender = "Testing Gender";

                this.map.data.revertStyle();
                if(this.selected === true) {
                    this.map.data.overrideStyle(event.feature, { strokeWeight: 8, strokeColor: "blue" });
                }
            });
            /*this.map.data.addListener("mouseover", (event) => {
                this.map.data.revertStyle();
                this.map.data.overrideStyle(event.feature, { strokeWeight: 8 });
            });
            this.map.data.addListener("mouseout", (event) => {
                this.map.data.revertStyle();
            });*/
        },
        methods: {
            gidToName(gid){
                switch(gid){
                    case '01':
                        return 'Alabama';
                    case '02':
                        return 'Alaska';
                    case '04':
                        return 'Arizona';
                    case '05':
                        return 'Arkansas';
                    case '06':
                        return 'California';
                    case '08':
                        return 'Colorado';
                    case '09':
                        return 'Connecticut';
                    case '10':
                        return 'Delaware';
                    case '11':
                        return 'District of Columbia';
                    case '12':
                        return 'Florida';
                    case '13':
                        return 'Georgia';
                    case '15':
                        return 'Hawaii';
                    case '16':
                        return 'Idaho';
                    case '17':
                        return 'Illinois';
                    case '18':
                        return 'Indiana';
                    case '19':
                        return 'Iowa';
                    case '20':
                        return 'Kansas';
                    case '21':
                        return 'Kentucky';
                    case '22':
                        return 'Louisiana';
                    case '23':
                        return 'Maine';
                    case '24':
                        return 'Maryland';
                    case '25':
                        return 'Massachusetts';
                    case '26':
                        return 'Michigan';
                    case '27':
                        return 'Minnesota';
                    case '28':
                        return 'Mississippi';
                    case '29':
                        return 'Missouri';
                    case '30':
                        return 'Montana';
                    case '31':
                        return 'Nebraska';
                    case '32':
                        return 'Nevada';
                    case '33':
                        return 'New Hampshire';
                    case '34':
                        return 'New Jersey';
                    case '35':
                        return 'New Mexico';
                    case '36':
                        return 'New York';
                    case '37':
                        return 'North Carolina';
                    case '38':
                        return 'North Dakota';
                    case '39':
                        return 'Ohio';
                    case '40':
                        return 'Oklahoma';
                    case '41':
                        return 'Oregon';
                    case '42':
                        return 'Pennsylvania';
                    case '44':
                        return 'Rhode Island';
                    case '45':
                        return 'South Carolina';
                    case '46':
                        return 'South Dakota';
                    case '47':
                        return 'Tennessee';
                    case '48':
                        return 'Texas';
                    case '49':
                        return 'Utah';
                    case '50':
                        return 'Vermont';
                    case '51':
                        return 'Virginia';
                    case '53':
                        return 'Washington';
                    case '54':
                        return 'West Virginia';
                    case '55':
                        return 'Wisconsin';
                    case '56':
                        return 'Wyoming';
                }
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
    outline: 2px solid grey;
}

div.table {
    margin: 10px auto 20px auto;
    width: 50%;
    outline: 2px solid grey;
    display: flex;
    justify-content: center;
}

table.table {
    align-self:center
}

</style>