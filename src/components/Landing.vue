<template>
    <div class="Landing">
        <div class="container">

            <div id="filter">
                <h4>Filter     <button class="icon" id="search" @click="filtered = !filtered"><i class="fa fa-search"></i></button></h4>
                <div id="options" v-if="filtered">
                    <input type="radio" value="age" v-model="filter" v-on:change="getDemoData(filter)"> <label>Age</label>
                    <input type="radio" value="male" v-model="filter" v-on:change="getDemoData(filter)"> <label>Male</label>
                    <input type="radio" value="female" v-model="filter" v-on:change="getDemoData(filter)"> <label>Female</label>
                    <input type="radio" value="pop" v-model="filter" v-on:change="getDemoData(filter)"> <label>Population</label>
                </div>
            </div>
            <div id="legend"><span style="color: green">Min: {{censusMin}} </span> <span style="color: #ff4c00">Max: {{censusMax}} </span></div>
            <div id="map" ref="map"></div>
            
        </div>
        <div class = "table" v-if="selected === true">
            <table class="table">
            <tr>
                <td>State</td>
                <td>County ID</td>
                <td>County Name</td>
                <td>Population</td>
                <td>Average Age</td>
                <td><button type="submit" @click="saveFile()">Export Data</button></td>
            </tr>
            <tr>
                <td>{{ selectedState }}</td>
                <td>{{ selectedID }}</td>
                <td>{{ selectedName }}</td>
                <td>{{ selectedPopulation }}</td>
                <td>{{ selectedAge }}</td>
                <td><button type="submit" @click="saveFileAll()">Export All</button></td>
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
                filter: "pop",
                filtered: false,
                countyclicked: false,
                censusMin: Number.MAX_VALUE,
                censusMax: 0,
                selected:false,
                selectedState:'',
                selectedID:'',
                selectedName: '',
                selectedPopulation: '',
                selectedAge:'',
                demoData:'',
                countyData:'',
            }
        },
        mounted() {
            //generate a google map centered on St. Louis
            this.map = new window.google.maps.Map(this.$refs["map"],{
                center:{lat:38.678,lng:-90.199},
                zoom:7
            });
            //import county coord data and apply it to the map above
            this.countycoords = require('@/assets/countyzones.json');
            this.map.data.addGeoJson(this.countycoords, {
                idPropertyName: "GEO_ID"
            });

            this.map.data.forEach((feature)=>{
                this.map.data.overrideStyle(feature, { strokeWeight: 1, fillOpacity: 0.1});
            });

            //bring in map data
            this.getDemoData(this.filter);

            //geoJSON map area events
            this.map.data.addListener("click", (event)=>{
                //TODO backend call to get county-specific data
                this.getCountyData(event.feature.j.STATE,event.feature.j.COUNTY);
                //debug prints
                console.log("Click Event",event.feature.j.STATE,this.gidToName(event.feature.j.STATE), "id: "+event.feature.getId());
                //if the same county is selected, hide data
                this.selected = ((this.selectedID === event.feature.j.COUNTY) ? this.selected = false : this.selected = true);
                //convert 2 digit code to state name using census data and assign rest of data for table
                this.selectedState = this.gidToName(event.feature.j.STATE);
                this.selectedID = event.feature.j.COUNTY;
                this.selectedName = event.feature.j.NAME;
                this.selectedPopulation = this.countyData[1];
                this.selectedAge = this.countyData[2];

                //this.map.data.revertStyle();
                // if(this.selected === true) {
                //     this.map.data.overrideStyle(event.feature, { strokeWeight: 6, strokeColor: "blue" });
                // }
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

            rgb(values){
                return 'rgb(' + values.join(', ') + ')';
            },
            //function for populating entire map
            getDemoData(filter){
                this.censusMin = Number.MAX_VALUE;
                this.censusMax = 0;
                axios.get("http://localhost:5000/"+filter).then(response => {
                    this.demoData = response.data; 
                    console.log(this.demoData)
                    for(let row in this.demoData){
                        const county = this.demoData[row];
                        const key = county[0];
                        const censusVar = parseFloat(county[1]);

                        if (censusVar < this.censusMin) {
                            this.censusMin = censusVar;
                            if(censusVar == 0){
                                console.log("0 POP:"+key)
                            }
                        }
                        if (censusVar > this.censusMax) {
                            this.censusMax = censusVar;
                        }
                        let featureID = "0500000US"+key.toString();
                        let curFeature = this.map.data.getFeatureById(featureID);
                        if(curFeature != undefined){
                            curFeature.setProperty("CENSUSVAR", censusVar);
                            this.styleFeature(curFeature);
                        }
                    }
                }).catch("Error in Country Data Get Request");
                
            },

            getCountyData(stateID, countyID){
                console.log(countyID);
                axios.get("http://localhost:5000/"+stateID+"/"+countyID).then(response => {
                    this.countyData = response.data[0];
                    console.log(this.countyData);
                    }).catch("Error in County Data Get Request");
                
            },

            styleFeature(feature){
                if(feature != undefined){
                    const high = [25, 83, 50]; // color of largest
                    const low = [151, 83, 34]; // color of smallest
                    // delta represents where the value sits between the min and max
                    const delta = (feature.getProperty("CENSUSVAR") - this.censusMin) / (this.censusMax - this.censusMin);
                    const color = [];

                    for (let i = 0; i < 3; i++) {
                        color.push((high[i] - low[i]) * delta + low[i]);
                    }

                    console.log(this.censusMin, this.censusMax);

                    this.map.data.overrideStyle(feature, {
                        fillColor:"hsl(" + color[0] + "," + color[1] + "%," + color[2] + "%)",
                        fillOpacity: 0.75
                    });
                }
            },


            saveFile(){
                console.log("Attempting to save File")
                const dataJSON = {
                    selectedState: this.selectedState,
                    selectedID: this.selectedID,
                    selectedName: this.selectedName,
                    selectedPopulation: this.selectedPopulation,
                    selectedAge: this.selectedAge,
                    selectedGender: this.selectedGender}
                const data = JSON.stringify(dataJSON)
                const blob = new Blob([data], {type: 'text/plain'});
                const e = document.createEvent('MouseEvents'),
                a = document.createElement('a');
                a.download = "export.json";
                a.href = window.URL.createObjectURL(blob);
                a.dataset.downloadurl = ['text/json', a.download, a.href].join(':');
                e.initEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
                a.dispatchEvent(e);
            },

            saveFileAll(){
                console.log("Attempting to save File")
                const data = JSON.stringify(this.demoData);
                const blob = new Blob([data], {type: 'text/plain'});
                const e = document.createEvent('MouseEvents'),
                a = document.createElement('a');
                a.download = "export.json";
                a.href = window.URL.createObjectURL(blob);
                a.dataset.downloadurl = ['text/json', a.download, a.href].join(':');
                e.initEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
                a.dispatchEvent(e);
            }
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

#search {
  width: 5%;
  padding: 10px;
  background: #2196f3;
  color: white;
  font-size: 17px;
  border: 1px solid grey;
  border-left: none; /* Prevent double borders */
  cursor: pointer;
}

table.table {
    align-self:center
}

</style>