<template>
  <div>
    <div class="container-fluid">
      <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <a class="navbar-brand ml-4" href="#">To-Do List</a>
        <div class="navbar-brand collapse navbar-collapse ml-5">
          <div class="input-group-prepend">
            <span class="input-group-text p-2" id="basic-addon1"
              > <b-icon icon="search"></b-icon></span>
          </div>
          <input type="text" class="form-control" placeholder="Search" />
        </div>
        <div class="collapse navbar-collapse" id="navbarText">
          <span class="navbar-text ml-auto">
            <button type="button" @click="insertList()" class="button">
              ADD LIST
            </button>
          </span>
        </div>
      </nav>
    </div>
    <div class="container-fluid">
      <div class="jumbotron bg-light">
        <div v-if="items.length == 0" class="center-text">
          No card available
        </div>
        <!-- cards -->
        <div v-if="items.length != 0" class="row"  >
          <div v-for="(item,index) in items" :key="index" >
          <div class="card-group ml-5 inline" style="height:33rem;width:40vh">
            <div class="card"  @drop='onDrop($event, index)' @dragover.prevent
      @dragenter.prevent >
              <div class="card-body bg-light top-border">
                <h5 class="card-title">{{item.title}} <b-icon icon="trash-fill" @click="deleteList(index)" class="float-right"></b-icon></h5> 
               <hr />
             
               <!--inner card -->
               <div class="row">
                 <div  v-for="(card,i) in item.cardList" :key="i">
              <div class="card mt-2" style="width:36vh"   draggable
                 @dragstart='startDrag($event, index,i,items)'   >
                  <div class="card-body p-2   bg-white">
                   <strong>{{card.title}}</strong> <b-icon icon="trash-fill" @click="deleteCard(index,i)" class="float-right"></b-icon><br> 
                   <hr class="mt-0 mb-0"/>
                  <p style="font-size:12px">fdssdfsdf  {{card.desc}}
                  </p>
                  </div>
              </div>
              </div>
               </div>
               </div>
              <!-- inner card-->
              <hr />
                <h6 class="text-center text-primary add-card" @click="addCardPopup(index)">+ Add Card</h6>
            </div>
          </div>
          </div>
        </div>
        <!-- cards -->
      </div>
    </div>
    <!-- card popup start-->
    <div
      v-if="toogleAdd"
      class="card popup"
      style="width: 33rem; height: 23rem; margin: auto"
    >
      <div class="modal-body">
        <h5 class="text-center">Add a Card</h5>
        <hr />
        <h6 class="ml-5 mt-4">Title</h6>
        <input v-model="title" class="form-control ml-5 mr-6" type="text" />
        <h6 class="ml-5 mt-4">Description</h6>
        <textarea
          v-model="description"
          class="form-control ml-5 mr-6"
        ></textarea>
        <div class="inline-flex ml-5 mt-5">
          <button class="ml-5 cancel" @click="cancelAdd()">Cancel</button
          ><button class="ml-5 add" @click="addCard()">Add</button>
        </div>
      </div>
    </div>
    <!-- card popup end-->
    <!-- List popup start-->
    <div
      v-if="toogleList"
      class="card popup"
      style="width: 33rem; height: 18rem; margin: auto"
    >
      <div class="modal-body">
        <h5 class="text-center">Add List</h5>
        <hr />
        <h6 class="ml-5 mt-4">Title</h6>
        <input v-model="listTitle" class="form-control ml-5 mr-6" type="text" />
        <div class="inline-flex ml-5 mt-5">
          <button class="ml-5 cancel" @click="cancelAdd()">Cancel</button
          ><button class="ml-5 add" @click="addList()">Add</button>
        </div>
      </div>
    </div>
    <!-- List popup end-->
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      items: [],
      toogleAdd: false,
      toogleList: false,
      title: "",
      description: "",
      listTitle: "",
      tmpCardIndex:0,
      tmpItem:null
    };
  },
  methods: {
    insertList() {
      this.toogleList = true;
    },
    cancelAdd() {
      this.toogleList = false;
      this.toogleAdd = false;
      this.listTitle = "";
      this.title = "";
      this.description = "";
    },
    addList() {
      let item ={
        title:this.listTitle,
        cardList:[]
      }
      this.items.push(item);
      this.toogleList = false;
      this.clearVal();
       this.saveData();
    },
    addCardPopup(index){
      this.toogleAdd= true;
      this.tmpCardIndex = index;
    },
    addCard(){
      let card={
        title:this.title,
        desc:this.description
      }
      this.items[this.tmpCardIndex].cardList.push(card);
      this.toogleAdd=false;
      this.clearVal();
      this.saveData();
    },
    deleteList(index){
        this.items.splice(index, 1);
        this.saveData();
    },
    deleteCard(index,i){
      this.items[index].cardList.splice(i,1);
      this.saveData();
    },
    clearVal(){
    this.title='';
    this.description='';
    this.listTitle='';
    },
    startDrag (evt, index,i) {
      this.tmpItem = this.items[index].cardList[i];
       evt.dataTransfer.setData('itemID',i)
       evt.dataTransfer.setData('i',index)
     // this.deleteCard(index,i);
},onDrop (evt, index) {
    const i = evt.dataTransfer.getData('itemID')
    const ii = evt.dataTransfer.getData('i')
     this.items[index].cardList.push(this.tmpItem);
     this.deleteCard(ii,i)
     this.saveData();
},saveData(){
        const parse = JSON.stringify(this.items);
        localStorage.setItem('item',parse);
}
  },mounted(){
    if(localStorage.getItem('item')){
      try{
    this.items = JSON.parse(localStorage.getItem('item'));
      }catch(e){
        localStorage.removeItem('item')
      }
    }
  }
};
</script>

<style scoped>
.button {
  width: 150px;
  height: 30px;
  background-color: #0074d9;
  color: white;
  border: 0 solid;
  text-align: center;
  border-radius: 5%;
}
.jumbotron {
  height: 86vh;
}
.center-text {
  position: relative;
  top: 48%;
  text-align: center;
  z-index: 1;
}
.popup {
  top: 25%;
  left: 32%;
  margin: auto;
  position: absolute;
  z-index: 2;
  display: flex;
}
.container {
  position: relative;
}
.form-control {
  width: 70%;
}
.add {
  width: 100px;
  height: 30px;
  background-color: #0074d9;
  color: white;
  border: 1 0 solid;
  text-align: center;
  border-radius: 5%;
}
.cancel {
  width: 100px;
  height: 30px;
  background-color: white;
  color: black;
  border: 1 0 solid;
  box-shadow: none;
  text-align: center;
  border-radius: 10%;
}
textarea {
  resize: none;
}
.top-border{
    border-top: 10px solid;
    border-color: blue;
    border-radius: 10px;
}
.add-card:hover{
  cursor: pointer;
}
</style>
