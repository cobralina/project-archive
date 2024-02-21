<template>
  <div id="archive-app">
    <div class="switch-button"><a href="https://dynamic.faz.net/red/2022/infografik-dauerbrenner/">Zu den Dauerbrennern</a></div>
    <h1>Dashboard Live-Grafiken</h1>

    <div class="archive-search-wrapper">
      Grafik suchen:<br>
    <input class="archive-input" type="text" v-model="search" placeholder= "Schlagwort... "/>
  </div>

  <div class="archive" v-if="search.length != 0 ">
  <div class="archive-flex-container">
    <div class="archive-category">Suchergebnisse nach "{{ search }}"</div>  
    <li class="vue-list" v-for="item in filteredCompleteList" :key="item">     
     <Archive v-bind:arcHeadline=item.infoHeadline v-bind:arcPolopolyID=item.infoPolopoly v-bind:arcCategory=item.infoCategory v-bind:arcImg=item.imgUrl v-bind:arcLink=item.infoLink v-bind:arcEditLink=item.editUrl v-bind:arcCSV=item.infoCSV v-bind:arcAuto=item.infoAuto />
    </li>
    <div v-if="filteredCompleteList.length == 0"> Es sind keine Grafiken zu "{{ search }}" vorhanden.</div>
  <br><br><br>
   </div>
   <div class="filter-headline"></div>
  </div>
   
  <div class="archive-filter-wrapper" >
    <div class="filter-headline">Nach Themen filtern:</div>
    <li class="vue-list" v-for="item in arrUniqueCategorys" :key="item">     
      <input type="checkbox" v-bind:id="item" v-bind:value="item" v-model="checkedCategorys" checked>
      <label class="archive-checkbox" v-bind:for="item"> {{ item }} </label>
    </li>
  </div>
 

 <div class="archive" >

  <li class="vue-list" v-for="cat in arrUniqueCategorys" :key="cat">     
  <div class="archive-flex-container" v-if="checkedCategorys.includes(cat)">
    <div class="archive-category">Alle Grafiken zu {{ cat }} </div>  
    <li class="vue-list" v-for="item in objSortedCategorys[cat]" :key="item">     
     <Archive v-bind:arcHeadline=item.infoHeadline v-bind:arcPolopolyID=item.infoPolopoly v-bind:arcCategory=item.infoCategory v-bind:arcImg=item.imgUrl v-bind:arcLink=item.infoLink v-bind:arcEditLink=item.editUrl v-bind:arcCSV=item.infoCSV v-bind:arcAuto=item.infoAuto />
    </li>
    <div v-if="objSortedCategorys[cat].length == 0">Es sind keine Grafiken zum Thema vorhanden.<br><br></div>
    </div>
    </li>

    <div class="filter-headline"></div>
    <a class="toplink" href="#">nach oben</a>
    <div class="filter-headline"></div>
  </div>
 
  <div class="filter-headline"></div>
</div>
</template>

<script>
import Archive from './components/Archive.vue'

