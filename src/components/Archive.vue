<template>
  <div class="archive-container" >
    
    <div class="archive-overlay" :id=iframeDivID >
      <div class="overlay-close" v-on:click=closePreview><img src="../assets/icon-close.png"/></div>
      <iframe :src= arcLink frameborder="0" style="border:none;"></iframe>
    </div>
    

      <div class="archive-box">
        <!-- <div v-if="arcAuto == 'auto'" class="archive-auto"></div>
        <div v-if="arcAuto == 'man'" class="archive-man"></div>-->
        <div class="archive-headline">{{ arcHeadline }}</div>
        <div v-if="arcPolopolyID.length!=0" class="archive-subline">Polopoly-ID: {{ arcPolopolyID }}</div>
        <div v-if="arcPolopolyID.length==0" class="archive-subline">&nbsp;</div>
        <a v-if="arcCSV.length!=0" :href= arcCSV target="_blank"><div class="csv-button">CSV</div></a>
        <a v-if="arcPolopolyID.length!=0" :href= arcEditLink target="_blank"><div class="edit-button"></div></a>
        <div class="archive-screenshot"><img :src= arcImg /></div>
        <div class="archive-button" v-if="arcPolopolyID.length!=0" v-on:click= getPreview>Vorschau</div>
        <div class="archive-button" v-if="arcPolopolyID.length!=0"><a :href= polopolyUrl target="_blank">Polopoly</a></div>
        <div class="archive-button confluence" v-if="arcPolopolyID.length==0"><a :href= arcLink target="_blank">Übersicht in Confluence</a></div>
      </div>
     
  </div>
  
    
    
</template>

<script>
export default {
  name: 'Archive',
  props: {
    arcHeadline: String,
    arcImg: String,
    arcPolopolyID: String,
    arcLink: String,
    arcCategory: String,
    hasCategory: Boolean,
    arcEditLink: String,
    arcAuto: String,
    arcCSV: String
  },
    computed: {
       polopolyUrl: function () {
        return "http://cms.faz.net/polopoly/content?eventDataClass=com.polopoly.cm.app.orchid.event.impl.EventDataUtil$ActionEventData&$action=view&$target=work&$contentId=" +this.arcPolopolyID  
    },
      iframeDivID: function () {
        let polID = this.arcPolopolyID.replace(".", "");
        return polID
      }
  },
  methods: {
    getPreview: function(){
      let prevOverlay = document.getElementById(this.iframeDivID)
      prevOverlay.style.visibility = "visible";
    },

    closePreview: function(){
      let prevOverlay = document.getElementById(this.iframeDivID)
      prevOverlay.style.visibility = "hidden";
    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import '../assets/style.css';
</style>
