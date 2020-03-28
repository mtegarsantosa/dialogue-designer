<template>
  <div>

    <div class="header shadow-sm">
      <div class="bg-info p-1 text-white"></div>
      <div class="bg-primary p-2 text-white">
        <div class="container">
          <div class="row">
            <div class="col-md-3">
              <h4><b>Dialogue</b> Designer!</h4>
            </div>
            <div class="col-md-9 text-center">
              <button class="btn-sm btn btn-warning" @click="tools('messages')">Add Message</button>
              <button class="btn-sm btn btn-success" @click="tools('options')">Add Option</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="workarea">
      <div class="output">
        <a href="javascript:" @click="outputClick()">Output</a>
        <div class="json p-3">
          <pre>{{dialogue}}</pre>
        </div>
      </div>
      <div class="drag bg-danger m-5">
        <div class="title">Add Character</div>
        <div class="body">
          <table class="w-100">
            <tr>
              <td>Name:</td>
              <td>
                <form @submit.prevent="addChar()">
                  <input type="text" class="form-control" id="characterName" autocomplete="off">
                </form>
              </td>
            </tr>
          </table>
          <ul v-if="dialogue.character.length > 0">
            <li v-for="(c,index) in dialogue.character" :key="index">{{c}} <a href="javascript:" class="text-danger" @click="deleteChar(index)">X</a></li>
          </ul>
        </div>
      </div>
      <div class="drag bg-warning" v-for="(msg,index) in dialogue.messages" :key="'m'+index" @mouseover="hoverMessageBlock(index)" :style="'top:'+dialogue.messages[index].style.top+'px;left:'+dialogue.messages[index].style.left+'px;'">
        <div class="title">
          Add Message
          <a href="javascript:" @click="deleteMessageBlock()" class="btn btn-danger btn-sm">X</a>
        </div>
        <div class="body">
          <table class="w-100">
            <tr>
              <td>Character:</td>
              <td>
                <select class="form-control" @change="getSelectedCharacter">
                  <option value="">-Select Character-</option>
                    <option v-for="(c,index) in dialogue.character" :key="index" :value="index">{{c}}</option>
                </select>
              </td>
            </tr>
            <tr>
              <td>Message:</td>
              <td>
                <input type="text" class="form-control" autocomplete="off" @keyup="typeMessage">
              </td>
            </tr>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data(){
    return{
      menu:{ // To detect all block
        characters:{
        },
        messages:{
          selected:'', // Selected Message
        },
        options:{
        }
      },
      dialogue:{
  "character": [
    "tegar",
    "bagas"
  ],
  "messages": [
    {
      "id": 0,
      "character": 0,
      "text": "apakabar",
      "next": "",
      "style": {
        "top": 118.66667175292969,
        "left": 358
      }
    },
    {
      "id": 1,
      "character": 1,
      "text": "baik",
      "next": "",
      "style": {
        "top": 279,
        "left": 148
      }
    }
  ]
}
    }
  },
  methods:{
    outputClick(){ // Show Hide Output
      this.$(".json").toggle()
    },
    tools(x){ // When Menu OnClick
      if (x === 'messages') { // Create Ready Object On dialogue.message
        let index = this.dialogue.messages.length
        let id = index
        id = index==0 ? id : this.dialogue.messages.slice(-1)[0].id+1 // Autoincrement ID
        this.dialogue.messages.push({id:id,character:"",text:"",next:"",style:{top:0,left:0}})
      }
      var _this = this
      this.$nextTick(function () {
        $('.drag').draggable({
          drag: function(){
            let offset = $(this).offset()
            let style = _this.dialogue.messages[_this.menu.messages.selected].style
            style.top = offset.top-55
            style.left = offset.left
          }
        })
      })
    },
    getSelectedCharacter(e){
      let index = this.dialogue.messages.findIndex(x => x.id == this.menu.messages.selected)
      this.dialogue.messages[index].character = parseInt(e.target.value)
    },
    typeMessage(e){
      let index = this.dialogue.messages.findIndex(x => x.id == this.menu.messages.selected)
      this.dialogue.messages[index].text = e.target.value
    },
    hoverMessageBlock(index){ // When Message Block Hovered
      this.menu.messages.selected = index
    },
    addChar(){ // Add New Character
      this.dialogue.character.push(this.$("#characterName").val())
      this.$("#characterName").val('')
    },
    // deleteMessageBlock(){ // Delete Messages Block
    //   this.swal({
    //     title: "Are you sure?",
    //     text: "This will delete relation for this block",
    //     icon: "warning",
    //     buttons: true,
    //     dangerMode: true,
    //   })
    //   .then((willDelete) => {
    //     if (willDelete) {
    //       let index = this.dialogue.messages.findIndex(x => x.id == this.menu.messages.selected)
    //       this.dialogue.messages.splice(index,1)
    //     }
    //   })
    // },
    deleteChar(index){ // Delete Character
      this.dialogue.character.splice(index,1)
    }
  },
  mounted(){
    var _this = this
    this.$nextTick(function () {
      $('.drag').draggable({
        drag: function(){
          let offset = $(this).offset()
          let style = _this.dialogue.messages[_this.menu.messages.selected].style
          style.top = offset.top-55
          style.left = offset.left
        }
      })
    })
  }
}
</script>

<style scoped>
  #ready{
    display: none;
  }
  .header{
      max-height: 55px;
      overflow: hidden;
  }
  .workarea{
    position: absolute;
    /* background: url('../assets/img/bg-grid.png'); */
    width: 100%;
    bottom: 0;
    top:55px;
    z-index:-1;
    background-size: 100% 100%;
    overflow-x: scroll;
    overflow-y: scroll;
  }
  .drag{
    max-width: 230px;
    cursor: all-scroll;
    position: absolute;
  }
  .drag .title{
    color: #FFF;
    padding: 5px;
  }
  .drag .body{
    padding: 3px;
    border: 1px solid #DDD;
    border-top:none;
    background: #FFF;
  }
  .drag.addCharacter{
    max-width: 200px;
  }
  .output{
    position: fixed;
    right:0;
    bottom:0;
  }
  .output a{
    position: fixed;
    bottom: 20px;
    right: 320px;
  }
  .output .json{
    width: 300px;
    border: 1px solid #DDD;
    max-height: 200px;
    height: 200px;
    overflow: scroll;
  }
</style>
