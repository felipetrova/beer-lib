<template>
  <a class="fixo button is-large is-danger is-loading" v-show="isLoading">Loading</a>
  <div class="container">
    <!-- <h1 class="title">{{title}}</h1> -->
    <div class="columns">
      <div class="column is-5">
        <p class="control has-addons">
          <input class="input is-expanded" type="text" placeholder="Procure pela cerveja" v-model="search">
          <a class="button is-info" @click.prevent="seers">Procurar</a>
        </p>
      </div>
      <div class="column is-6">
         
      </div>
      <div class="column is-1">
        <a class="button is-info" @click.prevent="neers">Nova</a>
      </div>
    </div>

    <div class="columns">
      <div class="column is-12">
        <table class="table is-narrow is-striped is-bordered">
          <thead>
            <tr>
              <th>Cerveja</th>
              <th>País</th>
              <th>Tipo</th>
              <th>Observação</th>
              <th>Mais</th>
              <th>Ações</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="beer in beers">
              <td>{{beer.name}} | {{beer.brewery}}</td>
              <td>{{beer.country}}</td>
              <td>{{beer.type}}</td>
              <td>{{beer.descript}}</td>
              <td class="is-icon">
                <a href="#">
                  <i class="fa fa-map-marker"></i>
                </a>
                <a href="#">
                  <i class="fa fa-plus-circle"></i>
                </a>
              </td>
              <td class="is-icon">

                <a href="#" @click.prevent="editBeer(beer)">
                  <i class="fa fa-edit"></i>
                </a>
                <a href="#" @click.prevent="removeBeer(beer)">
                  <i class="fa fa-trash"></i>
                </a>
              </td>
            </tr>
          </tbody>
        </table>
        <Pagination :total="total" :page="page" :itens-per-page="itensPerPage" @change-page="onChangePage"></Pagination>
      </div>
    </div>
  </div>

  <div id="modal_beer" class="modal" :class="{'is-active':showModal}">
    <div class="modal-background"></div>
    <div class="modal-card">
      <header class="modal-card-head">
        <p class="modal-card-title">Cerveja: {{selected.name}}</p>
        <button class="delete" @click.prevent="showModal=false"></button>
      </header>
      <section class="modal-card-body">

        <div class="columns">
          <div class="column">
            <label class="label">Cerveja</label>
            <p class="control">
              <input class="input" type="text" placeholder="Nome da Cerveja" v-model="selected.name">
            </p>
          </div>
          
          <div class="column">
            <label class="label">Cervejaria</label>
            <p class="control">
              <input class="input" type="text" placeholder="Cervejaria" v-model="selected.brewery">
            </p>
          </div>
          
          <div class="column">
            <label class="label">Código</label>
            <p class="control">
              <input class="input" type="text" placeholder="Código" v-model="selected.code">
            </p>
          </div>
        </div>

        <div class="columns">
          <div class="column">
            <label class="label">Tipo</label>
            <p class="control">
              <span class="select">
                <select v-model="selected.type">
                  <option>ALTBIER</option>
                  <option>AMERICAN BROWN ALE</option>
                  <option>AMERICAN LAGER</option>
                  <option>AMERICAN PALE ALE</option>
                  <option>BELGIAN BLOND ALE</option>
                  <option>BELGIAN PALE ALE</option>
                  <option>BOCK</option>
                  <option>DORTMUNDER EXPORT</option>
                  <option>DRY BEER</option>
                  <option>DUBBEL</option>
                  <option>DUNKEL</option>
                  <option>ENGLISH PALE ALE</option>
                  <option>ENGLISH BROWN ALE</option>
                  <option>ESB</option>
                  <option>FRUIT BEER</option>
                  <option>HEFEWEIZEN</option>
                  <option>HELLES</option>
                  <option>INDIA PALE ALE</option>
                  <option>KELLER</option>
                  <option>KÖLSCH</option>
                  <option>LITE</option>
                  <option>MARZEN</option>
                  <option>PILSNER</option>
                  <option>PREMIUM</option>
                  <option>RADLER</option>
                  <option>RED ALE</option>
                  <option>SAISON</option>
                  <option>STOUT</option>
                  <option>TRIPEL</option>
                  <option>VIENNA</option>
                  <option>WEISSBIER</option>
                  <option>WITBIER</option>
                </select>
              </span>
            </p>
          </div>

          <div class="column">
            <label class="label">Site</label>
            <p class="control">
              <input class="input" type="text" placeholder="Site/Facebook" v-model="selected.website">
            </p>
          </div>

          <div class="column">
            <label class="label">País</label>
            <p class="control">
              <input class="input" type="text" placeholder="País" v-model="selected.country">
            </p>
          </div>
        </div>

        <div class="columns">
          <div class="column">
            <label class="label">Descrição</label>
            <p class="control">
              <textarea class="textarea" placeholder="Observações" v-model="selected.descript"></textarea>
            </p>
          </div>
        </div>

      </section>
      <footer class="modal-card-foot">
        <a class="button is-primary" @click.prevent="saveBeer">Salvar</a>
        <a class="button" @click.prevent="showModal=false">Cancelar</a>
      </footer>
    </div>
  </div>
