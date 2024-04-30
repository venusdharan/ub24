<template>
  <div>
    <v-container>
    <v-row>
      <v-col>
        <v-card flat>
          <v-card-title>
          <h3 class="grey--text">Test Agent Configuration</h3>
          </v-card-title>
          <v-divider/>
          <v-card-text>Download config file</v-card-text>
          <!-- <v-row>
                  <v-col cols="4">
                      <v-text-field label="Name" v-model="add_key_name"></v-text-field>
                  </v-col>
                  <v-col cols="4">
                      <v-select label="Agent Type" :items="agent_types" v-model="add_type_name"></v-select>
                  </v-col>
                  <v-col cols="8">
                       <v-text-field
                        v-model="add_key_value"
                        name="input-7-4"
                        label="Number"
                        ></v-text-field>
                  </v-col>
          </v-row> -->
          <v-card-actions>
              <v-btn color="green" class="white--text" @click="downloadObjectAsJson">Download Agent Config</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
    </v-container>
  </div>
</template>


<script>
  export default {
    data () {
      return {
          add_key_name : "",
          add_key_value : "",
          add_type_name : "",
          agent_types:[
            "Cypress",
            "Puppet",
            "Patch"
          ],
          api_keys: [

        ],
        config:{
          server_url: "http://localhost:4500",
          project_id: "c3lf1168r4s"
        }
      }
    },
    async mounted() {
       const apis = await this.$axios.get('/api/agents');
       console.log(apis.data);
      this.api_keys = apis.data;
    },
    methods: {
     async remove_key(id){

        var res = await this.$axios.post('/api/agents/delete',{_id : this.api_keys[id]._id})
        this.api_keys.splice(id, 1);
        // this.$toast.info("API Removed");
        // const apis = await this.$axios.get('/api/key/all');
        // this.api_keys = apis.data;
      },
      copy_api(){
        this.$toast.info("API Copied");
      },
      downloadObjectAsJson(){
        var dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(this.config,null, 2));
        var downloadAnchorNode = document.createElement('a');
        downloadAnchorNode.setAttribute("href",     dataStr);
        downloadAnchorNode.setAttribute("download", "agent.json"); //exportObj.name +
        document.body.appendChild(downloadAnchorNode); // required for firefox
        downloadAnchorNode.click();
        downloadAnchorNode.remove();
      },
      async add_api(){

        if(!this.add_type_name){
          alert("Please select agent type");
          return false;
        }

        const api = await this.$axios.post('/api/agents/add',{
          name: this.add_key_name,
          number:Math.floor(100000 + Math.random() * 900000), //this.add_key_value,
          agent_type : this.add_type_name
        });
        // if(api.data.status){
        this.api_keys.push(api.data);
        // this.$toast.success("API Created");
        // }else{
        //   this.$toast.error("API Creation Error !");
        // }


      }
    },
  }
</script>
