<script>
  import axios from 'axios' 
  import {XMLParser} from 'fast-xml-parser'
  export default {
    data() {
      return {
        imgPath: '',
        imgAlt: '',
        caption1: '',
        caption2: ''
      }
    },

    async created() {
      const options = {
        ignoreAttributes : false
      };
      const parser = new XMLParser(options);
      try {
      await axios.get('https://vue-project-pearl.vercel.app/src/assets/data.xml', { crossdomain: true })
        .then(response => {
          var xmlText = response.data
          var result1 = parser.parse(xmlText)
          console.log(result1.config.dropdown.d[0]["@_end_time"].slice(11,13))
          const data = [
            // result1.config.dropdown.d[0]["@_start_time"].slice(11,13) --Retrives the start/end time of the asssets --.slice() method used to get the hour
            // 12am to 12am
            [result1.config.dropdown.d[0]["@_start_time"].slice(11,13), result1.config.dropdown.d[0]["@_end_time"].slice(11,13)],
            // 12am to 6pm
            [result1.config.dropdown.d[1]["@_start_time"].slice(11,13), result1.config.dropdown.d[1]["@_end_time"].slice(11,13)],
            // 6pm to midnight
            [result1.config.dropdown.d[2]["@_start_time"].slice(11,13), result1.config.dropdown.d[2]["@_end_time"].slice(11,13)],
          ]
          //get current system time
          const hr = new Date().getHours();

          for(var i=0; i<data.length;i++){
            if(hr >= data[i][0] && hr <= data[i][1]){
                const captionArr = result1.config.schedules.s[i].caption.split(" ");
                this.caption1 = captionArr[0]
                this.caption2 = captionArr[1]
                this.imgPath = 'src/assets/'+result1.config.assets.asset[i]["@_path"]
                this.imgAlt = result1.config.assets.asset[i]["@_id"]
                break;
            }
          }
        })
        //refresh page every hour to update display
        setTimeout(function(){
          window.location.reload(1);
        }, 3600000);

        //get dom load time
        function printNavTimingData() {
          // Use getEntriesByType() to just get the "navigation" events
          performance.getEntriesByType("navigation")
            .forEach((p) => {
              console.log(`DOM Load Time = ${p.domInteractive}`);
            });
        }
        printNavTimingData()
    } catch (error) {
      console.log(error);
    }
  }
  }
</script>
<template>
  <div class="bg-text position1">
    {{this.caption1}}
  </div>
  <div class="bg-text position2">
    {{this.caption2}}
  </div>
  <img v-bind:src="imgPath" v-bind:alt="imgAlt" />
</template>