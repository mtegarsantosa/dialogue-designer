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
              <button class="btn-sm btn btn-warning mr-3" @click="addMessageBlock()">Add Message</button>
              <button class="btn-sm btn btn-success">Add Option</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="output">
        <a href="javascript:" @click="outputClick()">Output</a>
        <div class="json p-3">
        <pre>{{dialogue}}</pre>
      </div>
    </div>
    <div class="workarea">
      <div class="block bg-danger character" @mouseover="hoverCharacterBlock()">
        <div class="title">Add Character</div>
        <div class="body">
          <table class="w-100">
            <tr>
              <td>Name:</td>
              <td>
                <form @submit.prevent="addChar()">
                  <input type="text" class="form-control" ref="characterName" autocomplete="off">
                </form>
              </td>
            </tr>
          </table>
          <ul v-if="dialogue.character.length > 0">
            <li v-for="(c,index) in dialogue.character" :key="index">{{c}} <a href="javascript:" class="text-danger" @click="deleteChar(index)">X</a></li>
          </ul>
        </div>
      </div>
      <div class="block bg-warning message" v-for="(m,index) in dialogue.messages" :key="index" @mouseover="hoverMessageBlock(index)" :id="dialogue.messages[index].id" :style="'top:'+dialogue.messages[index].style.top+'px;left:'+dialogue.messages[index].style.left+'px;'">
        <div class="title">
          Add Message
          <a @click="deleteMessageBlock(index)" href="javascript:" class="btn btn-danger btn-sm">X</a>
        </div>
        <div class="body">
          <table class="w-100">
            <tr>
              <td>Character:</td>
              <td>
                <select class="form-control" @change="selectCharForMessage">
                  <option value="">-Select Character-</option>
                  <option v-for="(c,cindex) in dialogue.character" :key="cindex" :value="cindex" :selected="dialogue.messages[index].character==cindex?true:false">{{c}}</option>
                </select>
              </td>
            </tr>
            <tr>
              <td>Message:</td>
              <td>
                <input type="text" class="form-control" autocomplete="off" :id="'msg'+index" :value="dialogue.messages[index].text" @keyup="changeTextMessage(index)">
              </td>
            </tr>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import queen from '../library/Queen'
export default{
  data(){
    return{
      menu:{
        characters:{
          selected: false,
        },
        messages:{
          selected:''
        }
      },
      dialogue:{
  "character": [
    "Bagas",
    "Arsan"
  ],
  "messages": [
    {
      "id": "ss5Hx",
      "character": "1",
      "text": "Hi, Bagas!",
      "next": "K8BFy",
      "style": {
        "top": 88,
        "left": 310
      }
    },
    {
      "id": "K8BFy",
      "character": "0",
      "text": "Hi,  Arsan!",
      "next": "lsKV3",
      "style": {
        "top": 150,
        "left": 620
      }
    },
    {
      "id": "lsKV3",
      "character": "0",
      "text": "Whats up",
      "next": "Badzj",
      "style": {
        "top": 316,
        "left": 387
      }
    },
    {
      "id": "Badzj",
      "character": "0",
      "text": "I'm good. Its been a long time not to see you",
      "next": "-",
      "style": {
        "top": 401,
        "left": 53
      }
    }
  ]
}
    }
  },
  methods:{
    // Inner Library
    outputClick(){ // Show Hide Output
      $(".output .json").toggle()
    },
    draggable(){ // Draggable Block Function
      var _this = this
      function renderLine(){
        var paths = []
        for (var i = 0; i < _this.dialogue.messages.length; i++) {
          paths.push({ start: "#"+_this.dialogue.messages[i].id, end: "#"+_this.dialogue.messages[i].next})
        }
        $(".workarea").HTMLSVGconnect({paths:paths})
        $(".workarea").HTMLSVGconnect("reset")
      }
      renderLine()
      this.$nextTick(function () {
        $(".block").draggable()
        $(".workarea").droppable({
          drop: function(e,l){
            if (_this.menu.messages.selected !== '') {
              let offset = l.position
              let style = _this.dialogue.messages[_this.menu.messages.selected].style
              style.top = offset.top
              style.left = offset.left
            }
            renderLine()
          }
        })
      })
    },
    getMessageIndexById(id){
      return this.dialogue.messages.findIndex(x => x.id == id)
    },
    hoverMessageBlock(index){ // When Message Block Hovered
      this.menu.messages.selected = index
      $(".block.message").css({zIndex:'1'})
      $("#"+this.dialogue.messages[this.menu.messages.selected].id).css({zIndex:'2'})
      this.draggable() // Activate Draggable Function For This Block
    },
    hoverCharacterBlock(){ // When Message Block Hovered
      this.menu.messages.selected = ''
    },
    // Inner Library

    addChar(){ // Add New Character
      this.dialogue.character.push(this.$refs.characterName.value)
      this.$refs.characterName.value = ''
    },
    changeTextMessage(index){
      this.dialogue.messages[this.menu.messages.selected].text = $("#msg"+index).val()
    },
    selectCharForMessage(e){
      this.dialogue.messages[this.menu.messages.selected].character = e.target.value
    },
    deleteChar(index){ // Delete Character
      this.dialogue.character.splice(index,1)
    },
    addMessageBlock(){ // Add New Message Block
      let id = queen.makeid()
      this.dialogue.messages.push({id:id,character:"-",text:"",next:"",style:{top:100,left:100}})
    },
    deleteMessageBlock(index){
      this.dialogue.messages.splice(index,1)
      this.$forceUpdate()
    }
  },
  mounted(){
    this.draggable()
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
      z-index: 10;
  }
  .workarea{
    position: absolute;
    /* background: url('../assets/img/bg-grid.png'); */
    width: 100%;
    bottom: 0;
    top:0;
    z-index:-1;
    background-size: 100% 100%;
    overflow: scroll;
  }
  .block{
    position: absolute;
    max-width: 230px;
    cursor: all-scroll;
  }
  .block .title{
    color: #FFF;
    padding: 5px;
  }
  .block .body{
    padding: 3px;
    border: 1px solid #DDD;
    border-top:none;
    background: #FFF;
  }
  .block.character{
    max-width: 200px;
    margin: 80px 10px;
    z-index:9;
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