export default {
  name: 'App',
  components: {
    Archive
  },
  data () {
    return {
      search: '',
      googleData:[],
      data_url:"https://docs.google.com/spreadsheets/d/e/2PACX-1vSoXVmGaAHJadH-xWDj-OzpbolqcGYrNTH3MOCJiv3o7DP1l85UVV6bAvxe0iyaWBKilJXCKMhQTd8D/pub?output=csv",
      checkedCategorys: [],
      arrCategorys: [],
      arrUniqueCategorys: [],
      objSortedCategorys: {}
    }
  },
  computed: {

    filteredCompleteList() {
      return this.googleData.filter(item => {
        return item.infoHeadline.toLowerCase().includes(this.search.toLowerCase()) || item.infoCategory.toLowerCase().includes(this.search.toLowerCase())
      })
    }
  },
  methods: {
    getGoogleData: function (_url) {
        
        let self = this
        let url = _url + '&gid=0' 
        
        this.$papa.parse(url, {
            download: true,
            header: true,
            complete: function(results) {              
             

                  for (var j = 0; j < results.data.length; j++) {
                    let dataObject = results.data[j]
                    //console.log(results.data[j])
                    self.googleData.push(dataObject)
                
                    if (dataObject.infoSource == "infogram" || dataObject.infoSource == "Infogram") {
                      let imgUrl = "https://infogram-thumbs-200.s3-eu-west-1.amazonaws.com/"+dataObject.infoID+".jpg"
                      let editUrl = "https://infogram.com/app/#/edit/"+dataObject.infoID;
                      self.googleData[j].imgUrl = imgUrl;
                      self.googleData[j].editUrl = editUrl;

                    }
                    if (dataObject.infoSource == "datawrapper" || dataObject.infoSource == "Datawrapper") {
                      let imgUrl = "https://img.datawrapper.de/"+dataObject.infoID+"/plain.png"
                      let editUrl="https://app.datawrapper.de/chart/"+dataObject.infoID+"/visualize#refine";
                      self.googleData[j].imgUrl = imgUrl;
                      self.googleData[j].editUrl = editUrl;
                    }
                    if (dataObject.infoSource == "MM" || dataObject.infoSource == "mm") {
                      let imgUrl = dataObject.infoLink+"/img/screenshot.png"
                      let editUrl = dataObject.infoLink;
                      self.googleData[j].imgUrl = imgUrl;
                      self.googleData[j].editUrl = editUrl;
                    }
                    if (dataObject.infoSource == "dpa" || dataObject.infoSource == "dpa") {
                      let imgUrl = "https://dynamic.faz.net/red/2022/infographic-archive/img/dpa.png"
                      let editUrl=dataObject.infoLink;
                      self.googleData[j].imgUrl = imgUrl;
                      self.googleData[j].editUrl = editUrl;
                    }
                    if (dataObject.infoSource == "") {
                      self.googleData[j].imgUrl ="https://dynamic.faz.net/red/2022/infographic-archive/default.png"
                      self.googleData[j].editUrl = "https://dynamic.faz.net/red/2022/infographic-archive";
                    }
                    self.arrCategorys.push(self.googleData[j].infoCategory);
                    self.arrUniqueCategorys = [...new Set(self.arrCategorys)];
                    //self.objSortedCategorys = self.arrUniqueCategorys;
                    

                    
                  }
                  
                  self.getCategoryData(self.googleData)

            }
        });
        
    },
    getCategoryData: function(_dataSet) {

      for (var k = 0; k < this.arrUniqueCategorys.length; k++)
      {
      this.objSortedCategorys[this.arrUniqueCategorys[k]] = []
      }
      //this.objSortedCategorys[1] = []

      for (var j = 0; j < _dataSet.length; j++) {
        let dataRow = _dataSet[j];

      for (var l = 0; l < this.arrUniqueCategorys.length+1; l++)
      {
        if(dataRow.infoCategory == this.arrUniqueCategorys[l]) {
        this.objSortedCategorys[this.arrUniqueCategorys[l]].push(dataRow);
      }
    }
       
        }
        this.checkedCategorys = this.arrUniqueCategorys;
      
    }
    
},

  mounted () {
    
    this.getGoogleData(this.data_url);

  }
}
</script>

<style scoped>
@import './assets/style.css';

.switch-button {
    font-size: 13px;
    line-height: 25px;
    height:25px;
    color: #666666;
    background-color: #e6e6e6;
    text-align: center;
    border-radius: 3px;
    padding-left: 16px;
    padding-right:16px;
    margin:5px;
    transition: background-color .5s;
    cursor:pointer;
    position:absolute;
    right:20px;
    }

    .switch-button:hover {
    color: #333333;
    background-color: #bebebe;
    }

    .switch-button a {
      text-decoration:none;
      color:#666666
    }

@media screen and (max-width:780px) {
  .switch-button {
    position:relative;
    display:block;
    margin: 20 auto;
    right:auto;
    }
}
</style>