</template>

<script>
  import Pagination from './Pagination.vue'

  export default {
    data () {
      return {
        isLoading: false,
        title: 'Beer Lib',
        search: '',
        beers: [],
        page: 1,
        total: 0,
        selected: {},
        itensPerPage: 10,
        showModal:false
      }
    },
    components: {
      Pagination
    },
    methods: {
      onChangePage(page){
        this.page = page
        this.leers()
      },
      showLoading(){
        this.isLoading=true;
      },
      hideLoading(){
        this.isLoading=false;
      },
      leers(){

        let t = this
        this.showLoading()

        let start = (this.page * this.itensPerPage) - (this.itensPerPage)
        let end = this.page * this.itensPerPage
        let qString = "";

        if (this.search){
          qString = `&q=${this.search}`
        }

        this.$http.get(`/beers?_start=${start}&_end=${end}${qString}`).then(
         response=>{
           // t.beers = response.json()
           // t.total = response.headers['X-Total-Count']
           t.beers = response.body;
           t.total = response.headers.get('X-Total-Count');
         },
         error=>{
           console.log(error)
         }).finally(function () {
          t.hideLoading();
        })

      },
      seers(){
        this.leers()
      },
      neers(){
        this.selected={}
        this.showModal = true;
      },
      editBeer(beer){
        this.selected=beer
        this.showModal = true;
      },
       removeBeer(beer){
        let self = this;
        swal({
          title: "Você tem certeza?",
          text: `Deseja apagar "${beer.name}"`,   
          type: "warning",   
          showCancelButton: true,   
          confirmButtonColor: "#DD6B55",   
          cancelButtonText: "Cancelar",
          confirmButtonText: "Sim, pode apagar!", 
          showLoaderOnConfirm: true,  
          closeOnConfirm: false }, function(){   

            self.$http.delete(`/beers/${beer.id}`).then(
              result=>{
                swal("Cervejaria removida!")
                self.leers()
              })
        });

       },
       saveBeer(){
        if (this.selected.id!=null){  //EDIT
          this.$http.put(`/beers/${this.selected.id}`,this.selected).then(
            response=>{
              this.$set('selected',{})
              this.$set('showModal',false)
            },
            error=>{
              console.error(error)
            }
            ).finally(
              this.leers()
            )
          }
          else
          { //NEW
            this.$http.post(`/beers`,this.selected).then(
            response=>{
              this.$set('selected',{})
              this.$set('showModal',false)
            },
            error=>{
              console.error(error)
            }
            ).finally(
              this.leers()
            )
          }
       }
     },
     created(){
      this.leers();
    },
  }
</script>
<style>
  .fixo{float: right; margin-right: 10px; margin-top: 0px; z-index: 1000;}
</style>